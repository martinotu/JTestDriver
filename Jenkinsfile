stage('Build') {
  // The first milestone step starts tracking concurrent build order
  milestone()
  node {
    echo "Building"
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
        echo "Deployed to INT"
      }
      stage('Acceptance Tests') {
        if (env.BRANCH_NAME.startsWith("PR-")) {
            echo "PR Tests"
          }
        else if (env.BRANCH_NAME.startsWith("feature/")) {
            echo "Feature Tests"
          }
        else if (env.BRANCH_NAME.startsWith("master")) {
            echo "System Tests"
          }
      }
    }
    milestone()
     stage('QA deploy'){
        echo "Deployed to QA"
      }
     stage('QA Acceptace&Regression test'){
        echo "Deployed to QA"
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
