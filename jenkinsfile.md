## Jenkins Pipeline for Building and Deploying a Java Application with Docker

```groovy
pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/EmAdd9/Shopping-Cart.git'
            }
        }
        stage('Mvn Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://13.235.252.22:9000/ -Dsonar.login=squ_81bbe5296c4b2f58d5d41c71e090bcfab5a5f9d3 -Dsonar.projectName=Shopping-Cart \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Shopping-Cart
                    '''
                }
            }
        }
        stage("Build") {
            steps {
                sh "mvn clean package -DskipTests=true "
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
   
        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'c9b058e5-bfe6-41f8-9b5d-dc0b0d2955ac', toolName: 'docker') {
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag shopping-cart sudebdocker/shopping-cart:latest"
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'c9b058e5-bfe6-41f8-9b5d-dc0b0d2955ac', toolName: 'docker') {
                        sh "docker push sudebdocker/shopping-cart:latest"
                    }
                }
            }
        }
        
        stage('Deploy To Docker Container') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'c9b058e5-bfe6-41f8-9b5d-dc0b0d2955ac', toolName: 'docker') {
                        sh "docker run -d --name shopping -p 8070:8070 sudebdocker/shopping-cart:latest"
                    }
                }
            }
        }
    }
}
```
This Jenkins pipeline script automates the build, testing, and deployment of a Java application using Docker. It includes stages for checking out code, compiling, analyzing with SonarQube, performing OWASP Dependency Check, building a Docker image, pushing the image to a Docker registry, and deploying it to a Docker container.

Please adjust the formatting as needed in your markdown documentation.
