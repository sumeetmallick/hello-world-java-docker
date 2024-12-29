node {
    
    stage ('build') {
        sh label: '', script: 'mvn clean install -DskipTests'
    }
    stage ('Docker Build') {
        sh label: '', script: '''
            docker build -t hello-world .
        '''
    }    
}
