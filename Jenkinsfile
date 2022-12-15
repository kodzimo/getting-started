pipeline {
    options {
        timeout(time: 1, unit: 'HOURS')
    }
    agent {
        label 'master'
    }
    stages {
        stage ('Prepare') {
            steps {
                script {
                    dockerRegistry = "ghcr.io"
                    dockerOwner = "kodzimo"
                }
            }
        }

        stage('build and push') {
            when {
                branch 'master'
            }
            steps {
                script {
                    
                    sh "docker build -t docker/getting-started:${env.BUILD_NUMBER} ."
                    
                    withCredentials([usernamePassword(credentialsId: 'githubPAT', passwordVariable: 'pass', usernameVariable: 'usr')]) {
                        sh """
                        echo ${pass} | docker login ${dockerRegistry} -u ${usr} --password-stdin
                        docker push docker/getting-started:${env.BUILD_NUMBER}
                        """
                        
                        // docker push ${dockerRegistry}/${dockerOwner}/${app}:${env.BUILD_NUMBER}
                    }                    
                }
            }
            when {
                branch 'dev'
            }
            steps {
                script {
                    
                    sh "docker build -t docker/getting-started-dev:${env.BUILD_NUMBER} ."
                    
                    withCredentials([usernamePassword(credentialsId: 'githubPAT', passwordVariable: 'pass', usernameVariable: 'usr')]) {
                        sh """
                        echo ${pass} | docker login ${dockerRegistry} -u ${usr} --password-stdin
                        docker push ${dockerRegistry}/${dockerOwner}/getting-started-dev:${env.BUILD_NUMBER}
                        """
                        
                        // docker push ${dockerRegistry}/${dockerOwner}/${app}:${env.BUILD_NUMBER}
                    }                    
                }
            }
        }
    }
}
