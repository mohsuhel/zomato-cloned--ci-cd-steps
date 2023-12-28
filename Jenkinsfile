pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'nodejs16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/Aj7Ay/Zomato-Clone.git'
            }
        }
        stage("Sonarqube Analysis ") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=zomato \
                        -Dsonar.projectKey=zomato '''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
        stage("Docker Build & Push") {
            steps {
                script {
                    sh "docker build -t mdsuh/zomato:latest ."
                    withCredentials([string(credentialsId: 'dockerHub', variable: 'dockerHub')]) {
                        sh 'docker login -u mdsuh -p $dockerHub'
                        sh "docker push mdsuh/zomato:latest "
                    }
                }
            }
        }
        stage("TRIVY") {
            steps {
                sh "trivy image mdsuh/zomato:latest > trivy.txt"
            }
        }
        stage('Deploy to container'){
          steps{
            sh 'docker run -d --name zomato -p 3000:3000 mdsuh/zomato:latest'
          }
      }
    }
}

