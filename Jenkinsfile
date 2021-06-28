pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                properties([
                            parameters([
                                        password(name: 'KEY', description: 'Encryption key')
                                      ])
                          ])
            }
        }
    }
}
