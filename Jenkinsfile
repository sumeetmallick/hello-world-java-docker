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
            if [ ! -f html.tpl ]; then
                wget -O html.tpl https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl
            fi        
            # Ensure Trivy is installed and available on the Jenkins agent
           # trivy image --severity CRITICAL,HIGH --no-progress --format template --output trivy-report.html --exit-code 1 hello-world
            # Run Trivy scan and generate a CSV report
            trivy image --severity CRITICAL,HIGH --no-progress --format json hello-world | \
            jq -r '.Results[].Vulnerabilities[] | [.Severity, .VulnerabilityID, .PkgName, .InstalledVersion, .FixedVersion, .Description] | @csv' > trivy-report.csv || true            
            #trivy image --format json --output trivy-report.json hello-world
           # trivy image --format template --template "@html.tpl" --output trivy-report.html hello-world
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
