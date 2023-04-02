node {
    def mavenHome
    def mavenCMD
    def docker
    def dockerCMD
    def tagName
    
    stage('prepare environment'){
        echo 'Initialize the variables'
        mavenHome = tool name: 'MAVEN_HOME' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        docker = tool name: 'DOCKER' , type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        dockerCMD = "${docker}/bin/docker"
        tagName = "1.0"
    }
    stage ('code checkout'){
        try{
        echo 'pulling the code from github repo'
        git 'https://github.com/sandeepajay1/star-agile-banking-finance.git'
        }
        catch(Exception e){
            echo 'Exception Occur'
            currentBuild.result = "FAILURE"
            emailext body: '''Hello Sandeep
            The Build Number ${BUILD_NUMBER} is Failed. Please look into that.
            Thanks,''', subject: 'The jenkins Job ${JOB_NAME} is Failed ', to: 'sandeep.ajayakumar@gmail.com'
        }
    }
    stage('Build the application'){
        echo 'clean and compile and test package'
        sh "${mavenCMD} clean package"
    }
    stage('publish html reports'){
        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Banking_Finance/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report Banking', reportTitles: '', useWrapperFileDirectly: true])
    }
    stage('Build the DockerImage of the application'){
        try{
        echo 'creating the docker image'
        sh "${dockerCMD} build -t sandeepajay1/finance-me:${tagName} ."
        
        }
        catch(Exception e){
            echo 'Exception Occur'
            currentBuild.result = "FAILURE"
            emailext body: '''Hello Sandeep
            The Build Number ${BUILD_NUMBER} is Failed. Please look into that.
            Thanks,''', subject: 'The jenkins Job ${JOB_NAME} is Failed ', to: 'sandeep.ajayakumar@gmail.com'
            
        }
    }
    stage('push the docker image to dockerhub'){
        echo 'pushing docker image'
        withCredentials([string(credentialsId: 'docker-password', variable: 'DockerPassword')]) {
        // some block
        sh "${dockerCMD} login -u sandeepajay1 -p ${DockerPassword}"
        sh "${dockerCMD} push sandeepajay1/finance-me:${tagName}"
        }
    }
    //Test to webhook
   // stage('deploy the application'){
        
     //   ansiblePlaybook become: true, credentialsId: 'ansiblekey', disableHostKeyChecking: true, installation: 'MyAnsible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
    //}
}
