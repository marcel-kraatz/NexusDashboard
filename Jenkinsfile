properties([
  parameters([
    gitParameter(
        branch: '',
        branchFilter: 'origin/(.*)',
        defaultValue: 'origin/main',
        description: '',
        name: 'BRANCH',
        quickFilterEnabled: false,
        selectedValue: 'DEFAULT',
        sortMode: 'NONE',
        tagFilter: '*',
        useRepository: 'git@github.com:DarkflameUniverse/NexusDashboard.git',
        type: 'PT_BRANCH'
    )
  ])
])

node('worker'){
    stage('Clone Code'){
        checkout([
            $class: 'GitSCM',
            branches: [[name: params.BRANCH]],
            extensions: [],
            userRemoteConfigs: [
                [
                    credentialsId: 'aronwk',
                    url: 'git@github.com:DarkflameUniverse/NexusDashboard.git'
                ]
            ]
        ])
    }
    def tag = ''
    stage("Build Container"){
        if (params.BRANCH.contains('main')){
            tag = 'latest'
        } else {
            tag = params.BRANCH.replace('\\', '-')
        }
        sh "docker build -t aronwk/dlu-account_manager:${tag} ."
    }
    stage("Push Container"){
        withCredentials([usernamePassword(credentialsId: 'docker-hub-token', passwordVariable: 'password', usernameVariable: 'username')]) {
            sh "docker login -u ${username} -p ${password}"
            sh "docker push aronwk/dlu-account_manager:${tag}"
            sh 'docker logout'
        }
    }
}
