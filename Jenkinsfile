pipeline {
    environment {
        registry = 'akshyaganesh/maven-docker-agent'
        registryCredential = 'akhyaganesh@rediffmail.com'
        dockerImage = 'maven-docker-agent'
    } 
   
    agent any 

    stages{

        stage('Github Checkout'){
            steps{
                git 'https://github.com/akshyaganesh/devops.git'
                //checkout scmGit(branches [[name '*/main']], extensions [], userRemoteConfigs [[url 'https://github.com/akshyaganesh/devops.git']])
                sh 'mvn clean package'
            }
        }
        stage('MVN CLEAN') {
            steps {
                echo 'Cleaning project...'
                sh 'mvn clean'
            }
        }
        stage('Unit test') {
            steps {
                echo 'Running Maven unit test with JUnit...'
                sh 'mvn test'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script [
                    withSonarQubeEnv('SonarQubeServer') { 
                        sh '''
                            mvn clean verify sonar:sonar \
                                -Dsonar.host.url=http://192.168.1.15:9000 \
                                -Dsonar.login=s$sonar-connection
                            '''
                    } 

                ]
            
            }
        }

        stage('MVN PACKAGE') {
            steps {
                echo 'Maven Packaging'
                sh 'mvn package -Dmaven.test.skip=true'
            }

        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t akshyaganesh/maven-docker-agent .'
                }
            }
        }

        /*stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs 'deploymentservice.yaml',kubeconfigId 'k8sconfigpwd')
                }
            }
        }*/
    } 
}