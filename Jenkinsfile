import jenkins.model.Jenkins;


pipeline {
    agent any

    stages {

        stage("NPM Setup") {
            steps {
              nodejs(nodeJSInstallationName: "sbom") {
                sh "npm install"
              }
            }
        }

        stage("Generate and Upload SBOM") {
            when { branch "main" }
            steps {
              nodejs(nodeJSInstallationName: "sbom") {
                sh "npx cyclonedx-bom -o npm-bom.xml"
              }
              dependencyTrackPublisher artifact: 'npm-bom.xml', projectName: 'getting-started', projectVersion: GIT_COMMIT.take(5), synchronous: false
            }
        }

      }

      post {
          always {
              cleanWs()
          }
      }
}
