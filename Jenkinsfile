pipeline {
    agent { docker { image 'node:6.3' } }
    stages {
        stage('build') {
            steps {
                sh 'npm --version'
            }
        }
    }
}


pipeline {
    agent { docker { image 'node:12.18.4' } }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git clone "https://github.com/zloof/Jenkins"

                // build docker
                docker build \
                  --cache-from "${IMAGE_NAME}:${IMAGE_TAG_DEV}" \
                  -t "${IMAGE_NAME}:${IMAGE_TAG}" \
                  --target production \
                  -f Dockerfile \
                  .

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                }
            }
        }
    }

    stage('push') {
            steps {
                // LOGIN_TO_ECR
                eval $(aws ecr get-login --profile my-profile --no-include-email)
                // docker push to ECR
                docker push "$ECR_IMAGE_NAME:$IMAGE_TAG"

            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                }
            }
        }
    }

    stage('deploy-to-test-env') {
            steps {
                // set kubeconfig
                // TODO: set kubeconfig
                // deploy to test
                helm upgrade --install --wait --set app.tag="${image_tag}" "${deployment_name}" --namespace "${namespace}" ./chart

            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                }
            }
        }
    }
}
