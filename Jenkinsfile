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
            #trivy image --severity CRITICAL,HIGH --no-progress --exit-code 1 hello-world
            trivy image --format json hello-world
        '''
    }
    stage ('Publish Trivy Report') {
        // Archive the Trivy report to make it available on the Jenkins dashboard
        archiveArtifacts artifacts: 'trivy-report.json', allowEmptyArchive: false

        // Display Trivy report content in the console (optional)
        sh label: '', script: 'cat trivy-report.json'
    }    
}
