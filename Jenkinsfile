pipeline {
    agent any

    stages {

        stage("Checkout source"){
            steps {
                git url:'https://github.com/joaochavesjr/pedelogo-catalogo.git', branch:'main'
            }
        }

        stage("Build Image"){
            steps {
                script {
                    dockerapp = docker.build("joaochavesjr/api-produto:${env.BUILD_ID}",
                        '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Push Image'){
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.puhs("${env.BUILD_ID}")
                    }
                }                
            }
        }
    }
}