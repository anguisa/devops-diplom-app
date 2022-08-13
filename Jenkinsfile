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

  environment {
    REPOSITORY_URL = 'https://github.com/anguisa/devops-diplom-app'
  }

  triggers {
    GenericTrigger(
      genericVariables: [
        [key: 'ref', value: '$.ref'],
        [key: 'ref_type', value: '$.ref_type', defaultValue: 'branch'], // дефолтные значения обязательны, если может отсутствовать
        [key: 'commit', value: '$.after', defaultValue: '0'],
        [key: 'repository', value: '$.repository.full_name'],
      ],
      genericHeaderVariables: [[key: 'x-github-event']],
      // создание тега + id коммита нет или push с коммитом с непустым id (одни 0)
      // если тег ставится на коммит, то приходит 2 события - создание тега и push коммитов
      regexpFilterExpression: '^tag create anguisa/devops-diplom-app 0$|^branch push anguisa/devops-diplom-app (?!0{40})([0-9a-f]+)$',
      regexpFilterText: '$ref_type $x_github_event $repository $commit',
      causeString: 'Triggered on $ref',
      printContributedVariables: true,
      printPostContent: true,
      token: 'MYTOKEN',
      tokenCredentialId: ''
    )
  }

  stages {
    stage('Checkout code') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '$ref']], userRemoteConfigs: [[url: env.REPOSITORY_URL]]])
        script {
          if (env.ref_type == 'tag') {
            env.IMAGE_NAME = 'anguisa/diplom_app:' + env.ref // здесь будет ref == v0.8
          } else {
            env.SHORT_HASH = sh(returnStdout: true, script: 'git rev-parse --short $commit').trim() // берём сокращённый хэш
            env.IMAGE_NAME = 'anguisa/diplom_app:'  + env.SHORT_HASH
          }
        }
      }
    }

    stage('Build docker image') {
      steps {
        container(name: 'kaniko', shell: '/busybox/sh') { // сборка docker образов без docker
          sh '''#!/busybox/sh
          /kaniko/executor --cleanup --dockerfile Dockerfile --context=${WORKSPACE}/app --destination=${IMAGE_NAME} -v debug
          '''
        }
      }
    }

    stage('Deploy to kubernetes') {
      when {
        environment name: 'ref_type', value: 'tag'
      }
      steps {
        container('kubectl') {
          sh 'kubectl patch deploy/diplom -p \'{"spec": {"template": {"spec": {"containers": [{"name": "diplom-app","image": "${IMAGE_NAME}"}]}}}}\' -n stage'
        }
      }
    }
  }

}
