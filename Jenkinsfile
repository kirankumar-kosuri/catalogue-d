// this are pre-build section
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
    // this is build section
    stages{
        stage('Read Version'){
            steps{
                script{

                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "app version is: ${appVersion}"

                }
            }
        }
        stage('Install Dependences'){
            steps{
                script{
                    sh """
                         npm install
                    """
                }
            }
        }
        stage('Deploy'){

            // when{
            //     expression  = { "$params.DEPLOY" == "true" }
            // }

            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "Kiran"
            //     parameters {
            //         string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
            //     }
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

    post{
        always{
            echo 'I will always say Hello again!'
            cleanWs()
        }

        success{
            echo "I will run if SUCCESS"
        }

        failure{
            echo "I will run if FAILURE"
        }

        // aborted{
        //     echo "Pipeline is Aborrted please Check"
        // }
    }
}