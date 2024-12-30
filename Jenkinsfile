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
          #  if [ ! -f html.tpl ]; then
          #      wget -O html.tpl https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl
         #   fi        
            # Ensure Trivy is installed and available on the Jenkins agent
           # trivy image --severity CRITICAL,HIGH --no-progress --format template --output trivy-report.html --exit-code 1 hello-world
            # Run Trivy scan and generate a CSV report
            trivy image --severity CRITICAL,HIGH --no-progress --format json hello-world | \
            jq -r '.Results[].Vulnerabilities[] | [.Severity, .VulnerabilityID, .PkgName, .InstalledVersion, .FixedVersion, .Description] | @csv' > trivy-report.csv || true            
            #trivy image --format json --output trivy-report.json hello-world
           # trivy image --format template --template "@html.tpl" --output trivy-report.html hello-world
        '''
    }
    stage('Archive Trivy CSV Report') {
        // Archive the CSV report as an artifact
        archiveArtifacts artifacts: 'trivy-report.csv', allowEmptyArchive: false
    }    
}
