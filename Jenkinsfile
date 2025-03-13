pipeline {
    agent any

    environment {
        S3_BUCKET = 'your-bucket-name'  // Replace with your S3 bucket name
        BUILD_VERSION = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                // Install Task if not already installed
                sh '''
                    if ! command -v task &> /dev/null; then
                        echo "Installing Task..."
                        sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d -b ~/.local/bin
                        export PATH="$HOME/.local/bin:$PATH"
                    fi
                '''
                
                // Build the static website
                sh 'task docs:build'

                // Add build version to a version file
                sh """
                    echo "Build: ${BUILD_VERSION}" > hugo/src/build/version.txt
                    echo "Build Date: \$(date)" >> hugo/src/build/version.txt
                """
            }
        }

        stage('Deploy to S3') {
            steps {
                // Deploy to versioned path
                s3Upload(
                    bucket: "${S3_BUCKET}",
                    path: "builds/${BUILD_VERSION}/",
                    includePathPattern: '**/*',
                    workingDir: 'hugo/src/build',
                    acl: 'PublicRead',
                    cacheControl: 'max-age=3600',
                    metadatas: [
                        'Cache-Control:max-age=3600',
                        "Build-Version:${BUILD_VERSION}",
                        "Build-Timestamp:${currentBuild.startTimeInMillis}"
                    ],
                    pluginFailureResultConstraint: 'FAILURE'
                )

                // Also deploy to latest path
                s3Upload(
                    bucket: "${S3_BUCKET}",
                    path: 'latest/',
                    includePathPattern: '**/*',
                    workingDir: 'hugo/src/build',
                    acl: 'PublicRead',
                    cacheControl: 'no-cache',
                    metadatas: [
                        'Cache-Control:no-cache',
                        "Build-Version:${BUILD_VERSION}",
                        "Build-Timestamp:${currentBuild.startTimeInMillis}"
                    ],
                    pluginFailureResultConstraint: 'FAILURE'
                )
            }
        }
    }

    post {
        success {
            echo """
                Website deployed successfully:
                - Versioned build: ${S3_BUCKET}/builds/${BUILD_VERSION}/
                - Latest build: ${S3_BUCKET}/latest/
                Build number: ${BUILD_VERSION}
            """
        }
        failure {
            echo "Deployment failed for build ${BUILD_VERSION}!"
        }
    }
}