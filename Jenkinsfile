pipeline {
    agent any
    triggers {
        githubPush()
    } 

    stages {
        stage('CheckOut') {
            steps {
                git 'https://github.com/abdulp07-git/jenkins-week9.git'
            }
        }
        stage('Unit Test') {  // New stage for unit testing
            steps {
                sh 'mvn test'  // Runs the unit tests
            }
            //post {
              //  always {
                //    junit '**/target/surefire-reports/*.xml'  // Publishes the test results
                //}
            //}
        }
        stage('SonarCloud analysis') {
            steps {
                withSonarQubeEnv('MySonarCloud') {
                    sh 'mvn clean verify sonar:sonar \
                        -Dsonar.token=338fc2a1e13ecf48a1960961105dade4785c7300 \
                        -Dsonar.host.url=https://sonarcloud.io \
                        -Dsonar.organization=abdulp07-git \
                        -Dsonar.projectKey=abdulp07-git_jenkins-week9'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') { 
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("Build") {
            steps {
                sh 'mvn clean package'
                archiveArtifacts artifacts: '**/target/*.war', allowEmptyArchive: true
            }
        }
        stage("Deploy") {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'Tomcat_deployer', path: '', url: 'http://abdjenkins.com:8081/manager/html')], contextPath: 'Web application', war: '**/*.war'
            }
        }
    }
}

