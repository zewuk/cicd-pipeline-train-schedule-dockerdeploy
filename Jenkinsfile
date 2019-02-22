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
                    withDockerRegistry(credentialsId: 'docker_hub_login', url: 'https://registry.hub.docker.com') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
 
        }

        stage(DeployToproduction){
            when {
                branch 'master'
            }

            steps {

                input 'Deploy to Prodcution'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                    script{
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull zewuk/train-schedule:${env.BUILD_NUMBER}\"" 
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop zewuk/train-schedule\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm zewuk/train-schedule\""
                        }
                        
                        catch (err) {
                            echo: 'caught error: $err'
                        }
                    }
                }
            }
        }
    }
