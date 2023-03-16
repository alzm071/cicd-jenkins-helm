pipeline {
    agent any
    stages {
        stage ("Build Maven") {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }
        stage ('Copy Artifacts') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
        stage ('Build docker image') {
            steps {
                script {
                    def customImage = docker.build('alzaar07/petclinic',"./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    customImage.push('latest')
                    }
                }
            }
        }
        stage ('Build on Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'pwd'
                    sh 'cp -R helm/* .'
                    sh 'ls -ltrh'
                    sh 'pwd'
                    sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=ismailshaikh/petclinic --set image.tag=latest'
                }

            }
        }
    }
}
