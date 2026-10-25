pipeline{
    agent any
    environment{
        REPOSITORY = 'T537-webapp'
        IMAGE_NAME = 'webapp'
        IMAGE_TAG = "${BUILD_NUMBER}"
        USERNAME = 'greybox1'
    }
    stages{
        stage("Checkout"){
            steps{
                checkout scm
            }
        }
        stage("build"){
            steps{
                sh'''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }
        stage('DockerHub Login and Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: '70cd6994-ca26-42b1-a5e5-ab135197f22f',
                        usernameVariable: 'DOCKERHUB_USERNAME',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        printf '%s' "$DOCKERHUB_TOKEN" | \
                          docker login -u "$DOCKERHUB_USERNAME" --password-stdin

                        docker tag $IMAGE_NAME:$IMAGE_TAG \
                          $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG

                        docker push \
                          $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG

                        docker logout
                    '''
                }
            }
        }
        stage("Deploy"){
            steps{
                sh'''
                docker run -d -p 80:80 ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
}