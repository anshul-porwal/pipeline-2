pipeline {
    agent any
    environment {
        DISABLE_AUTH = 'true'
    }
    stages {
        stage('Build') {
            steps {
                echo env.DISABLE_AUTH
            }
        }
    }
}
