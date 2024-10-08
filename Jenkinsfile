@Library("jenkins-shared@main") _ 

pipeline {
    agent {
        kubernetes {
            yaml """
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: docker
                image: docker:24.0.2-dind
                command:
                - dockerd
                - --host=unix:///var/run/docker.sock
                - --insecure-registry=nexus-docker.nexus.svc.cluster.local:8083
                securityContext:
                  privileged: true
            """
        }
    }
    environment {
        DOCKER_IMAGE_BASE_JDK = "jdk-base-image:1.0.0"
        DOCKER_IMAGE_BASE_KUBEHELM = "kubehelm:1.0.0"
        DOCKER_REGISTRY = "http://nexus-docker.nexus.svc.cluster.local:8083"
        DOCKER_REGISTRY_DOMAIN = "nexus-docker.nexus.svc.cluster.local:8083"
    }
    stages {
        stage('Build Docker Image For Quarkus App') {
            steps {
                container('docker') {
                    script {
                        dockerActions.buildDockerImage('Dockerfile.base', env.DOCKER_IMAGE_BASE_JDK, env.DOCKER_REGISTRY_DOMAIN)
                    }
                }    
            }
        }
        stage('Build KubeHelm Docker Image') {
            steps {
                container('docker') {    
                    script {
                        dockerActions.buildDockerImage('Dockerfile.kubehelm', env.DOCKER_IMAGE_BASE_KUBEHELM, env.DOCKER_REGISTRY_DOMAIN)
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
