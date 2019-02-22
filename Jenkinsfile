pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }

        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build("zewuk/train-schedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }


        stage('Push to docker Registry'){
            steps{

                script {

                               // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'docker_hub_login', url: 'https://registry.hub.docker.io') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
 
        }

        
    }
}
