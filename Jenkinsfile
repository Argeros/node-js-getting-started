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
                    sh "npx cyclonedx-bom -o derps/npm-bom.xml"
                  }

                  script {
                    sbom_list = sh(returnStdout: true, script: "find . -iname '*bom.xml'").trim().split("\n")
                    sbom_list.each { item ->
                      application_name = item.split("/")[1] + "_" + item.split("/")[2].split("_")[0]
                      echo "Uploading ${item} to project ${application_name}"
                      dependencyTrackPublisher artifact: item, projectName: application_name, projectVersion: GIT_COMMIT.take(5), synchronous: false
                    }
                  }
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
