pipeline {
    agent {
        node {
            label 'roboshop'
        }
    }
    environment {
        appVersion = ""
        region = "us-east-1"
        ACC_ID = "665096241917" 
    }
    stages {
        stage ('Read Version') {
            steps {
                script {
                    // Read and parse the package.json file
                    def packageJson = readJSON file: 'package.json'
                    
                    // Access specific fields
                    appVersion = packageJson.version
                    echo "Building version ${appVersion}"
                }
            }

        }
        stage ('Install dependencies') {
            steps {
                script {
                    sh """
                        npm install
                    """
                }
            }
        }    
        stage ('Build Image') {
            steps {
               script{
                    withAWS(credentials: 'aws-creds', region: "${region}") {
                        // Commands here have AWS authentication
                        sh """
                            # Login to ECR
                            aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.${region}.amazonaws.com

                            # Build Docker image
                            docker build -t ${ACC_ID}.dkr.ecr.${region}.amazonaws.com/roboshop/catalogue:${env.appVersion} .

                            # Push image
                            docker push ${ACC_ID}.dkr.ecr.${region}.amazonaws.com/roboshop/catalogue:${env.appVersion}
                        """
                    }
                }
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            cleanWs()
        }
        success {
            echo "pipeline success"
        }
        failure {
            echo "pipeline failure"
        }
    }
} 