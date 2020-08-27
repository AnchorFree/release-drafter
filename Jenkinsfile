pipeline {
    agent none

    environment {
        BRANCH  = "${env.CHANGE_BRANCH ? env.CHANGE_BRANCH : env.BRANCH_NAME}"
        // A workaround to change anchorfree to AnchorFree for git action
        REPO            = "https://github.com/AnchorFree/release-drafter.git"
        UPSTREAM_REPO   = "https://github.com/release-drafter/release-drafter.git"
    }

    stages {
        stage('Sync with upstream git repo') {
            agent { label 'general' }
            when {
                beforeAgent true
                // Run only if a user triggered a job manually
                triggeredBy 'UserIdCause'
            }
            steps {
                script {
                        println(
                            gitSyncFork(BRANCH, REPO, UPSTREAM_REPO)
                        )
                    }
                }
            }
        }
    }
}

/**
* Syncing the fork with upstream
*
* @param branch           Git branch where Head should be pushed into the fork repo
* @param repo             Git remote origin (the fork)
* @param upstreamRepo     Git remote upstream
*/
def gitSyncFork(
                String branch,
                String repo
                String upstreamRepo
) {
    cmd = null
    wrap([
        $class: 'VaultBuildWrapper',
        vaultSecrets: [
            [$class: 'VaultSecret',
                path: "secret/devops/jenkins-cloud/github",
                engineVersion: 1,
                secretValues: [
                    [$class: 'VaultSecretValue',
                    envVar: 'GIT_USERNAME',
                    vaultKey: 'user'],
                    [$class: 'VaultSecretValue',
                    envVar: 'GIT_PASSWORD',
                    vaultKey: 'pass']
            ]]
    ]]) {
        cmd = sh(
            label: "Syncing the fork with upstream",
            script: """
            # Set git credential helper
            git config credential.helper "/bin/bash ./git-creds-helper.sh"
            # Set upstream
            git remote add upstream ${upstreamRepo}
            # Fetch upstream
            git fetch upstream
            # Merge upstream changes from master if any
            git merge upstream/master
            # Push changes
            git remote set-url origin ${repo}
            git push origin HEAD:${branch}
            """,
            returnStdout: true
        )
    }
    return cmd
}
