pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'task docs:build'
            }
        }
        stage('Deploy to S3 bucket') {
            steps {
                s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'mydocproject', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: false, selectedRegion: 'us-east-1', showDirectlyInBrowser: false, sourceFile: 'hugo/src/build/*', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 'mydocprofile', userMetadata: []
            }
        }
    }
}
