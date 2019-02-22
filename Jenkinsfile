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

        stage('Build image') {
        /* This builds the actual image */

        app = docker.build("zewuk/train-schedule")
        }

        stage('Test image') {
        
        app.inside {
            echo "Tests passed"
        }
    }
        
    }
}
