pipeline {

    environment {
        REPO = 'https://github.com/Heropon33/petclinic.git'
        BRANCH = 'master'
    }

    agent {
        label 'java'
    }
    stages {
        stage('version') {
            steps {
                sh 'java --version'
            }
        }

        stage('Checkout') {
            steps {
                git branch: "${BRANCH}",
                    url: "${REPO}",
                    credentialsId: 'github-token'
            }
        }

        stage('Modify file') {
            steps {
                sh '''
                  echo "build=1" > version.txt
                '''
            }
        }

        stage('Commit & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-token',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_TOKEN'
                )]) {
                    sh '''
                      git config user.name  "Jenkins CI"
                      git config user.email "jenkins@ci.local"

                      git add version.txt
                      git commit -m "chore: update version [ci skip]" || echo "Nothing to commit"

                      git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/Heropon33/petclinic.git
                      git push origin ${BRANCH}
                    '''
                }
            }
        }

        // stage('Compilation') {
           // steps {
                // sh './mvnw package'
           // }
        // }

       // stage('Verification de la présence du war'){
         // steps {
           //     sh 'ls -l target/petclinic.war'
            // }
       // }
    }
}
