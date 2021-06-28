properties([
    parameters([
        password(name: 'KEY', description: 'Encryption key')
    ])
])

node {
    stage('Stage 1') {
        # Will print the actual value of the KEY, verbatim
        sh "echo ${KEY}"
    }
}
