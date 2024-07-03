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
        stage('quality gate analysis'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('docker img build and tag'){
            steps{
                sh 'docker build -t mukeshr29/zomato-jenkins .'
            }
        }
        stage('docker image scan'){
            steps{
                sh 'trivy image --format table -o trivyimg-report.html mukeshr29/zomato-jenkins'
            }
        }
        stage('dockerimage push'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){
                        sh 'docker push mukeshr29/zomato-jenkins'
                    }
                }
            }
        }
        stage('k8s deployment'){
            steps{
                script{
                    withKubeConfig(caCertificate: '', clusterName: 'zomata', contextName: '', credentialsId: 'k8s', namespace: 'zomato', restrictKubeConfigAccess: false, serverUrl: 'https://9B0E634D4A14EF94B5C0ECBCA9EEF643.gr7.us-east-1.eks.amazonaws.com'){
                        sh 'kubectl apply -f deployment.yml -f service.yml'
                        sleep 60
                    }
                }
            }
        }
        stage('verify deployment'){
            steps{
                script{
                    withKubeConfig(caCertificate: '', clusterName: 'zomata', contextName: '', credentialsId: 'k8s', namespace: 'zomato', restrictKubeConfigAccess: false, serverUrl: 'https://622C179E106D3C06E5A8BA4259FA47A0.gr7.us-east-1.eks.amazonaws.com'){
                        sh 'kubectl get pods'
                        sh 'kubectl get svc'
                    }
                }
            }
        }
    }
}

