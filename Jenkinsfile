pipeline {
    agent any

    environment {
        registry = "artibhoir369/springboot-helm-minikube"
        imageName = "spring-boot-web"  // Define the image name
        dockerHubCredentialsId = "34c63854-b5d3-4cbf-b647-20147c56cdc9" // Docker Hub credentials ID
        helmChartPath = "helm/springboot" // Path to your Helm chart
        helmReleaseName = "springboot-release" // Helm release name
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/artibhoir369/jenkins-helmchart-k8-springboot']])
            }
        }

        stage('Build JAR') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageTag = "${env.registry}:${env.BUILD_ID}"
                    docker.build(imageTag, "-f Dockerfile .")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: dockerHubCredentialsId, usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh "echo ${DOCKER_HUB_PASSWORD} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin"
                    }
                    def imageTag = "${env.registry}:${env.BUILD_ID}"
                    sh "docker push ${imageTag}"
                }
            }
        }

    //     stage('Helm Package') {
    //         steps {
    //             script {
    //                 sh "helm package ${helmChartPath}"
    //             }
    //         }
    //     }

    //     stage('Helm Install/Upgrade') {
    //         steps {
    //             script {
    //                 def chartFile = sh(script: "ls ${helmChartPath}/*.tgz", returnStdout: true).trim()
    //                 sh "helm upgrade --install ${helmReleaseName} ${chartFile} --namespace default"
    //             }
    //         }
    //     }
    // }
    
    // post {
    //     always {
    //         // Clean up Docker images to free up space
    //         sh "docker system prune -af"
    //     }
    }
}
