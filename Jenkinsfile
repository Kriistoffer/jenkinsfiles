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

                    env.REPOSITORIES.tokenize(',').each { repository ->
                        dir("${repository}") {
                            try {
                                git branch: "${env.BRANCH}", credentialsId: "github-credentials", url: "${env.BASE_URL}/${repository}.git" 
                            } catch (error) {
                                echo "Failed to clone the repository due to ${error}"
                                echo "Defaulting to the master branch..."
                                git branch: "master", credentialsId: "github-credentials", url: "${env.BASE_URL}/${repository}.git"
                            }
                        }
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
                            sh "${env.PACKETMANAGER} install"
                            sh "${env.PACKETMANAGER} audit --json > ${WORKSPACE}/logs/${parentDirectory.replaceAll('/', '_')}_audit.json"
                        }
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {

                }
            }
        }
    }
}