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

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }                
            }
        }

        stage('Deploy k8s'){
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                script {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api/deployment.yaml'
                    sh 'cat ./k8s/api/deployment.yaml'
                    kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 
'kubeconfig')
                }

            }

        }

    }
}
