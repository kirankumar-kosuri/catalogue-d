// pre build 
pipeline{
    agent{
        node{
            label "Agent-1"
        }
    }

// build
    environment{
        COURSE = "Jenkins-CICD"
        appVersion = ""
        ACC_ID = "103295384824"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }
    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }

    stages{
        stage('Read Version'){
            steps{
                script{
                    def packageJson = readJSON file: "package.json"
                    appVersion = packageJson.version
                    echo "appVersion is: ${appVersion}"
                }
            }
        }
        stage('Install Dependencies'){
            steps{
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Building Image') {
    steps {
        script {
            withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                sh """
                    # Login to AWS ECR
                    aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com

                    # Build Docker Image
                    docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .

                    # List Docker Images
                    docker images

                    # Push Image to ECR
                    docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                """
                    }
                }
            }
        }
    }
// post build
    post{
        always{
            echo 'I will always say Hello again!'
            cleanWs()
        }
        success{
            echo "I WILL RUN IF SUCCESS"
        }
        failure{
            echo "I WILL RUN IF FAILURE"
        }
        aborted{
            echo "Pipeline is Aborted please Check"
        }
    }
}