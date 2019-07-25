pipeline {
    agent {
        label "jenkins-go"
    }
    stages {
      stage('Build Release') {
        when {
          branch 'master'
        }
        steps {
          container('go') {
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http') {
              checkout scm
            }
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http/charts/golang-http') {
                // ensure we're not on a detached head
                sh "git checkout master"
                // until we switch to the new kubernetes / jenkins credential implementation use git credentials store
                sh "git config --global credential.helper store"

                sh "jx step git credentials"
            }
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http') {
              // so we can retrieve the version in later steps
              sh "echo \$(jx-release-version) > VERSION"
            }
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http/charts/golang-http') {
              sh "make tag"
            }
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http') {
              container('go') {
                sh "make build"
                sh 'export VERSION=`cat VERSION` && skaffold build -f skaffold.yaml'

                sh "jx step post build --image $DOCKER_REGISTRY/$ORG/$APP_NAME:\$(cat VERSION)"
              }
            }
          }
        }
      }
   }
}
