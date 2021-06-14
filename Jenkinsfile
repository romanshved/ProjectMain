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
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'docker_prod',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/index.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo rm -rf /opt/index/* && unzip /tmp/index.zip -d /opt/index && sudo /usr/bin/systemctl start index'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
