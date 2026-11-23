pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout GitHub') {
            steps {
                checkout scm
            }
        }

        stage('Tests unitaires') {
            steps {
                dir('backend') {
                    sh 'mvn -B clean test'
                }
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'backend/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Livrable (target)') {
            steps {
                dir('backend') {
                    sh 'mvn -B package -DskipTests'
                }
            }
        }

        stage('Archivage') {
            steps {
                archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
            }
        }
    }

    post {
        failure {
            emailext(
                to: 'ayetteboubaya@gmail.com',
                subject: "ECHEC du build ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """Le build a echoue.

Job : ${env.JOB_NAME}
Build : #${env.BUILD_NUMBER}
Details : ${env.BUILD_URL}console
"""
            )
        }
    }
}
