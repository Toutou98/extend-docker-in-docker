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
        DOCKER_IMAGE_BASE = "jdk-base-image:1.0.0"
        DOCKER_REGISTRY = "http://nexus-docker.nexus.svc.cluster.local:8083"
        DOCKER_REGISTRY_DOMAIN = "nexus-docker.nexus.svc.cluster.local:8083"
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    dockerActions.buildDockerImage('Dockerfile.base', env.DOCKER_IMAGE_BASE, env.DOCKER_REGISTRY_DOMAIN)
                }
            }
        }
        // stage('Build Base Docker Image') {
        //     steps {
        //         container('docker') {
        //             script {
        //                 // Build the base Docker image from the Dockerfile in the repo
        //                 sh "docker build -f Dockerfile.base -t ${DOCKER_IMAGE_BASE} ."
                        
        //                 // Authenticate with Nexus Docker registry
        //                 withCredentials([usernamePassword(credentialsId: 'nexus', usernameVariable: 'NEXUS_USERNAME', passwordVariable: 'NEXUS_PASSWORD')]) {
        //                     sh "echo $NEXUS_PASSWORD | docker login ${DOCKER_REGISTRY} -u $NEXUS_USERNAME --password-stdin"
        //                     sh "docker tag ${DOCKER_IMAGE_BASE} ${DOCKER_REGISTRY_DOMAIN}/${DOCKER_IMAGE_BASE}"
        //                     sh "docker push ${DOCKER_REGISTRY_DOMAIN}/${DOCKER_IMAGE_BASE}"
        //                 }
        //             }
        //         }
        //     }
        // }
    }
    post {
        always {
            cleanWs()
        }
    }
}
