pipeline {
    agent any

    stages {
        stage('Checkout Source Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Tools') {
            steps {
                sh 'git --version'
                sh 'mvn -version'
            }
        }

        stage('Build Spring Boot Jar') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Jar Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Push Jar to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'Nexus_credentials',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        JAR_FILE=$(ls target/*.jar | grep -v original | head -n 1)

                        mvn deploy:deploy-file \
                          -DgroupId=com.githubhw6 \
                          -DartifactId=github-hw6 \
                          -Dversion=1.0.${BUILD_NUMBER} \
                          -Dpackaging=jar \
                          -Dfile=$JAR_FILE \
                          -DrepositoryId=nexus \
                          -Durl=http://nexus:8081/repository/maven-releases/ \
                          -DgeneratePom=true \
                          -Dusername=$NEXUS_USER \
                          -Dpassword=$NEXUS_PASS
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'ls -lah target || true'
        }
    }
}
