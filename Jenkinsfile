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
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http-demo') {
              checkout scm
            }
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http-demo') {
                // ensure we're not on a detached head
                sh "git checkout master"
                // until we switch to the new kubernetes / jenkins credential implementation use git credentials store
                sh "git config --global credential.helper store"

            }
            dir ('/home/jenkins/go/src/github.com/janderton/golang-http-demo') {
              container('go') {
                sh 'apk add --no-cache --update build-base gcc abuild binutils binutils-doc gcc-doc git musl-dev'
                sh 'go get github.com/codegangsta/negroni && \'
                     'go get github.com/mattn/go-sqlite3 && \'
                     'go get github.com/yosssi/ace'
                sh 'CGO_ENABLED=1 GOOS=linux go build -a -ldflags \'-linkmode external -extldflags "-static"\' -o goapp ./src'                
               // go-sqlite3 requires CGO_ENABLED=1 and static linked mode to work in the scratch container

                
              }
              //TODO: Archive artifact from above step
            }
          }
        }
      }
   }
}
