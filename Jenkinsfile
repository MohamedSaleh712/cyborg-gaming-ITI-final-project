pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'build the code using docker'
                    withCredentials([usernamePassword(credentialsId: 'Docker_cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                            sh '''
                                docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                                docker build -t ${DOCKER_USERNAME}/cyborg-gaming-app:v${BUILD_NUMBER} .
                                docker push ${DOCKER_USERNAME}/cyborg-gaming-app:v${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../build_num.txt
                            '''
                        }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'deploy to the cluster'
                    withCredentials([file(credentialsId: 'kubeconfig_cred', variable: 'KUBECONFIG_ITI')]) {
                            sh '''
                                export BUILD_NUMBER=$(cat ../build_num.txt)
                                mv deployments/deploy.yaml deployments/deploy.yaml.tmp
                                cat deployments/deploy.yaml.tmp | envsubst > deployments/deploy.yaml
                                rm -rf deployments/deploy.yaml.tmp
                                kubectl apply -f deployments --kubeconfig ${KUBECONFIG_ITI}
                            '''
                        }
                }
            }
        }
    }
}
