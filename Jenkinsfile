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
    }
    stages('Build Image'){
        when {
            branch 'master'   
        }
        steps {
            script{
                image = docker.build($DOCKER_IMAGE) 
                image.inside{
                    sh 'echo Its works!'   
                }
            }
        }
    }
}
