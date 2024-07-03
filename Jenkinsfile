pipeline{
    agent any
    tools{
        nodejs 'node18'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages{
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/mukeshr-29/project-jenkins-zomatoClone.git'
            }
        }
        stage('install dependencies'){
            steps{
                sh 'npm install'
            }
        }
        stage('file system scanning'){
            steps{
                sh 'trivy fs --format table -o trivyfs-report.html .'
            }
        }
        stage('static code analysis'){
            steps{
                script{
                    withSonarQubeEnv('sonar-server'){
                        sh '''
                            $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=zomata-clone-jen -Dsonar.projectKey=zomata-clone-jen
                        '''
                    }
                }
            }
        }
        
    }
}

