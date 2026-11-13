// pre-build stage
pipeline{
    agent{
        node{
            label "Agent-1"
        }
    }

    environment{
        COURSE = "Jenkins"
        appVersion = ""
        ACCOUNT_ID = "103295384824"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }

    options {
        timeout(time: 10, unit: 'SECONDS') 
        disableConcurrentBuilds()
    }

// build stage
    stages{
        stage('Read Version'){
            steps{
                script{
                    def packageJSON = readJSON file: "package.json"
                    appVersion = packageJSON.version
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
        stage('Build Docker Image'){
            steps{
                script{
                       withAWS(region:'us-east-1',credentials:'aws-creds') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com

                            docker build -t ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .

                            docker images

                            docker push ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} 

                        """
                    }
                }
            }
        }
    }

// post build stage
    post{
        always{
            echo "I will always say Hello again!"
            cleanWs()
        }
        success{
            echo "I will run if success"
        }
        failure{
            echo "I will run if failure"
        }
    }
}