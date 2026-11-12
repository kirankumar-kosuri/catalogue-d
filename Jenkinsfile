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
                    sh """
                        docker build -t catalogue:${appVersion} .
                        docker images
                    """
                }
            }
        }
        stage('Deploy'){
            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "alice,bob"
            //     parameters {
            //         string(name: 'PERSON', defaultValue: 'Mr Kiran', description: 'Who should I say hello to?')
            //     }
            // }
            // when{
            //     expression { "$params.DEPLOY" == "true" }

            // }
            steps{
                script{
                    sh """
                        echo "Deploying"
                    """
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