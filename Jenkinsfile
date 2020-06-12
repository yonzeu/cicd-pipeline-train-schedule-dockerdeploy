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
                script{
                    docker.withRegistry('https://registry.hub.docker.com','docker_hub_credential'){
                        image.push("${env.BUILD_NUMBER}")
                        image.push("latest")
                    }
                }
            }
        }
        stage('Deploy to Production'){
            when {
                branch 'master'   
            }
            steps {
                input('Deploy to proction?')
                milestones(1)
                withCredentials([usernamePassword(credentialsId: 'prod_credential',usernameVariable: 'USERNAME',passwordVariable: 'PASSWORD')]){
                    script{
                        sh "sshpass -p "$PASSWORD" -v ssh -o StrictHostKeyChecking=no $USERNAME@$PROD_IP \"docker pull paungui/train-schedule:${env.BUILD_NUMBER}\""
                        try{
                            sh "sshpass -p "$PASSWORD" -v ssh -o StrictHostKeyChecking=no $USERNAME@$PROD_IP \"docker stop train-schedule\""
                            sh "sshpass -p "$PASSWORD" -v ssh -o StrictHostKeyChecking=no $USERNAME@$PROD_IP \"docker rm train-schedule\""   
                        }catch(err){
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p "$PASSWORD" -v ssh -o StrictHostKeyChecking=no $USERNAME@$PROD_IP \"docker run -d -p 8080:8080 --restart always --name train-schedule paungui/train-schedule:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
    }
}
