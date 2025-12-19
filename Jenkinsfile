pipeline {

    environment {
        REPO = 'https://github.com/Heropon33/petclinic.git'
        BRANCH = 'master'
        ANSIBLE_HOST_KEY_CHECKING = 'False'
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
        
        /* stage('Checkout') {
            steps {
                git branch: "${BRANCH}",
                    url: "${REPO}",
                    credentialsId: 'github-token'
            }
        } */

       /* stage('Suppression du war'){
            steps {
                sh 'rm -f target/petclinic.war'
            }
        } 

        stage('Compilation') {
           steps {
                sh './mvnw package'
            }
        }
*/
        stage('Verification de la présence du war'){
            steps {
                sh 'ls -l target/petclinic.war'
            }
        }
        
/*
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

                      git add target/petclinic.war
                      git commit -m "Jenkins: Ajout du War petclinic.war [ci skip]" || echo "Nothing to commit"

                      git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/Heropon33/petclinic.git
                      git push origin ${BRANCH}
                    '''
                }
            }
        }
*/
        stage('Deployer sur les webservers via ansible') {
            steps {
                    sh '''
                      ssh ansible@159.8.76.124 << 'EOF'
                        cd /home/ansible/trelaze-ansible/
                        git pull
                        run-ansible.sh \
                          -i inventory \
                          playbooks/ans_play_install_petclinic.yml
                      EOF
                    '''
            }
        }
    }
}
