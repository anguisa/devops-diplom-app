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

		stage('Build docker image') {
			steps {
                container(name: 'kaniko', shell: '/busybox/sh') {
//                   withEnv(['PATH+EXTRA=/busybox']) {
                      sh '''#!/busybox/sh
			          /kaniko/executor --cleanup --dockerfile `pwd`/app/Dockerfile --context=`pwd` --destination=anguisa/diplom_app:1.0.0 -v debug
			          '''
// 			      }
			    }
			}
		}
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