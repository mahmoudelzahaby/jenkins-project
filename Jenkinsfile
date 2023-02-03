pipeline {
    agent { label 'jenkins-agent' }
    parameters {
        choice(name: 'ENV', choices: ['dev', 'test', 'prod',"release"])
    } 
    stages {
        stage('build') {
            steps {
                script {
                   if (params.ENV == "release") {
                       withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           sh """
                                docker login -u $USERNAME -p $PASSWORD
                                docker build -t mahmoudahaby/jenkins-pro:${BUILD_NUMBER} .
                                docker push mahmoudahaby/jenkins-pro:${BUILD_NUMBER}
                                echo ${BUILD_NUMBER} > ../jenkins-pro.txt
                           """
                       }
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    if (params.ENV == "dev" || params.ENV == "test" || params.ENV == "prod") {
                            withCredentials([file(credentialsId: 'kubernetes_kubeconfig', variable: 'KUBECONFIG')]) {
                          sh """
                              export BUILD_NUMBER=\$(cat ../jenkins-pro.txt)
                              mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                              cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                              rm -f Deployment/deploy.yaml.tmp
                              kubectl apply -f Deployment --kubeconfig=${KUBECONFIG}
                            """
                        }
                    }
                }
            }
        }
    }
}
