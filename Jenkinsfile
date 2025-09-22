pipeline {
    agent any
    environment {
        GIT_TOKEN = credentials('SampleGit')  // Secret text credential
        GIT_URL = "https://$GIT_TOKEN@github.com/akash08-ak/sample.git"
    }
    
   
    stages {
        stage('Checkout') {
            steps {
                git branch: 'sample',
                    url: "${GIT_URL}"
            }
        }
        stage('Build') {
            steps {
                echo "Code checked out from sample branch. Add your build steps here."
            }
        }
    }
}
