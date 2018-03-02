#!groovy
@Library('Utilities@1.5')_
node ('worker_node1') {
try {
      stage('Source') {
         // always run with a new workspace
         cleanupWs()
         checkout scm
         stash name: 'test-sources', includes: 'build.gradle,src/test/'
      }
      stage('Build') {
         // Run the gradle build
         gbuild2 'clean build -x test'
      }
      stage ('Test') {
         // execute required unit tests in parallel
	 parallel (
            worker2: { node ('worker_node2'){
               // always run with a new workspace
               cleanupWs()
	unstash 'test-sources'
	gbuild2 '-D test.single=TestExample1 test'
            }},
            worker3: { node ('worker_node3'){
               // always run with a new workspace
               cleanupWs()
               unstash 'test-sources'
               gbuild2 '-D test.single=TestExample2 test'
            }},
         )
      } 
   } 
   catch (err) {
      echo "Caught: ${err}"
   }
   stage ('Notify') {
      // mailUser('<your email address>', "Finished")
   }
}
