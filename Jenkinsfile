pipeline {
    environment {
        CredDocker = credentials('DOCKERHUB')
    }
    agent none
    stages {
        stage("En contenedor") {
            agent {
                docker { image "python:3"
                args '-u root:root'
                }
            }
            stages {
                stage('Clone') {
                    steps {
                        git branch:'main',url:'https://github.com/angelsuarez1612/djangotutorialtest.git'
                    }
                }
                stage('Install') {
                    steps {
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Test')
                {
                    steps {
                        sh 'python3 manage.py test'
                    }
                }

            }
        }
        stage("En maquina") {
            agent any
            stages {
                stage('Clonar') {
                    steps {
                        git branch:'main',url:'https://github.com/angelsuarez1612/djangotutorialtest.git'
                    }
                }
                stage('Construir y subir imagen') {
                    steps {
                        script {
                            docker.withRegistry( '', CredDocker ) {
                                def imagen = docker.build('angelsuarez1612/icdjango')
                            }
                        }
                    }
                }
                stage('EliminarImg') {
                    steps {
                        sh "docker rmi angelsuarez1612/icdjango"
                    }
                }
            }
        }           
    }
    post {
        always {
            mail to: 'angelsp1612@gmail.com',
            subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} has result ${currentBuild.result}"
        }
    }
}
