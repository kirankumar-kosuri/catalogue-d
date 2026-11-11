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
        stage('Building Image'){
            steps{
                script{
                    sh """
                        docker build -t catalogue:${appVersion} .
                        docker images
                    """
                }
            }
        }
        stage(Deploy){
            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "Kiran"
            //     parameters {
            //         string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
            //     }
            // }
            when{
                expression { "$params.DEPLOY" == "true"}
            }
            steps{
                script{
                    sh """
                        echo "Deploying"
                    """
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