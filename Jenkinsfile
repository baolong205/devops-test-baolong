pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timestamps()
    }

    environment {
        GH_REPO = 'https://github.com/baolong205/devops-test-baolong.git'
        GH_PAGES_BRANCH = 'gh-pages'
        PROJECT_NAME = 'devops-test'
        PROJECT_BRANCH = 'main'
        APP_URL = 'https://baolong205.github.io/devops-test-baolong/'
        TELEGRAM_BOT_TOKEN = '8975515792:AAEyz3RbKog7msllb5yMMk5Yb9MlN-gHMpE'
        TELEGRAM_CHAT_ID = '6646581590'
    }

    stages {
        stage('Deploy Started') {
            steps {
                echo '🚀 DEPLOY STARTED'
                sh '''
                    set -eu
                    MSG=$(printf '🚀 DEPLOY STARTED\nProject: %s\nBranch: %s' "$PROJECT_NAME" "$PROJECT_BRANCH")
                    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
                        -d "chat_id=${TELEGRAM_CHAT_ID}" \
                        --data-urlencode "text=${MSG}"
                '''
            }
        }

        stage('Checkout source') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                echo 'Static HTML project: no external dependencies to install.'
            }
        }

        stage('Build project') {
            steps {
                sh '''
                    set -eu
                    test -s index.html
                    mkdir -p dist
                    cp index.html dist/index.html
                '''
                archiveArtifacts artifacts: 'dist/**', fingerprint: true
            }
        }

        stage('Deploy to GitHub Pages') {
            when {
                branch 'main'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-https',
                    usernameVariable: 'GH_USER',
                    passwordVariable: 'GH_TOKEN'
                )]) {
                    sh '''
                        set -eu
                        export GIT_CONFIG_COUNT=1
                        export GIT_CONFIG_KEY_0='http.https://github.com/.extraheader'
                        export GIT_CONFIG_VALUE_0="AUTHORIZATION: basic $(printf '%s' "$GH_USER:$GH_TOKEN" | base64 | tr -d '\\n')"

                        deploy_dir="$(mktemp -d)"
                        trap 'rm -rf "$deploy_dir"' EXIT

                        git -C "$deploy_dir" init --quiet -b "$GH_PAGES_BRANCH"
                        git -C "$deploy_dir" remote add origin "$GH_REPO"

                        if git ls-remote --exit-code --heads origin "$GH_PAGES_BRANCH" >/dev/null 2>&1; then
                            git -C "$deploy_dir" fetch --depth=1 origin "$GH_PAGES_BRANCH"
                            git -C "$deploy_dir" reset --hard FETCH_HEAD
                        fi

                        find "$deploy_dir" -mindepth 1 -maxdepth 1 ! -name .git -exec rm -rf {} +
                        cp -R dist/. "$deploy_dir/"
                        git -C "$deploy_dir" add --all

                        if git -C "$deploy_dir" diff --cached --quiet; then
                            echo 'GitHub Pages is already up to date.'
                        else
                            git -C "$deploy_dir" -c user.name='Jenkins' -c user.email='jenkins[bot]@users.noreply.github.com' commit -m "Deploy build ${BUILD_NUMBER}"
                            git -C "$deploy_dir" push origin "$GH_PAGES_BRANCH"
                        fi
                    '''
                }
            }
        }
    }

    post {
        success {
            sh '''
                set -eu
                MSG=$(printf '✅ DEPLOY SUCCESS\nProject: %s\nBranch: %s\nURL: %s' "$PROJECT_NAME" "$PROJECT_BRANCH" "$APP_URL")
                curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    --data-urlencode "text=${MSG}"
            '''
            echo 'BUILD SUCCESS: Pipeline completed successfully.'
        }
        failure {
            sh '''
                set -eu
                MSG=$(printf '❌ DEPLOY FAILED\nProject: %s\nBranch: %s\nPlease check Jenkins.' "$PROJECT_NAME" "$PROJECT_BRANCH")
                curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
                    -d "chat_id=${TELEGRAM_CHAT_ID}" \
                    --data-urlencode "text=${MSG}"
            '''
            echo 'BUILD FAILED: Check the stage logs for details.'
        }
    }
}
