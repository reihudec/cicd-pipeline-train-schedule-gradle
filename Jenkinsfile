pipeline {
    stages {
        stage('Build') {
            steps {
                echo 'Building the train schedule application..'
                sh ./gradlew build
                archiveArtifacts artifacts: 'dist/rainSchedule.zip'
            }
        }
    }
}
