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
        stage('Sonar Test') {
            steps {
                echo 'Running Sonar Test on Code Quality...'
                sh 'mvn sonar:sonar -Dsonar.host.url=http://192.168.1.15:9000'
            }
        }
            steps {
                withCredentials([string(credentialsId 'sonarqube-connection', variable 'SONAR_AUTH_TOKEN')]) {
                sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
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
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId 'm5muthu1975', variable 'dockerhubpwd')]) {
                   sh 'docker login -u akshyaganesh@rediffmail.com -p ${dockerhubpwd}'

                    }
                   sh 'docker push akshyaganesh/devops'
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