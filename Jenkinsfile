/*
# Copyright (c) 2017 The University of Manchester
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
*/
pipeline {
    agent {
        docker { image 'python3.12' }
    }
    environment {
        // This is where 'pip install --user' puts things
        PATH = "$HOME/.local/bin:$PATH"
        GLOBAL_REPORTS = "${WORKSPACE}/global_reports"
        THE_JOB = getJobName()
    }
    options {
        skipDefaultCheckout true
    }
    stages {
        stage('Clean and Checkout') {
            steps {
                sh 'echo "Job name is $THE_JOB (from $JOB_NAME)"'
                sh 'rm -rf ${WORKSPACE}/*'
                sh 'rm -rf ${WORKSPACE}/.[a-zA-Z0-9]*'
                sh 'mkdir ${WORKSPACE}/global_reports'
                dir('IntegrationTests') {
                    checkout scm
                }
            }
        }
        stage('Before Install') {
            environment {
                TRAVIS_BRANCH = getGitBranchName()
                GITHUB_TOKEN = credentials('98413a50-3a5d-4ca9-b672-5bfc168f01a5')
            }
            steps {
                // Verify the branch
                sh 'echo "Branch is $TRAVIS_BRANCH"'
                // remove all directories left if Jenkins ended badly
                sh 'git clone https://github.com/SpiNNakerManchester/SupportScripts.git support'
                sh 'pip3 install --upgrade setuptools wheel'
                sh 'pip install --user --upgrade pip'
                sh 'pip install virtualenv'
                // SpiNNakerManchester internal dependencies; development mode
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNUtils.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNMachine.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNMan.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/PACMAN.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/spalloc.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNakerGraphFrontEnd.git'
                // C dependencies
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/spinnaker_tools.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/spinn_common.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNFrontEndCommon.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/sPyNNaker.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/Visualiser.git'
                // Java dependencies
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/JavaSpiNNaker'
                // scripts
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/PyNNExamples.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/sPyNNakerNewModelTemplate.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/microcircuit_model.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNGym.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/MarkovChainMonteCarlo.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/TestBase.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNaker_PDP2.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNakerJupyterExamples.git'
                sh 'support/gitclone.sh https://$GITHUB_TOKEN@github.com/SpiNNakerManchester/TSPonSpiNNaker.git'
            }
        }
        stage('Install') {
            environment {
                SPINN_DIRS = "${workspace}/spinnaker_tools"
                NEURAL_MODELLING_DIRS = "${workspace}/sPyNNaker/neural_modelling"
            }
            steps {
                // Make a virtualenv
                sh 'virtualenv pyenv'
                run_in_pyenv('pip3 install --upgrade setuptools wheel')
                run_in_pyenv('pip install --upgrade pip')

                // Install SpiNNUtils first as needed for C build
                run_in_pyenv('pip install ./SpiNNUtils[test]')
                // C Build next as builds files to be installed in Python
                run_in_pyenv('make -C $SPINN_DIRS')
                run_in_pyenv('make -C spinn_common install')
                run_in_pyenv('make -C SpiNNFrontEndCommon/c_common')
                run_in_pyenv('make -C SpiNNFrontEndCommon/c_common install')
                run_in_pyenv('make -C sPyNNaker/neural_modelling')
                run_in_pyenv('make -C sPyNNakerNewModelTemplate/c_models')
                run_in_pyenv('make -C SpiNNakerGraphFrontEnd/gfe_examples')
                run_in_pyenv('make -C SpiNNakerGraphFrontEnd/gfe_integration_tests/')
                run_in_pyenv('make -C SpiNNGym/c_code')
                run_in_pyenv('make -C MarkovChainMonteCarlo/c_models')
                run_in_pyenv('make -C SpiNNaker_PDP2/c_code')
                run_in_pyenv('make -C Visualiser')
                run_in_pyenv('make -C TSPonSpiNNaker/spinnaker_c')
                // Python install
                run_in_pyenv('pip install ./SpiNNMachine[test]')
                run_in_pyenv('pip install ./SpiNNMan[test]')
                run_in_pyenv('pip install ./PACMAN[test]')
                run_in_pyenv('pip install ./spalloc[test]')
                run_in_pyenv('pip install ./SpiNNFrontEndCommon[test]')
                run_in_pyenv('pip install ./TestBase[test]')
                run_in_pyenv('pip install ./sPyNNaker[test]')
                run_in_pyenv('pip install ./sPyNNakerNewModelTemplate[test]')
                run_in_pyenv('pip install ./SpiNNakerGraphFrontEnd[test]')
                run_in_pyenv('pip install ./SpiNNGym[test]')
                run_in_pyenv('pip install ./MarkovChainMonteCarlo[test]')
                // Due to the binaries being outside of the package
                run_in_pyenv('pip install -e ./SpiNNaker_PDP2[test]')
                run_in_pyenv('pip install ./Visualiser[test]')
                run_in_pyenv('pip install ./TSPonSpiNNaker[test]')
                // no install SpiNNakerJupyterExamples
                run_in_pyenv('python -m spynnaker.pyNN.setup_pynn')
                // Additional requirements for testing here
                // coverage version capped due to https://github.com/nedbat/coveragepy/issues/883
                run_in_pyenv('pip install nbmake python-coveralls "coverage>=5.0.0" pytest-instafail pytest-xdist pytest-progress pytest-forked pytest-timeout')
                run_in_pyenv('pip freeze')
                // Java install, not server
                sh 'mvn package -B -f JavaSpiNNaker -pl -SpiNNaker-allocserv'
            }
        }
        stage('Delete install sources') {
            steps {
                sh 'rm -r spinn_common'
                sh 'rm -r SpiNNUtils/spinn_utilities'
                sh 'rm -r SpiNNUtils/build'
                sh 'rm -r SpiNNMachine/spinn_machine'
                sh 'rm -r SpiNNMachine/build'
                sh 'rm -r SpiNNMan/spinnman'
                sh 'rm -r SpiNNMan/build'
                sh 'rm -r PACMAN/pacman'
                sh 'rm -r PACMAN/pacman_test_objects'
                sh 'rm -r PACMAN/build'
                sh 'rm -r spalloc/spalloc_client'
                sh 'rm -r spalloc/build'
                sh 'rm -r SpiNNFrontEndCommon/spinn_front_end_common'
                sh 'rm -r SpiNNFrontEndCommon/c_common'
                sh 'rm -r SpiNNFrontEndCommon/build'
                sh 'rm -r TestBase/spinnaker_testbase'
                sh 'rm -r TestBase/build'
                sh 'rm -r sPyNNaker/spynnaker'
                sh 'rm -r sPyNNaker/neural_modelling'
                sh 'rm -r sPyNNaker/build'
                sh 'rm -r sPyNNakerNewModelTemplate/python_models8'
                sh 'rm -r sPyNNakerNewModelTemplate/build'
                sh 'rm -r SpiNNakerGraphFrontEnd/spinnaker_graph_front_end'
                sh 'rm -r SpiNNakerGraphFrontEnd/build'
                sh 'rm -r SpiNNGym/spinn_gym'
                sh 'rm -r SpiNNGym/c_code'
                sh 'rm -r SpiNNGym/build'
                sh 'rm -r MarkovChainMonteCarlo/mcmc'
                sh 'rm -r MarkovChainMonteCarlo/build'
                // Due to the binaries being outside of the package
                //NO remove SpiNNaker_PDP2
                sh 'rm -r Visualiser/visualiser_example_binaries'
                sh 'rm -r Visualiser/build'
                // No remove SpiNNakerJupyterExamples
                sh 'rm -r TSPonSpiNNaker/spinnaker_c'
                sh 'rm -r TSPonSpiNNaker/build'
            }
        }
        stage('Before Script') {
            steps {
                // Prepare coverage
                sh 'rm -f coverage.xml'
                sh 'rm -f .coverage'
                sh 'echo "[run]" > .coveragerc'
                sh 'echo "parallel = True" >> .coveragerc'
                // Create a directory for test outputs
                sh 'mkdir junit/'
            }
        }
        stage('Unit Tests') {
            steps {
                // Empty config is sometimes needed in unit tests
                sh 'echo "# Empty config" >  ~/.spinnaker.cfg'
                create_spynnaker_config()
                create_gfe_config()
                run_pytest('SpiNNUtils/unittests', 1200, 'SpiNNUtils', 'unit', 'auto')
                run_pytest('SpiNNMachine/unittests', 1200, 'SpiNNMachine', 'unit', 'auto')
                run_pytest('SpiNNMan/unittests', 1200, 'SpiNNMan', 'unit', 'auto')
                run_pytest('PACMAN/unittests', 1200, 'PACMAN', 'unit', 'auto')
                run_pytest('spalloc/tests', 1200, 'spalloc', 'unit', '1')
                run_pytest('SpiNNFrontEndCommon/unittests SpiNNFrontEndCommon/fec_integration_tests', 1200, 'SpiNNFrontEndCommon', 'unit', 'auto')
                run_pytest('sPyNNaker/unittests', 1200, 'sPyNNaker', 'unit', 'auto')
                run_pytest('SpiNNakerGraphFrontEnd/unittests', 1200, 'SpiNNakerGraphFrontEnd', 'unit', 'auto')
                run_pytest('PyNNExamples/unittests', 1200, 'PyNNExamples', 'unit', 'auto')
                run_pytest('SpiNNGym/unittests', 1200, 'SpiNNGym', 'unit', 'auto')
                run_pytest('MarkovChainMonteCarlo/unittests', 1200, 'MarkovChainMonteCarlo', 'unit', 'auto')
                run_pytest('SpiNNaker_PDP2/unittests', 1200, 'SpiNNaker_PDP2', 'unit', 'auto')
                run_pytest('TSPonSpiNNaker/unittests', 1200, 'TSPonSpiNNaker', 'unit', 'auto')
                run_in_pyenv('python -m spinn_utilities.executable_finder')
                // no SpiNNakerJupyterExamples
            }
        }
        stage('Run sPyNNaker Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_pytest('sPyNNaker/spynnaker_integration_tests/', 24000, 'sPyNNaker_Integration_Tests', 'integration', 'auto')
                }
            }
        }
        stage('Run GFE Integeration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_gfe_config()
                    run_in_pyenv('python SpiNNakerGraphFrontEnd/gfe_integration_tests/script_builder.py')
                    run_pytest('SpiNNakerGraphFrontEnd/gfe_integration_tests/', 3600, 'GFE_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run PyNNExamples Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_in_pyenv('python PyNNExamples/integration_tests/script_builder.py')
                    run_pytest('PyNNExamples/integration_tests', 3600, 'PyNNExamples_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run sPyNNakerNewModelTemplate Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_in_pyenv('python sPyNNakerNewModelTemplate/nmt_integration_tests/script_builder.py')
                    run_pytest('sPyNNakerNewModelTemplate/nmt_integration_tests', 3600, 'sPyNNakerNewModelTemplate_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run microcircuit_model Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_pytest('microcircuit_model/integration_tests', 12000, 'microcircuit_model_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run SpiNNGym Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_in_pyenv('python SpiNNGym/integration_tests/script_builder.py')
                    run_pytest('SpiNNGym/integration_tests', 3600, 'SpiNNGym_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run MarkovChainMonteCarlo Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_gfe_config()
                    run_in_pyenv('python MarkovChainMonteCarlo/mcmc_integration_tests/script_builder.py')
                    run_pytest('MarkovChainMonteCarlo/mcmc_integration_tests', 3600, 'MarkovChainMonteCarlo_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run SpiNNaker_PDP2 Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_gfe_config()
                    run_in_pyenv('python SpiNNaker_PDP2/integration_tests/script_builder.py')
                    run_pytest('SpiNNaker_PDP2/integration_tests', 3600, 'SpiNNaker_PDP2_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run Visualiser Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_pytest('Visualiser/visualiser_integration_tests', 12000, 'visualiser_Integration', 'integration', 'auto')
                }
            }
        }
        stage("SpiNNakerJupyterExamples") {
            steps {
                create_spynnaker_config()
                run_in_pyenv("pytest -n auto --nbmake SpiNNakerJupyterExamples/**/*.ipynb SpiNNakerJupyterExamples/**/**/*.ipynb")
            }
        }
        stage('Run TSPonSpiNNaker Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_gfe_config()
                    run_in_pyenv('python TSPonSpiNNaker/integration_tests/script_builder.py')
                    run_pytest('TSPonSpiNNaker/integration_tests', 3600, 'TSPonSpiNNaker', 'integration', 'auto')
                }
            }
        }
       /*
        stage('Run Whole Machine Tests') {
            when {
                environment name: 'THE_JOB', value: 'Integration_Tests_Cron_Job'
            }
            environment {
                SPALLOC_PASSWORD = credentials('spalloc-password')
            }
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    create_spynnaker_config()
                    run_pytest('sPyNNaker/test_whole_board', 12000, 'test_whole_machine', 'integration', '16')
                }
            }
        }
        */
        stage('Reports') {
            steps {
                run_in_pyenv('python -m spinn_utilities.executable_finder')
                sh 'find ${WORKSPACE}/global_reports -print -exec cat \\{\\}  \\;'
            }
        }
        stage('Check Destroyed') {
            steps {
                run_in_pyenv('python -m spinnaker_testbase.test_no_job_destroy')
            }
        }
    }
    post {
        always {
            script {
                def recipients = ""
                if (env.THE_JOB.equals("Integration_Tests_Cron_Job")) {
                    recipients = '$DEFAULT_RECIPIENTS'
                } else {
                    recipients = emailextrecipients([culprits(), developers(), buildUser()])
                    if (recipients == "") {
                        recipients = '$DEFAULT_RECIPIENTS'
                    }
                }
                emailext subject: '$DEFAULT_SUBJECT',
                    body: '$DEFAULT_CONTENT',
                    to: recipients,
                    replyTo: '$DEFAULT_REPLYTO'
            }
        }
        success {
            junit 'junit/*.xml'
            recordCoverage(tools: [[parser: 'COBERTURA', pattern: '*_cov.xml']],
                id: 'coverage', name: 'Coverage',
                sourceCodeRetention: 'EVERY_BUILD')
        }
    }
}

def run_pytest(String tests, int timeout, String results, String covfile, String threads) {
    def resfile = 'junit/' + results + '.xml'
    covfile += '_cov.xml'
    sh 'echo "<testsuite tests="0"></testsuite>" > ' + resfile
    run_in_pyenv('py.test ' + tests +
        ' -rs -n ' + threads + ' --forked --show-progress --maxschedchunk=1 ' +
        '--cov-config=.coveragerc --cov-branch ' +
        '--cov spynnaker --cov spinn_front_end_common --cov pacman ' +
        '--cov spinnman --cov spinn_machine --cov spalloc ' +
        '--cov spinn_utilities --cov spinnaker_graph_front_end ' +
        '--junitxml ' + resfile + ' --cov-report xml:' + covfile + ' --cov-append ' +
        '--timeout ' + timeout + ' --log-level=INFO ')
}

def create_spynnaker_config() {
    // Write a sPyNNaker config file for spalloc and java use
    sh '''
        if [[ ! -f ~/.spynnaker.cfg ]]
        then
            echo "[Machine]" > ~/.spynnaker.cfg
            echo "spalloc_server = 10.11.192.11" >> ~/.spynnaker.cfg
            echo "spalloc_user = Jenkins" >> ~/.spynnaker.cfg
            echo "enable_advanced_monitor_support = True" >> ~/.spynnaker.cfg
            echo "[Java]" >> ~/.spynnaker.cfg
            echo "use_java = True" >> ~/.spynnaker.cfg
            echo "java_call=/usr/bin/java" >> ~/.spynnaker.cfg
            echo "java_properties=-Dspinnaker.parallel_tasks=10" >> ~/.spynnaker.cfg
            printf "java_spinnaker_path=" >> ~/.spynnaker.cfg
            pwd >> ~/.spynnaker.cfg
        fi
    '''
}

def create_gfe_config() {
    // Write a GFE config file for spalloc and java use
    sh '''
        if [[ ! -f ~/.spiNNakerGraphFrontEnd.cfg ]]
        then
            echo "[Machine]" > ~/.spiNNakerGraphFrontEnd.cfg
            echo "spalloc_server = 10.11.192.11" >> ~/.spiNNakerGraphFrontEnd.cfg
            echo "spalloc_user = Jenkins" >> ~/.spiNNakerGraphFrontEnd.cfg
            echo "enable_advanced_monitor_support = True" >> ~/.spiNNakerGraphFrontEnd.cfg
            echo "[Java]" >> ~/.spiNNakerGraphFrontEnd.cfg
            echo "use_java = True" >> ~/.spiNNakerGraphFrontEnd.cfg
            echo "java_call=/usr/bin/java" >> ~/.spiNNakerGraphFrontEnd.cfg
            echo "java_properties=-Dspinnaker.parallel_tasks=10" >> ~/.spiNNakerGraphFrontEnd.cfg
            printf "java_spinnaker_path=" >> ~/.spiNNakerGraphFrontEnd.cfg
            pwd >> ~/.spiNNakerGraphFrontEnd.cfg
        fi
    '''
}

def run_in_pyenv(String command) {
    sh script:'source ${WORKSPACE}/pyenv/bin/activate && ' + command, label: command
}

def getGitBranchName() {
    if (env.BRANCH_NAME) {
        return env.BRANCH_NAME;
    }
    dir('IntegrationTests') {
        return sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
    }
}

def getJobName() {
    def index = env.JOB_NAME.lastIndexOf('/')
    if (index == -1) {
        index = env.JOB_NAME.length()
    }

    return env.JOB_NAME.substring(0, index)
}
