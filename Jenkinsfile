pipeline {
    agent any
    tools{
        nodejs 'nodejs16'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git_Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/anoopkmathew4u/fullstack-bank.git'
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
         stage('Trivy Scan') {
            steps {
                sh "trivy fs ."
            }
        }
         stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Bank -Dsonar.projectKey=Bank "
               }
            }
        }
       stage('Backend') {
            steps {
                sh "cd /var/lib/jenkins/workspace/Bank-App/app/frontend"
                    sh "npm install"
                }
        }
        
        stage('frontend') {
            steps {
                   sh "cd /var/lib/jenkins/workspace/Bank-App/app/backend"
                    sh "npm install"
                }
        }
        stage('Deploy to Conatiner') {
            steps {
                sh "cd /var/lib/jenkins/workspace/Bank-App/app"
                sh "npm run compose:up -d"
            }
        }
    }
}
