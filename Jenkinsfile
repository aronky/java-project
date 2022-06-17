pipeline {
    agent any

    stages {
        stage('Git clone') {
            steps {
               git branch: 'main', url: 'https://github.com/aronky/java-project.git'
            }
        }
    
        stage('Build') {
            steps {
               sh 'cd MyWebApp && mvn clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'cd MyWebApp && mvn test'
            }
        }
        stage ('Code Quality scan') {
            steps{
        withSonarQubeEnv('sonar') {
        sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
            }
         }
        }
        stage ('Artifactory configuration') {
            steps {
                rtServer {
                    id: 'jart',
                    url: "http://3.80.159.88:8082//artifactory"
                    credentialsId: "jfrog-art",
                    bypassProxy: true
                }
            }
        }

        stage('Deploy Artifacts') {
            steps {
                rtUpload {
                     serverId: 'jart'
                }
            }
        }
         stage("Quality gate") {
             steps {
                 waitforQualityGate abortPipeline: true
             }
         }
        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'deployer_user', path: '', url: 'http://54.90.167.194:8080/')], contextPath: 'path', war: '**/*.war'
            }
        }
    }
}
