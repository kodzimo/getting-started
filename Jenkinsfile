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

        stage('Build from master and push') {
            when {
                branch 'master'
            }
            steps {
                script {
                    
                    sh "docker build -t ${dockerRegistry}/${dockerOwner}/getting-started:${env.BUILD_NUMBER} ."
                    sh "docker build -t ${dockerRegistry}/${dockerOwner}/notes:${env.BUILD_NUMBER} ./app"
                    
                    withCredentials([usernamePassword(credentialsId: 'githubPAT', passwordVariable: 'pass', usernameVariable: 'usr')]) {
                        sh """
                        echo ${pass} | docker login ${dockerRegistry} -u ${usr} --password-stdin
                        docker push ${dockerRegistry}/${dockerOwner}/getting-started:${env.BUILD_NUMBER}
                        docker push ${dockerRegistry}/${dockerOwner}/notes:${env.BUILD_NUMBER}
                        docker rmi -f ${dockerRegistry}/${dockerOwner}/getting-started:${env.BUILD_NUMBER}
                        docker rmi -f ${dockerRegistry}/${dockerOwner}/notes:${env.BUILD_NUMBER}
                        """
                        
                        // docker push ${dockerRegistry}/${dockerOwner}/${app}:${env.BUILD_NUMBER}
                    }                    
                }
            }
        }
        stage('Build from dev and push') {
            when {
                branch 'dev'
            }
            steps {
                script {
                    
                    sh "docker build -t docker/getting-started-dev:${env.BUILD_NUMBER} ."
                    sh "docker build -t ${dockerRegistry}/${dockerOwner}/notes-dev:${env.BUILD_NUMBER} ./app"
                    
                    withCredentials([usernamePassword(credentialsId: 'githubPAT', passwordVariable: 'pass', usernameVariable: 'usr')]) {
                        sh """
                        echo ${pass} | docker login ${dockerRegistry} -u ${usr} --password-stdin
                        docker push ${dockerRegistry}/${dockerOwner}/getting-started:dev-${env.BUILD_NUMBER}
                        docker push ${dockerRegistry}/${dockerOwner}/notes:dev-${env.BUILD_NUMBER}
                        docker rmi -f ${dockerRegistry}/${dockerOwner}/getting-started:dev-${env.BUILD_NUMBER}
                        docker rmi -f ${dockerRegistry}/${dockerOwner}/notes:dev-${env.BUILD_NUMBER}
                        """
                        
                        // docker push ${dockerRegistry}/${dockerOwner}/${app}:${env.BUILD_NUMBER}
                    }                    
                }
            }
        }
        stage('Deploy') {
            when {
                branch 'master'
            }            
            steps {
                script {

                    sh """docker pull ${dockerRegistry}/${dockerOwner}/getting-started:${env.BUILD_NUMBER}
                    docker pull ${dockerRegistry}/${dockerOwner}/notes:${env.BUILD_NUMBER}
                    docker run --name gs-${env.BUILD_NUMBER} -dp 780:80 --rm --network="project ${dockerRegistry}/${dockerOwner}/getting-started:${env.BUILD_NUMBER}
                    docker run --name notes-${env.BUILD_NUMBER} -dp 3000:3000 -v notes:/app --network="project ${dockerRegistry}/${dockerOwner}/notes:${env.BUILD_NUMBER}""""
                }
            }
        }
    }
    post {
        // always {
        //     cleanWs()
        // }
        success {
            echo "Job succeded"            
        }
    }
}
