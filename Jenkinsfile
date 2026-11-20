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
        ACCOUNT_ID = "921040036993"
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
                   def packageJSON = readJSON file: 'package.json'
                   appVersion = packageJSON.version
                   echo "appVersion is ${appVersion}"
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
        
        stage('Unit Test'){
            steps{
                script{
                    sh """
                        npm test 
                    """
                }
            }
        }

        stage('Sonar Scan'){
            environment{
                    def scannerHome = tool 'sonar-8.0'
                }
            steps{
                script{
                    withSonarQubeEnv('sonar-server') {
                        sh "${scannerHome}/bin/sonar-scanner"
                     }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Prevents the pipeline from hanging if the webhook fails
                timeout(time: 1, unit: 'HOURS') {
                    // Pauses until SonarQube sends the webhook payload
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Image'){
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

        
        stage('Trivy Scan'){
            steps{
                script{
                    sh """
                        trivy image \
                        --scanners vuln \
                        --severity HIGH,CRITICAL,MEDIUM \
                        --pkg-types os \
                        --exit-code 1 \
                        --format table \
                        ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                    """
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
