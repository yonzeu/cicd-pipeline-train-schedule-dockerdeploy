pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'paungui/train-schedule'   
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Image'){
            when {
                branch 'master'   
            }
            steps {
                script{
                    image = docker.build('paungui/train-schedule') 
                    image.inside{
                        sh 'echo Its works!'   
                    }
                }
            }
        }
        stage('Push to Docker Hub'){
            when {
                branch 'master'   
            }
            steps {
                srcipt{
                    docker.withRegistry('registry.hub.docker.com','	docker_hub_credential'){
                        image.push("${env.BUILD_NUMBER}")
                        image.push("latest")
                    }
                }
            }
        }
    }
}
