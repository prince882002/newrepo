//def feature="feature/*"
def devl_br = "dev3"
//def qual_br = "QA"
//def cert_br = "cert"
//def prod_br = "master"

// def packageName="plsqls_P9.zip"

// def targetZipName='ebusiness-extensions_dev3-plsqls-1.0.zip'

def deployScript="audit_deploy.sh"

def environmentName = "dev3"


nodeName="shared"



//Other variables

def props
def remote_path
def remote_cred_id
//def List<String> files = findFiles()

def remote = [:]
remote.name = "dev3"
remote.allowAnyHosts = true

def username_env
def password_env
def appsPassword_env

pipeline {
    agent none
    
    environment{
                dockerRegistry = "689019322137.dkr.ecr.us-east-1.amazonaws.com"
                }

    options {
        timeout(time: 1, unit: 'HOURS') //abort after 1 hour if not completed
    }

    parameters([
        [$class: 'DateParameterDefinition', dateFormat: 'dd/MM/yyyyy', defaultValue: 'LocalDate.now();', description: 'Start date ', name: 'start_date'], [$class: 'DateParameterDefinition', dateFormat: 'dd/MM/yyyy', defaultValue: 'LocalDate.now().plusDays(1);', description: 'Last date', name: 'end_date']
        ])


   stages {
       
       stage("Pipeline Prepare") {
            agent { label nodeName }
            steps {
                pipelinePrepareJDF("jdf-core-finance-package", "sharmaprince@johndeere.com", false)
            }
        }

        stage("Build and Deploy") {
            when{
                beforeAgent true
                anyOf {
                    branch devl_br;
                } }

            stages {

                // stage('Run Maven Build') {
                //     agent { label  nodeName}
                    

                //    steps {
                //           mavenDockerBuild(dockerRegistry , "jdfdockerimages-maven-3.6.3-jdk-8:ff6a235" , "mvn clean package -Ddest=${params.plsqlsFile}")
                //     }
                // }

                stage('Read environment properties') {
                    agent { label  nodeName}

                    steps {
                        script{
                            props = readProperties("jenkins/env/${environmentName}.properties")

                            remote_path = props['remote_path']
                            remote.host = props['remote_server']
                        }

                    }
                }

                stage("Get server credentials")
                        {
                            steps {
                                script {
                                    appsPassword_env = input message: "Enter appsPassword for ${environmentName}", parameters: [password(defaultValue: '', description: '', name: 'hidden')]
                                }

                            }

                        }

                stage('Copy to Server') {
                    agent { label  nodeName}
        
                    steps {
                        script{
                            
                        writeFile(
                        file: "id_rsa",
                        text: retrieveParameterFromSSM("jdf-ops-core-financial-accounting")
                    )
                    sh("sudo chmod 600 ./id_rsa")
                    sh("sudo chown ec2-user:ec2-user ./id_rsa")

                    // Connect to remote server and upload file
                    sh("scp -o StrictHostKeyChecking=no -i id_rsa appldev6@204.53.92.204:/oraebs/appldev6/xxjars")
                    // sh("ssh -i id_rsa appldev6@204.53.92.204 '/oraebs/appldev6/xxjars/deploy-Script-plsql_P9.sh ${appsPassword_env}'")
                     sh("ssh -i id_rsa appldev6@204.53.92.204 '/oraebs/appldev6/xxjars/audit_automation.sh ${appsPassword_env} ${start_date} ${end_date}'")

                     }

                    }
                }
            }
}

    }
}

def readProperties(filePath)
{
    def props=[:]
    def data = readFile(file: filePath)
    def properties=data.split("\n")
    for (int i = 0; i < properties.size(); i++) {
        def pr=properties[i].split("=")
        props[pr[0]]=pr[1].trim()
    }
    return props

}

def retrieveParameterFromSSM(def parameterName) {
    def value = sh(script: "aws ssm get-parameter --name ${parameterName} --with-decryption --region us-east-1 --query Parameter.Value --output text", returnStdout: true)
    return value
}
