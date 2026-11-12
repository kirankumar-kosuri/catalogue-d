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
        ACC_ID = "103295384824"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }

    options {
        // timeout(time: 10, unit: 'SECONDS') 
        disableConcurrentBuilds()
    }

// build stage
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
        stage('Build Image'){
            steps{
                script{
                    withAWS(region:"us-east-1",credentials:"aws-creds") {
                        sh """
                                // Step 1: Authenticate Docker with AWS ECR
                                // Retrieves a temporary login password from AWS ECR
                                // and uses it to log in to the ECR Docker registry.
                                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com

                                // Step 2: Build the Docker image
                                // Builds a Docker image using the Dockerfile in the current directory.
                                // Assigns the ECR repository URL, project, component, and application version as the image tag.
                                docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .

                                // Step 3: Verify Docker images
                                // Displays all locally available Docker images.
                                // Helps verify that the image was successfully created.
                                docker images

                                // Step 4: Push the Docker image to AWS ECR
                                // Uploads the newly built Docker image to the specified AWS ECR repository.
                                // Makes the image available for deployment.
                                docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                            """
                        // sh """

                        //     aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com

                        //     docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                       
                        //     docker images

                        //     docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} 

                        // """
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
        // aborted{
        //     echo "Pipeline is Aborted please Check It"
        // }
    }
}