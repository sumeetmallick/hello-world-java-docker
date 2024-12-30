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
            #trivy image --format json --output trivy-report.json hello-world
            trivy image --format template --template "@contrib/html.tpl" --output trivy-report.html hello-world
        '''
    }
    stage ('Publish Trivy Report') {
        // Archive the Trivy report to make it available on the Jenkins dashboard
        archiveArtifacts artifacts: 'trivy-report.html', allowEmptyArchive: false
        // Publish the HTML report on Jenkins dashboard
        publishHTML (target: [
            reportName: 'Trivy Vulnerability Report',
            reportDir: '.',
            reportFiles: 'trivy-report.html',
            keepAll: true,
            alwaysLinkToLastBuild: true,
            allowMissing: false
        ])        
    }    
}
