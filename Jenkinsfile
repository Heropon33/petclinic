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
        
         stage('Checkout') {
            steps {
                git branch: "${BRANCH}",
                    url: "${REPO}",
                    credentialsId: 'github-token'
            }
        }

       stage('Suppression du war'){
            steps {
                sh 'rm -f target/petclinic.war'
            }
        } 

        stage('Compilation') {
           steps {
                sh './mvnw package'
            }
        }

        stage('Verification de la présence du war'){
            steps {
                sh 'ls -l target/petclinic.war'
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

                      git add target/petclinic.war
                      git commit -m "Jenkins: Ajout du War petclinic.war [ci skip]" || echo "Nothing to commit"

                      git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/Heropon33/petclinic.git
                      git push origin ${BRANCH}
                    '''
                }
            }
        }
 
        stage('Deployer sur les webservers via ansible') {
            steps {
                sh """
                    ssh ansible@159.8.76.124 \
                    'cd /home/ansible/trelaze-ansible/ && \
                    git pull && \
                    ./run-ansible.sh playbooks/ans_play_install_petclinic.yml -i inventory'   
                """
            }
        }

        stage('Check application') {
            steps {
                sh '''
                sleep 20
                URL="http://13.48.28.53:8080/petclinic/"
                KEYWORD="Welcome"

                echo "Checking $URL for keyword: $KEYWORD"
                for i in {1..10}; do
                    HTTP_CODE=$(curl -s -o /tmp/page.html -w "%{http_code}" "$URL")

                    if [ "$HTTP_CODE" = "200" ] && grep -q "$KEYWORD" /tmp/page.html; then
                        echo "Application OK"
                        rm /tmp/page.html
                        exit 0
                    fi

                    echo "Not ready yet (try $i)"
                    sleep 5
                done

                rm /tmp/page.html
                echo "ERROR: keyword '$KEYWORD' not found"
                exit 1
                '''
            }
        }
    }
}
