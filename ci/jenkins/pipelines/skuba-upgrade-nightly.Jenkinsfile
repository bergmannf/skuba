/**
 * This pipeline verifies skuba node/cluster upgrade plan/apply
 */

pipeline {
   agent { node { label 'caasp-team-private' } }

   environment {
        OPENSTACK_OPENRC = credentials('ecp-openrc')
        GITHUB_TOKEN = credentials('github-token')
        VMWARE_ENV_FILE = credentials('vmware-env')
   }

   stages {
        stage('Getting Ready For Cluster Deployment') { steps {
            sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-prepare-${BUILD_NUMBER}; make -f skuba/ci/Makefile pre_deployment", label: 'Pre Deployment')
        } }

        stage('Run All Skuba Upgrade Tests In Parallel') {
            parallel {
                stage('Run Skuba Upgrade Plan All fine Test') {
                    steps {
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-plan-all-fine-${BUILD_NUMBER}; make -f skuba/ci/Makefile test_upgrade_plan_all_fine", label: 'Skuba Upgrade Plan All fine')
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-plan-all-fine-${BUILD_NUMBER}; make --keep-going -f skuba/ci/Makefile post_run", label: 'Post Run')
                    }
                }

                stage('Run Skuba Upgrade Plan from previous Test') {
                    steps {
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-plan-from-previous-${BUILD_NUMBER}; make -f skuba/ci/Makefile test_upgrade_plan_from_previous", label: 'Skuba Upgrade Plan From Previous')
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-plan-from-previous-${BUILD_NUMBER}; make --keep-going -f skuba/ci/Makefile post_run", label: 'Post Run')
                    }
                }

                stage('Run Skuba Upgrade Apply All fine Test') {
                    steps {
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-apply-all-fine-${BUILD_NUMBER}; make -f skuba/ci/Makefile test_upgrade_apply_all_fine", label: 'Skuba Upgrade Apply All fine')
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-apply-all-fine-${BUILD_NUMBER}; make --keep-going -f skuba/ci/Makefile post_run", label: 'Post Run')
                    }
                }

                stage('Run Skuba Upgrade Apply from previous Test') {
                    steps {
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-apply-from-previous-${BUILD_NUMBER}; make -f skuba/ci/Makefile test_upgrade_apply_from_previous", label: 'Skuba Upgrade Apply From Previous')
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-apply-from-previous-${BUILD_NUMBER}; make --keep-going -f skuba/ci/Makefile post_run", label: 'Post Run')
                    }
                }

                stage('Run Skuba Upgrade Apply With User Lock') {
                    steps {
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-apply-user-lock-${BUILD_NUMBER}; make -f skuba/ci/Makefile test_upgrade_apply_user_lock", label: 'Skuba Upgrade Apply With User Lock')
                        sh(script: "export TERRAFORM_STACK_NAME=${JOB_NAME}-apply-user-lock-${BUILD_NUMBER}; make --keep-going -f skuba/ci/Makefile post_run", label: 'Post Run')
                    }
                }
            }
        }
   }

   post {
       always {
           zip(archive: true, dir: 'testrunner_logs', zipFile: 'testrunner_logs.zip')
       }
       cleanup {
           dir("${WORKSPACE}") {
               deleteDir()
           }
       }
    }
}
