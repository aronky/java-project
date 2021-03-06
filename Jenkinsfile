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
        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: 'jfrog',
                    url: 'http://44.195.45.90:8082/artifactory',
                    credentialsId: 'jfrog-art',
                    bypassProxy: true
                )
            }
        }        
         stage('Deploy Artifacts') {
            steps {
                rtUpload (
                     serverId: 'jfrog',
                     spec: ''{
                        "files": [{
                            "pattern": '*.war',
                            "target": 'maven/'
                          }
                        ]
                     }'',
                 )
            }
        }
        
        stage ('Code Quality scan') {
            steps{
        withSonarQubeEnv('sonar') {
        sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
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
