stage('Build') {
  // The first milestone step starts tracking concurrent build order
  version = "1.0.' + env.BUILD_NUMBER
  currentBuild.displayName = version
  
  milestone()
  node {
    echo "Building"
    sh "sleep 10s"
  }
}

// This locked resource contains both Test stages as a single concurrency Unit.
// Only 1 concurrent build is allowed to utilize the test resources at a time.
// Newer builds are pulled off the queue first. When a build reaches the
// milestone at the end of the lock, all jobs started prior to the current
// build that are still waiting for the lock will be aborted
  lock(resource: 'myResource', inversePrecedence: true){
    node() {
      stage('Unit Tests') {
        echo "Unit Tests"
      }
      stage('Archiving package'){
        echo "package archived to S3"
      }
      stage('INT deploy'){
        sh "sleep 5s"
        echo "Deployed to INT"
      }
      stage('Acceptance Tests') {
        if (env.BRANCH_NAME.startsWith("PR-")) {
            sh "sleep 5s"  
            echo "PR Tests set"
          }
        else if (env.BRANCH_NAME.startsWith("feature/")) {
            sh "sleep 10s"  
            echo "Feature Tests set"
          }
        else if (env.BRANCH_NAME.startsWith("master")) {
          sh "sleep 15s"  
          echo "Acceptance Tests set"
          }
      }
    }
    milestone()
    }
     
stage('QA deploy'){
  node{
    sh "sleep 5s" 
    echo "Deployed to QA"
  }  
}
     
stage('QA Acceptace&Regression test'){
  node{
    sh "sleep 20s" 
    echo "Acceptance Tests"
  }
}
  

// The Deploy stage does not limit concurrency but requires manual input
// from a user. Several builds might reach this step waiting for input.
// When a user promotes a specific build all preceding builds are aborted,
// ensuring that the latest code is always deployed.
stage('PROD deploy') {
  if (env.BRANCH_NAME.startsWith("master")) {
            input "Deploy?"
            milestone()
            node {
              echo "Deployingg"
            }
      }

}
