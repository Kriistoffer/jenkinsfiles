pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage("Setup") {
            steps {
                script {
                    echo "Cleaning workspace..."
                    cleanWs()

                    echo "Checking out source code..."
                    checkout scm

                    if (!fileExists('logs')) {
                        echo "Setting up a log directory..."
                        sh 'mkdir logs'
                    }


                    try {
                        sh "git clone https://github.com/Kriistoffer/jenkins-frontend-1.git"
                    } catch (error) {
                        echo "Failed to clone the repository due to ${error}"
                    }
                }
            }
        }
        stage('Scan') {
            steps {
                script {
                    def files = findFiles(glob: "**/package.json", excludes: "${env.NODE_EXCLUDE_LIST}")

                    for (file in files) {
                        def parentDirectory = "${file.path}" - "/${file.name}"

                        dir("${parentDirectory}") {
                            sh "npm install"
                            sh "npm audit --json > ${WORKSPACE}/logs/${parentDirectory.replaceAll('/', '_')}_audit.json"
                        }
                    }
                }
            }
        }
        // stage('Deploy') {
        //     steps {
        //         script {

        //         }
        //     }
        // }
    }
}