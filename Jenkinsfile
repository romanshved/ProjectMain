pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build'
                archiveArtifacts artifacts: 'index.html'
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'main'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'docker_prod',
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/index.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo scp - v -o StrictHostKeyChecking=no index.html ubuntu@ec2-3-64-114-249.eu-central-1.compute.amazonaws.com:/var/www/html && sh exit=0'
                                    )
                                ]
                            )
                        ]
                 )
            }
        }
    }
}
