def projectName = 'AEM'
def bucket = 'dev.{URL}'
def targetEnv = 'dev'
def branchname = env.BRANCH_NAME
def pipelineView = ""

def gzip = true
def promote = false
def prNumber = env.BRANCH_NAME.drop(3)
def testReportFileName = env.BUILD_TAG + '-report.html'
def functionalFailure = false
def preBuildFailure = false
def buildFailure = false

//artifacts
def packageNameAEM = ''
def packageNameHybris = ''
def packageURL = ''

//deployment urls
//dev
def devAuthor = ""
def devPublisher1 = ""
def devPublisher2 = "" //For Use when there are additional dev publishers
def devPublisher3 = "" //For Use when there are additional de publishers
//qa
def qaAuthor = ""
def qaPublisher1 = ""
def qaPublisher2 = "" //For Use when there are additional qa publishers
def qaPublisher3 = "" //For Use when there are additional qa publishers
//stress
def strAuthor = ""
def strPublisher1 = ""
def strPublisher2 = "" //For Use when there are additional stress publishers
def strPublisher3 = "" //For Use when there are additional stress publishers
//release or stage
def relAuthor = ""
def relPublisher1 = ""
def relPublisher2 = "" //For Use when there are additional stage or release publishers
def relPublisher3 = "" //For Use when there are additional stage or release publishers
//master
def prodAuthor = ""
def prodPublisher1 = ""
def prodPublisher2 = "" //For Use when there are additional prod publishers
def prodPublisher3 = "" //For Use when there are additional prod publishers

// application Support
def bitBucketProj = 'AEM'
def bitBucketRepo = 'aem'
def linkNexus = ""
def linkBitBucket = ""
def linkSonar = ""
def baseReport = ""
def linkFunctional = ""



node {
        stage('Check out Code, Prepare environment') {
            parallel (
                "getEnvironment":{
                    echo sh(returnStdout: true, script: 'env')
                },
                "checkout code" : {
                    checkout scm
                }
            )
            dir('node_modules/') {
                deleteDir()
            }
            sh 'echo $PATH'
            sh "node -v"
            sh "npm -v"
			sh "mvn --version"
			sh "gem list"
        }

       stage('Build AEM Hybris Services & Unit/Checkstyle Tests') {
            parallel (
                "build & test" : {
                    try {
						sh "cd hybris"
                        sh "mvn clean install"
                    }catch(err){
                        hybrisBuildFailure = true;
                        throw err
                    }
                
                },
   
                "checkstyle" : {
                        sh "mvn checkstyle:checkstyle"
                }
            ) 
        }
        stage('Build AEM UI PreBuild Tasks') {
                    try {
		    sh "cd .."
		    sh "cd whirlpool/whirlpool-ui"
                    sh "npm install -g grunt"                     
                    sh "npm install -g grunt-scss-lint"
                    sh "npm install -g grunt-cli"
                    sh "gem install sass"
                    sh "gem install compass"
                    sh "gem install scss_lint"
                    sh "npm install -g node-gyp"
                    sh "npm install yarn -g"
                    sh "npm install -g grunt-webfont --save-dev" 
                    sh "grunt build:dev --brand=insidepass,insidepass-outlet,maytag,whirlpool"
                    }catch(err){
                        preBuildFailure = true;
                        throw err
                    }
                
                } 
        stage('Build AEM & Unit/Checkstyle Tests') {
            parallel (
                "build & test" : {
                    try {
						sh "cd .."
                        sh "mvn clean install"
                    }catch(err){
                        aemBuildFailure = true;
                        throw err
                    }
                
                },
   
                "checkstyle" : {
                        sh "mvn checkstyle:checkstyle"
                }
            ) 
        }
    }
