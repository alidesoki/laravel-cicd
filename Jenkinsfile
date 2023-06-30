pipeline {
    agent any
    stages {
        stage("verify tooling") {
            steps {
                sh '''
                docker info
                docker version
                docker compose version
                '''
            }
        }
        stage ("clear all running docker containers") {
            steps {
                script {
                    try {
                        sh 'docker rm -f $(docker ps -aq)'
                    }
                    catch (Exception e) {
                        echo "No running container to clear up"
                    }
                }
            }
        }
        stage ("start docker") {
            steps {
                sh "make up"
                sh "docker compose ps"
            }
        }
        stage ("run composer install") {
            steps {
                sh 'docker compose run --rm composer install'
            }
        }
        stage ("Populate .env file"){
            steps {
                dir("/var/lib/jenkins/workspace/envs/laravel-test") {
                    fileOperations([fileCopyOperation(excludes: '', flattenFiles: true, includes: '.env', targetLocation: "${WORKSPACE}")])
                }
            }
        }
        stage ("Run Tests") {
            steps {
                sh 'docker compose run --rm artisan test'
            }
        }
    }
    post {
        always {
            sh 'docker compose down --remove-orphans -v'
            sh 'docker compose ps'
        }
    }
}