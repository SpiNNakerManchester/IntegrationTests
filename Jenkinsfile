/*
# Copyright (c) 2017-2019 The University of Manchester
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
*/
pipeline {
    agent {
        docker { image 'python3.8' }
    }
    environment {
        // This is where 'pip install --user' puts things
        PATH = "$HOME/.local/bin:$PATH"
        BINARY_LOGS_DIR = "${workspace}"
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
                dir('IntegrationTests') {
                    checkout scm
                }
            }
        }
        stage('Before Install') {
            environment {
                TRAVIS_BRANCH = getGitBranchName()
            }
            steps {
                // Verify the branch
                sh 'echo "Branch is $TRAVIS_BRANCH"'
                // remove all directories left if Jenkins ended badly
                sh 'git clone https://github.com/SpiNNakerManchester/SupportScripts.git support'
                sh 'pip3 install --upgrade "setuptools<59.8" wheel'
                sh 'pip install --user --upgrade pip'
                sh 'pip install virtualenv'
                // SpiNNakerManchester internal dependencies; development mode
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNUtils.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNMachine.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/SpiNNMan.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/PACMAN.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/DataSpecification.git'
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
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/IntroLab.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/PyNN8Examples.git'
                sh 'support/gitclone.sh https://github.com/SpiNNakerManchester/sPyNNaker8NewModelTemplate.git'
                sh 'support/gitclone.sh git@github.com:SpiNNakerManchester/microcircuit_model.git'
                sh 'support/gitclone.sh git@github.com:SpiNNakerManchester/SpiNNGym.git'
                sh 'support/gitclone.sh git@github.com:SpiNNakerManchester/MarkovChainMonteCarlo.git'
                sh 'support/gitclone.sh git@github.com:SpiNNakerManchester/TestBase.git'
                sh 'support/gitclone.sh git@github.com:SpiNNakerManchester/SpiNNaker_PDP2.git'
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
                // Install SpiNNUtils first as needed for C build
                run_in_pyenv('cd SpiNNUtils && python setup.py develop')
                // C Build next as builds files to be installed in Python
                run_in_pyenv('make -C $SPINN_DIRS')
                run_in_pyenv('make -C spinn_common install')
                run_in_pyenv('make -C SpiNNFrontEndCommon/c_common')
                run_in_pyenv('make -C SpiNNFrontEndCommon/c_common install')
                run_in_pyenv('make -C sPyNNaker/neural_modelling')
                run_in_pyenv('make -C sPyNNaker8NewModelTemplate/c_models')
                run_in_pyenv('make -C SpiNNakerGraphFrontEnd/gfe_examples')
                run_in_pyenv('make -C SpiNNakerGraphFrontEnd/gfe_integration_tests/')
                run_in_pyenv('make -C SpiNNGym/c_code')
                run_in_pyenv('make -C MarkovChainMonteCarlo/c_models')
                run_in_pyenv('make -C SpiNNaker_PDP2/c_code')
                run_in_pyenv('make -C Visualiser')
                // Python install
                run_in_pyenv('cd SpiNNMachine && python setup.py develop')
                run_in_pyenv('cd SpiNNMan && python setup.py develop')
                run_in_pyenv('cd PACMAN && python setup.py develop')
                run_in_pyenv('cd DataSpecification && python setup.py develop')
                run_in_pyenv('cd spalloc && python setup.py develop')
                run_in_pyenv('cd SpiNNFrontEndCommon && python setup.py develop')
                run_in_pyenv('cd sPyNNaker && python setup.py develop')
                run_in_pyenv('cd sPyNNaker8NewModelTemplate && python ./setup.py develop')
                run_in_pyenv('cd SpiNNakerGraphFrontEnd && python ./setup.py develop')
                run_in_pyenv('cd SpiNNGym && python ./setup.py develop')
                run_in_pyenv('cd MarkovChainMonteCarlo && python ./setup.py develop')
                run_in_pyenv('cd TestBase && python ./setup.py develop')
                run_in_pyenv('cd SpiNNaker_PDP2 && python ./setup.py develop')
                run_in_pyenv('cd Visualiser && python ./setup.py develop')
                run_in_pyenv('python -m spynnaker.pyNN.setup_pynn')
                // Test requirements
                run_in_pyenv('pip install -r SpiNNMachine/requirements-test.txt')
                run_in_pyenv('pip install -r SpiNNMan/requirements-test.txt')
                run_in_pyenv('pip install -r PACMAN/requirements-test.txt')
                run_in_pyenv('pip install -r DataSpecification/requirements-test.txt')
                run_in_pyenv('pip install -r spalloc/requirements-test.txt')
                run_in_pyenv('pip install -r SpiNNFrontEndCommon/requirements-test.txt')
                run_in_pyenv('pip install -r sPyNNaker/requirements-test.txt')
                run_in_pyenv('pip install -r SpiNNakerGraphFrontEnd/requirements-test.txt')
                run_in_pyenv('pip install -r SpiNNGym/requirements-test.txt')
                run_in_pyenv('pip install -r MarkovChainMonteCarlo/requirements-test.txt')
                run_in_pyenv('pip install -r SpiNNaker_PDP2/requirements-test.txt')
                // Additional requirements for testing here
                // coverage version capped due to https://github.com/nedbat/coveragepy/issues/883
                run_in_pyenv('pip install python-coveralls "coverage>=5.0.0"')
                run_in_pyenv('pip install pytest-instafail "pytest-xdist==1.34.0"')
                // Java install
                sh 'mvn -f JavaSpiNNaker package'
            }
        }
        stage('Before Script') {
            steps {
                // Write a sPyNNaker config file for spalloc and java use
                sh 'echo "[Machine]" > ~/.spynnaker.cfg'
                sh 'echo "spalloc_server = 10.11.192.11" >> ~/.spynnaker.cfg'
                sh 'echo "spalloc_user = Jenkins" >> ~/.spynnaker.cfg'
                sh 'echo "enable_advanced_monitor_support = True" >> ~/.spynnaker.cfg'
                sh 'echo "[Java]" >> ~/.spynnaker.cfg'
                sh 'echo "use_java = True" >> ~/.spynnaker.cfg'
                sh 'echo "java_call=/usr/bin/java" >> ~/.spynnaker.cfg'
                sh 'echo "java_properties=-Dspinnaker.parallel_tasks=10" >> ~/.spynnaker.cfg'
                sh 'printf "java_spinnaker_path=" >> ~/.spynnaker.cfg'
                sh 'pwd >> ~/.spynnaker.cfg'
                // Write a GFE config file for spalloc and java use
                sh 'echo "[Machine]" > ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "spalloc_server = 10.11.192.11" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "spalloc_user = Jenkins" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "enable_advanced_monitor_support = True" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "[Java]" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "use_java = True" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "java_call=/usr/bin/java" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'echo "java_properties=-Dspinnaker.parallel_tasks=10" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'printf "java_spinnaker_path=" >> ~/.spiNNakerGraphFrontEnd.cfg'
                sh 'pwd >> ~/.spiNNakerGraphFrontEnd.cfg'
                // Prepare coverage
                sh 'rm -f coverage.xml'
                sh 'rm -f .coverage'
                sh 'echo "[run]" > .coveragerc'
                sh 'echo "parallel = True" >> .coveragerc'
                // Prepare for unit tests
                sh 'echo "# Empty config" >  ~/.spinnaker.cfg'
                // Create a directory for test outputs
                sh 'mkdir junit/'
            }
        }
        stage('Unit Tests') {
            steps {
                run_pytest('SpiNNUtils/unittests', 1200, 'SpiNNUtils', 'unit', 'auto')
                run_pytest('SpiNNMachine/unittests', 1200, 'SpiNNMachine', 'unit', 'auto')
                run_pytest('SpiNNMan/unittests', 1200, 'SpiNNMan', 'unit', 'auto')
                run_pytest('PACMAN/unittests', 1200, 'PACMAN', 'unit', 'auto')
                run_pytest('spalloc/tests', 1200, 'spalloc', 'unit', '1')
                run_pytest('DataSpecification/unittests', 1200, 'DataSpecification', 'unit', 'auto')
                run_pytest('SpiNNFrontEndCommon/unittests SpiNNFrontEndCommon/fec_integration_tests', 1200, 'SpiNNFrontEndCommon', 'unit', 'auto')
                run_pytest('sPyNNaker/unittests', 1200, 'sPyNNaker', 'unit', 'auto')
                run_pytest('SpiNNakerGraphFrontEnd/unittests', 1200, 'SpiNNakerGraphFrontEnd', 'unit', 'auto')
                run_pytest('PyNN8Examples/unittests', 1200, 'PyNN8Examples', 'unit', 'auto')
                run_pytest('SpiNNGym/unittests', 1200, 'SpiNNGym', 'unit', 'auto')
                run_pytest('MarkovChainMonteCarlo/unittests', 1200, 'SpiNNaker_PDP2', 'unit', 'auto')
                run_pytest('SpiNNaker_PDP2/unittests', 1200, 'SpiNNaker_PDP2', 'unit', 'auto')
                run_in_pyenv('python -m spinn_utilities.executable_finder')
            }
        }
        stage('Run sPyNNaker Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_pytest('sPyNNaker/spynnaker_integration_tests/', 24000, 'sPyNNaker_Integration_Tests', 'integration', 'auto')
                }
            }
        }
        stage('Run GFE Integeration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_in_pyenv('python SpiNNakerGraphFrontEnd/gfe_integration_tests/script_builder.py')
                    run_pytest('SpiNNakerGraphFrontEnd/gfe_integration_tests/', 3600, 'GFE_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run IntroLab Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_in_pyenv('python IntroLab/integration_tests/script_builder.py')
                    run_pytest('IntroLab/integration_tests', 3600, 'IntroLab_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run PyNN8Examples Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                  run_in_pyenv('python PyNN8Examples/integration_tests/script_builder.py')
                  run_pytest('PyNN8Examples/integration_tests', 3600, 'PyNN8Examples_Integration', 'integration', 'auto')
              }
            }
        }
        stage('Run sPyNNaker8NewModelTemplate Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_in_pyenv('python sPyNNaker8NewModelTemplate/nmt_integration_tests/script_builder.py')
                    run_pytest('sPyNNaker8NewModelTemplate/nmt_integration_tests', 3600, 'sPyNNaker8NewModelTemplate_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run microcircuit_model Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_pytest('microcircuit_model/integration_tests', 12000, 'microcircuit_model_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run SpiNNGym Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_in_pyenv('python SpiNNGym/integration_tests/script_builder.py')
                    run_pytest('SpiNNGym/integration_tests', 3600, 'SpiNNGym_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run MarkovChainMonteCarlo Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_in_pyenv('python MarkovChainMonteCarlo/mcmc_integration_tests/script_builder.py')
                    run_pytest('MarkovChainMonteCarlo/mcmc_integration_tests', 3600, 'MarkovChainMonteCarlo_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run SpiNNaker_PDP2 Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_in_pyenv('python SpiNNaker_PDP2/integration_tests/script_builder.py')
                    run_pytest('SpiNNaker_PDP2/integration_tests', 3600, 'SpiNNaker_PDP2_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run Visualiser Integration Tests') {
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_pytest('Visualiser/visualiser_integration_tests', 12000, 'visualiser_Integration', 'integration', 'auto')
                }
            }
        }
        stage('Run Whole Machine Tests') {
            when {
                environment name: 'THE_JOB', value: 'Integration_Tests_Cron_Job'
            }
            steps {
                catchError(stageResult: 'FAILURE', catchInterruptions: false) {
                    run_pytest('sPyNNaker/test_whole_board', 12000, 'test_whole_machine', 'integration', '16')
                }
            }
        }
        stage('Reports') {
            steps {
                sh 'find . -maxdepth 3 -type f -wholename "*/reports/*" -print -exec cat \\{\\}  \\;'
                run_in_pyenv('python -m spinn_utilities.executable_finder')
            }
        }
        stage('Check Destroyed') {
            steps {
                run_in_pyenv('py.test TestBase/spinnaker_testbase/test_no_job_destroy.py --forked --instafail --timeout 120')
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
            cobertura coberturaReportFile: '*_cov.xml', enableNewApi: true
        }
    }
}

def run_pytest(String tests, int timeout, String results, String covfile, String threads) {
    def resfile = 'junit/' + results + '.xml'
    covfile += '_cov.xml'
    sh 'echo "<testsuite tests="0"></testsuite>" > ' + resfile
    run_in_pyenv('py.test ' + tests +
        ' -rs -n ' + threads + ' --forked --show-progress --cov-config=.coveragerc --cov-branch ' +
        '--cov spynnaker8 --cov spynnaker --cov spinn_front_end_common --cov pacman ' +
        '--cov data_specification --cov spinnman --cov spinn_machine --cov spalloc ' +
        '--cov spinn_utilities --cov spinnaker_graph_front_end ' +
        '--junitxml ' + resfile + ' --cov-report xml:' + covfile + ' --cov-append ' +
        '--timeout ' + timeout + ' --log-level=INFO ')
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
