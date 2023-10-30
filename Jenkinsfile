def podTemplate = "podTemplate.yaml"
echo "${podTemplate}"
sh "printenv"
sh "pwd"
sh "ls"

pipeline {
    agent {
        kubernetes {
            label "jenkins-${UUID.randomUUID().toString()}"
            //yamlFile "$podTemplate"
            yaml """
            apiVersion: v1
            kind: Pod
            metadata:
              labels:
                jenkins: jenkins-pipeline
            spec:
              volumes:
                - name: sharedvolume
                  emptyDir: {}
                - name: kaniko-secret
                  secret:
                    secretName: dockercred
                    items:
                      - key: .dockerconfigjson
                        path: config.json
              serviceAccountName: jenkins
              securityContext:
                runAsUser: 0
              containers:
                - name: helm
                  image: "justinrlee/helm3"
                  ttyEnabled: true
                  command:
                    - sleep
                  args:
                    - "9999999"
                - name: kaniko
                  image: gcr.io/kaniko-project/executor:debug
                  command:
                    - sleep
                  args:
                    - "9999999"
                  volumeMounts:
                    - name: kaniko-secret
                      mountPath: /kaniko/.docker
                - name: gradle
                  image: gradle
                  imagePullPolicy: Always
                  ttyEnabled: true
                  command:
                    - sleep
                  args:
                    - 99d
                - name: python
                  image: python:latest
                  imagePullPolicy: Always
                  command:
                    - sleep
                    - "1000"
                  tty: true
            """

        }
    }
    environment {
        NAME = "priyankalearnings"
        // VERSION = "${env.GIT_COMMIT}"
        VERSION = "${env.BUILD_ID}"
        IMAGE_REPO = "gagan2104"
        NAMESPACE = "jenkins"
        HELM_CHART_DIRECTORY = "charts/priyankalearnings"
        GITHUB_TOKEN = credentials('githubpat')
    }
    stages {
        stage('Unit Tests') {
            steps {
                echo 'Implement unit tests if applicable from pr.'
                echo 'This stage is a sample placeholder'
            }
        }
//         stage('Gradle build') {
//             steps {
//                 script {
//                     container(name: 'gradle') {
//                         sh "gradle clean build"
//                     }
//                 }
//             }
//         }
//         stage('Build Image') {
//             steps {
//                 script {
//                     container('kaniko') {
//                         sh '''
//               /kaniko/executor --context `pwd` --destination ${IMAGE_REPO}/${NAME}:${VERSION}
//             '''
//                     }
//                 }
//             }
//         }
//         // stage('helm install') {
//         //   steps {
//         //     script{
//         //         container('helm'){
//         //           sh "helm list"
//         //           sh "helm lint ./${HELM_CHART_DIRECTORY}"
//         //           sh "helm upgrade --set image.tag=${VERSION} ${NAME} ./${HELM_CHART_DIRECTORY} -n ${NAMESPACE}"
//         //           sh "helm list"
//         //         }
//         //          }
//         //     }
//         // }

//         stage('Clone/Pull Repo') {
//             steps {
//                 script {
//                     if (fileExists('helm-charts')) {
//                         echo 'Cloned repo already exists - Pulling latest changes'
//                         dir("helm-charts") {
//                             sh 'git pull'
//                         }
//                     } else {
//                         sh 'git clone https://github.com/Gagans2104/helm-charts'
//                         sh 'ls -ltr'
//                     }
//                 }
//             }
//         }

//         stage('Commit & Push') {
//             steps {
//                 script {
//                     dir("helm-charts/manifests/${NAME}/") {
//                         withCredentials([usernamePassword(
//                             credentialsId: 'githubpat',
//                             usernameVariable: 'username',
//                             passwordVariable: 'password'
//                         )]) {
//                             encodedPassword = URLEncoder.encode("$password", 'UTF-8')
//                             echo "sa ${encodedPassword}"
//                             sh "git config --global user.email 'jenkins@ci.com'"
//                             sh "git remote set-url origin https://${username}:${encodedPassword}@github.com/${username}/helm-charts.git"
//                             sh 'sed -i "s#tag:.*#tag: ${VERSION}#g" values.yaml'
// //                             sh "git checkout -b ${NAME}-${env.BUILD_ID}"
//                             sh 'cat values.yaml'
//                             sh 'git add values.yaml'
//                             sh 'git commit -am "Updated image version for Build - $VERSION"'
//                             echo 'push started'
// //                             sh "git push origin ${NAME}-${env.BUILD_ID}"
//                             sh "git push origin main"
//                         }
//                         echo 'push complete'
//                     }
//                 }
//             }
//         }

        // stage('Raise PR') {
        //   steps {
        //      script {
        //         withCredentials([usernamePassword(credentialsId: 'githubpat',
        //               usernameVariable: 'username',
        //               passwordVariable: 'password')]){
        //             encodedPassword = URLEncoder.encode("$password",'UTF-8')
        //             echo 'In Pr'
        //             container(name: 'python') {
        //             sh "printenv"
        //             sh "pip3 install -r requirements.txt"
        //             sh "python3 oop.py"
        //             }
        //               }
        //         // sh "bash pr.sh"
        //     }
        //   }
        // }
    }
}
