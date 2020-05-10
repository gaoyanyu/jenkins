pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
spec:
  nodeSelector:
    openstack-control-plane: enabled
  containers:
  - name: jnlp
    image: hub.easystack.io/arm64v8/jnlp-slave:3.27-1
  - name: docker
    image: hub.easystack.io/arm64v8/docker:19.03.1
    imagePullPolicy: IfNotPresent
    command:
    - sleep
    args:
    - 99d
    tty: true
    env:
      - name: DOCKER_HOST
        value: tcp://localhost:2375
    volumeMounts:
      - name: docker-config
        mountPath: /root/Dockerfile
        subPath: Dockerfile
  - name: docker-daemon
    image: hub.easystack.io/arm64v8/docker:dind
    imagePullPolicy: IfNotPresent
    tty: true
    securityContext:
      privileged: true
    env:
      - name: DOCKER_TLS_CERTDIR
        value: ""
      - name: JENKINS_HARBOR_USER
        valueFrom:
          configMapKeyRef:
            name: jenkins-harbor
            key: HARBOR_USER
      - name: JENKINS_HARBOR_PASSWD
        valueFrom:
          configMapKeyRef:
            name: jenkins-harbor
            key: HARBOR_PASSWD
    volumeMounts:
      - name: dind-storage
        mountPath: /var/lib/docker
      - name: docker-config
        mountPath: /etc/docker/daemon.json
        subPath: daemon.json
  volumes:
    - name: dind-storage
      emptyDir: {}
    - name: docker-config
      configMap:
        name: jenkins-harbor
"""
    }
  }
  stages {
    stage('login to harbor') {
      steps {
        container('docker-daemon') {
          sh 'sleep 60'
          sh 'cd /root/ && docker login hub.easystack.io -u ${JENKINS_HARBOR_USER} -p ${JENKINS_HARBOR_PASSWD}'
        }
      }
    }
    stage('Build docker image') {
      steps {
        container('docker') {
          sh 'cd /root/ && sleep 60'
          sh 'cd /home/jenkins/agent/workspace/test_master && docker build -t hub.easystack.io/production/testing-docker-in-docker:latest .'
        }
        container('docker-daemon') {
          sh 'sleep 10'
          sh 'cd /root/ && docker images'
          sh 'docker push hub.easystack.io/production/testing-docker-in-docker:latest'
        }
      }
    }
  }
}
