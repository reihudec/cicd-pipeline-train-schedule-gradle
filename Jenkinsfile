pipeline {
    stages {
        stage('Build') {
            steps {
                echo 'Building the train schedule application..'
                sh ./gradlew build --no-daemon
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}
