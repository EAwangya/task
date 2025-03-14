pipeline {
    agent any

    stages {
        // stage('git') { 
        //     steps {
        //         git branch: 'main', url: 'https://github.com/EAwangya/task.git'
        //     }
        // }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the custom Docker image using the Dockerfile.ci
                    sh 'docker build -f Dockerfile.ci -t custom-hugo-ci .'
                }
            }
        }

        stage('Verify Hugo Installation') {
            steps {
                script {
                    // Run Hugo version command to confirm Hugo is installed
                    sh 'docker run --rm custom-hugo-ci hugo version'
                }
            }
        }

        stage('Run Task and Build Documentation') {
            steps {
                script {
                    // Run the custom Docker image and execute Task to build the docs
                    sh '''
                    docker run --rm -v $PWD:/app custom-hugo-ci sh -c "cd /app && task docs:build"
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up resources...'
            // Clean up Docker images if necessary
            sh 'docker image prune -f'
        }
    }
}
