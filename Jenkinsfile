pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Checkout the Code') {
            steps {
                git url: 'https://github.com/naimishdev/springboot-maven-course-micro-svc.git', branch: 'master'
            }
        }
        stage('Build the Code') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Sonar Quality Check') {
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh 'mvn sonar:sonar'
                    }
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t Sankalita-2000/spring-petclinic:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                    sh 'docker push Sankalita-2000/spring-petclinic:latest'
                }
            }
        }
    }
}
