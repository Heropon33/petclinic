pipeline {
    agent {
        label 'java'
    }
    stages {
        stage('version') {
            steps {
                sh 'java --version'
            }
        }

        stage('Compilation') {
            steps {
                sh './mvnw package'
            }
        }

        stage('Verification de la présence du war')
          steps {
                sh 'ls -l target/petclinic.war'
            }
        }
    }
}
