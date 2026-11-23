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
                    junit allowEmptyResults true, testResults 'backendtargetsurefire-reports.xml'
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
                archiveArtifacts artifacts 'backendtarget.jar', fingerprint true
            }
        }
    }

    post {
        failure {
            emailext(
                to 'ayetteboubaya@gmail.com',
                subject ÉCHEC du build ${env.JOB_NAME} #${env.BUILD_NUMBER},
                body Le build a échoué.

Job  ${env.JOB_NAME}
Build  #${env.BUILD_NUMBER}
Détails  ${env.BUILD_URL}console

            )
        }
    }
}