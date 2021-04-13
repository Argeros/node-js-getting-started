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
              catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                  nodejs(nodeJSInstallationName: "sbom") {
                    sh "npx cyclonedx-bom -o npm-bom.xml"
                  }

                  script {
                    sbom_list = sh(returnStdout: true, script: "find . -iname '*bom.xml'").trim().split("\n")
                    sbom_list.each { item ->
                      application_name = item.split("/")
                      sh "echo ${item}"
                      sh "echo ${application_name}"
                    }
                  }
                  //sbom_list.each { item ->
                  //    sh "echo ${item}"
                  //}

                  dependencyTrackPublisher artifact: 'npm-bom.xml', projectName: 'getting-started', projectVersion: GIT_COMMIT.take(5), synchronous: false
              }
            }
        }

      }

      post {
          always {
              cleanWs()
          }
      }
}
