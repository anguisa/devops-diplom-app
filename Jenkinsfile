// pipeline {
//   agent {
//     kubernetes {
//       defaultContainer 'jnlp'
//       yaml """
// apiVersion: v1
// kind: Pod
// metadata:
// labels:
//   component: ci
// spec:
//   # Use service account that can deploy to all namespaces
//   serviceAccountName: jenkins-agent
//   containers:
//   - name: kubectl
//     image: bitnami/kubectl:1.24.3
//     command: ["cat"]
//     tty: true
//     # Without this sh hangs in kubernetes plugin
//     securityContext:
//       runAsUser: 1000
//   - name: kaniko
//     image: gcr.io/kaniko-project/executor:debug
//     command: ["/busybox/cat"]
//     tty: true
//     volumeMounts:
//     - name: jenkins-docker-cfg
//       mountPath: /kaniko/.docker
//   volumes:
//   - name: jenkins-docker-cfg
//     secret:
//       secretName: docker-credentials
//       items:
//       - key: .dockerconfigjson
//         path: config.json
// """
// }
//   }
//
// 	stages {
// //         stage('Checkout Code') {
// //             steps {
// //               checkout scm
// //             }
// //         }
//
// //         stage('Checkout') {
// //             steps {
// //                 git 'https://github.com/anguisa/devops-diplom-app.git'
// //             }
// //         }
//
// 		stage('Build docker image') {
// 			steps {
// 			    checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/anguisa/devops-diplom-app']]])
//                 container(name: 'kaniko', shell: '/busybox/sh') {
// //                   withEnv(['PATH+EXTRA=/busybox']) {
//                       sh '''#!/busybox/sh
// 			          /kaniko/executor --cleanup --dockerfile app/Dockerfile --context=`pwd` --destination=anguisa/diplom_app:1.0.0 -v debug
// 			          '''
// // 			      }
// 			    }
// 			}
// 		}
// //         stage('Deploy to kubernetes') {
// //           steps {
// //             container('kubectl') {
// // //                 sh("kubectl patch deploy/diplom -p '{\"spec\":{\"replicas\":2}}' -n stage")
// //                 sh 'kubectl patch deploy/diplom -p \'{"spec": {"template": {"spec": {"containers": [{"name": "diplom-app","image": "anguisa/diplom_app:1.0.0"}]}}}}\' -n stage'
// //             }
// //           }
// //         }
// 	}
// }
//
//
pipeline {
  agent {
    kubernetes {
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: jenkins-agent
  containers:
  - name: kubectl
    image: bitnami/kubectl:1.24.3
    command: ["cat"]
    tty: true
    # Without this sh hangs in kubernetes plugin
    securityContext:
      runAsUser: 1000
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command: ["/busybox/cat"]
    tty: true
    volumeMounts:
    - name: jenkins-docker-cfg
      mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    secret:
      secretName: docker-credentials
      items:
      - key: .dockerconfigjson
        path: config.json
"""
}
  }

	stages {
//         stage('Checkout Code') {
//             steps {
//               checkout scm
//             }
//         }

//         stage('Checkout') {
//             steps {
//                 git 'https://github.com/anguisa/devops-diplom-app.git'
//             }
//         }

        stage("TEST") {
            steps {
            script {
                sh'''
                echo GIT_COMMIT ${GIT_COMMIT}
                echo GIT_TAG ${GIT_TAG}
                echo GIT_BRANCH ${GIT_BRANCH}
                echo GIT_LOCAL_BRANCH ${GIT_LOCAL_BRANCH}
                echo GIT_PREVIOUS_COMMIT ${GIT_PREVIOUS_COMMIT}
                echo GIT_PREVIOUS_SUCCESSFUL_COMMIT ${GIT_PREVIOUS_SUCCESSFUL_COMMIT}
                echo GIT_URL ${GIT_URL}
                echo GIT_URL_N - ${GIT_URL_N}
                echo GIT_AUTHOR_NAME ${GIT_AUTHOR_NAME}
                echo GIT_COMMITTER_EMAIL ${GIT_COMMITTER_EMAIL}
                echo !!!!!!!!!
                echo GIT_COMMIT ${env.GIT_COMMIT}
                echo GIT_TAG ${env.GIT_TAG}
                echo GIT_BRANCH ${env.GIT_BRANCH}
                echo GIT_LOCAL_BRANCH ${env.GIT_LOCAL_BRANCH}
                echo GIT_PREVIOUS_COMMIT ${env.GIT_PREVIOUS_COMMIT}
                echo GIT_PREVIOUS_SUCCESSFUL_COMMIT ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}
                echo GIT_URL ${env.GIT_URL}
                echo GIT_URL_N - ${env.GIT_URL_N}
                echo GIT_AUTHOR_NAME ${env.GIT_AUTHOR_NAME}
                echo GIT_COMMITTER_EMAIL ${env.GIT_COMMITTER_EMAIL}
                echo !!!!!!!!!
                '''
            }
            script {
                echo sh(returnStdout: true, script: 'env')
            }
            }
        }
//!!!!
// 		stage('Build docker image') {
// 			steps {
// 			    checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/anguisa/devops-diplom-app']]])
//                 container(name: 'kaniko', shell: '/busybox/sh') {
// //                   withEnv(['PATH+EXTRA=/busybox']) {
//                       sh '''#!/busybox/sh
// 			          /kaniko/executor --cleanup --dockerfile Dockerfile --context=`pwd`/app --destination=anguisa/diplom_app:1.0.0 -v debug
// 			          '''
// // 			      }
// 			    }
// 			}
// 		}
//         stage('Deploy to kubernetes') {
//           steps {
//             container('kubectl') {
// //                 sh("kubectl patch deploy/diplom -p '{\"spec\":{\"replicas\":2}}' -n stage")
//                 sh 'kubectl patch deploy/diplom -p \'{"spec": {"template": {"spec": {"containers": [{"name": "diplom-app","image": "anguisa/diplom_app:1.0.0"}]}}}}\' -n stage'
//             }
//           }
//         }
	}
}