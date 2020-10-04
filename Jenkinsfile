pipeline {
   agent any

   tools {
      maven "M3"
   }

   stages {
      stage('BUILD') {
         steps {
            git 'https://github.com/kmasani81/WebGoatLocal.git'

            // Run Maven
            sh "mvn -Dmaven.test.failure.ignore=true clean package"
         }
      }
      stage('Scans') {
         steps {
            parallel(
               SonarQube: {
                  sh "mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.login=3efe63b36269b032801965f520a4ff10cca0c15c"
                  echo "Getting the analysis results .. "
               },
               GithubReport: {
                  echo "Getting Github report"
               },
               SCAAnalysis: {
                  echo "TODO: Pending to be included."
               },
               SkenAnalysis: {
                  sh "pip3 install --upgrade skencli --user"
                  sh "skencli"
               }
            )
         }
      }

      stage('Publish Artifacts') {
         steps {
            sh "echo 'Pushing to Nexus'"
            sh "sleep 30"
         }
      }

      stage('Docker Build') {
         steps {
            sh "echo 'Running Docker build ..' "
            script {
              SHORT_HASH = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
              DOCKER_RELEASE_TAG = "MYAPP-${SHORT_HASH}"
            }
            echo "DOCKER_RELEASE_TAG:  $DOCKER_RELEASE_TAG"
            //sh "cd $WORKSPACE/webgoat-server && /usr/bin/docker build -t kmasani/webgoat:${DOCKER_RELEASE_TAG} ."
         }
      }

      stage('Container Scan') {

         steps {
            sh "echo 'Running Container scan .. ' "
            // sh "cd $WORKSPACE && /opt/devops/tools/inline_scan-v0.6.0 scan -r kmasani/myapp:${DOCKER_RELEASE_TAG}"
            // sh "/usr/bin/python /opt/devops/scripts/parse_anchore_analysis.py --outfile $WORKSPACE/anchore-reports/webgoat-local_latest-vuln.json"

            //sh "echo 'Pushing Docker .. ' "
            //sh "docker push kmasani/myapp:${DOCKER_RELEASE_TAG}"
         }
      }

      stage('Deploy: DEV') {
         steps {
            sh "echo 'Deploying Docker ..' "
            // sh "/usr/bin/python /opt/devops/scripts/deploy_runner.py ${DOCKER_RELEASE_TAG}"
         }
      }      

   }
}
