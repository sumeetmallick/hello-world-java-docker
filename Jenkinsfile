node {
    
    stage ('build') {
        sh label: '', script: 'mvn clean install -DskipTests'
    }
    stage ('Docker Build') {
        sh label: '', script: '''
            docker build -t hello-world .
        '''
    }
    stage ('Trivy Scan') {
        sh label: '', script: '''
            # Ensure Trivy is installed and available on the Jenkins agent
            trivy image --severity CRITICAL,HIGH --no-progress --exit-code 1 hello-world
        '''
    }    
}
