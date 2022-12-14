pipeline {
    options {
        timeout(time: 1, unit: 'HOURS')
    }
    agent {
        label 'ubuntu-1804 && amd64 && docker'
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
                    
                    sh "docker build -t docker/getting-started ."
                    
                    withCredentials([usernamePassword(credentialsId: 'githubPAT', passwordVariable: 'pass', usernameVariable: 'usr')]) {
                        sh """
                        echo ${pass} | docker login ${dockerRegistry} -u ${usr} --password-stdin
                        docker push docker/getting-started
                        """
                        
                        // docker push ${dockerRegistry}/${dockerOwner}/${app}:${env.BUILD_NUMBER}
                    }                    
                }
            }
        }
    }
}
