#!groovy

node('build-slave') {

    currentBuild.result = "SUCCESS"
    cleanWs()

    try {

        stage('Checkout') {
            checkout scm
        }

        stage('Build') {

            env.NODE_ENV = "build"
            print "Environment will be : ${env.NODE_ENV}"
            sh '''
            set -x
            git pull origin master
            pwd
            currdir=$(pwd)
            sh configure-dependencies.sh
	        cd java && mvn clean install
            pwd
	        cd registry && mvn clean install
            cd $currdir
            chmod 755 ./target/metadata.sh
            ./build.sh
            '''

        }

        stage('Publish') {

            echo 'Push to Repo'
            sh '''
            set -x
            ls -al ~/
             # ARTIFACT_LABEL=bronze ./dockerPushToRepo.sh
            ./target/metadata.sh > metadata.json
            cat metadata.json
            archive includes: "metadata.json"
            '''
        }
    } catch (err) {

        currentBuild.result = "FAILURE"
        throw err

    }

}
