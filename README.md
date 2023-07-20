[![Jenkins tests](https://img.shields.io/jenkins/tests?compact_message&jobUrl=http%3A%2F%2Fapollo.cs.man.ac.uk%3A8080%2Fjob%2FIntegration%2520Tests%2Fjob%2Fmain%2F&label=integration%20tests)](http://apollo.cs.man.ac.uk:8080/blue/organizations/jenkins/Integration%20Tests/activity/)

# IntegrationTests
Integration Tests unified over all repositories

In particular, tests:
* [sPyNNaker](https://github.com/SpiNNakerManchester/sPyNNaker)
* [SpiNNaker Graph Front End](https://github.com/SpiNNakerManchester/SpiNNakerGraphFrontEnd)
* [sPyNNaker New Model Template](https://github.com/SpiNNakerManchester/sPyNNaker8NewModelTemplate)
* [PyNN8 Examples](https://github.com/SpiNNakerManchester/PyNN8Examples)
* [Introductory Lab Scripts](https://github.com/SpiNNakerManchester/IntroLab)
* [Cortical microcircuit](https://github.com/SpiNNakerManchester/microcircuit_model)
* [SpiNNGym](https://github.com/SpiNNakerManchester/SpiNNGym)
* [Markov Chain Monte Carlo](https://github.com/SpiNNakerManchester/MarkovChainMonteCarlo)
* [SpiNNaker_PDP2](https://github.com/SpiNNakerManchester/SpiNNaker_PDP2)
* [Visualiser](https://github.com/SpiNNakerManchester/Visualiser)
* [Whole Machine Tests](https://github.com/SpiNNakerManchester/sPyNNaker/tree/master/test_whole_board) (overnight only)

# Testing a branch
The SpiNNaker software stack uses branch name matching to coordinate between repositories. To trigger an integration test for a branch, simply create a branch in this repository with same name, and a PR (can be Draft) for that branch. Typically such branches just use a trivial whitespace edit of this file and are tagged `jenkins trigger` so that the team knows not to merge them in this repo. You're _strongly_ encouraged to link to the PRs for the other branches in your branch here (or at least to the lead PR). Once the tests pass, we generally close the PR in this repo and delete the branch without merging it.

This method of working means that this repo seems to have a strange commit pattern.


Pip Freeze
==========
This code was tested with all (SpiNNakerManchester)[https://github.com/SpiNNakerManchester] on tag 7.0.0

Pip Freeze showed the dependencies as:

appdirs==1.4.4

astroid==2.15.6

attrs==23.1.0

certifi==2023.5.7

charset-normalizer==3.2.0

contourpy==1.1.0

coverage==7.2.7

csa==0.1.12

cycler==0.11.0

dill==0.3.6

ebrains-drive==0.5.1

exceptiongroup==1.1.2

execnet==2.0.2

fonttools==4.41.0

graphviz==0.20.1

httpretty==1.1.4

idna==3.4

importlib-resources==6.0.0

iniconfig==2.0.0

isort==5.12.0

jsonschema==4.18.4

jsonschema-specifications==2023.7.1

kiwisolver==1.4.4

lazy-object-proxy==1.9.0

lazyarray==0.5.2

matplotlib==3.7.2

mccabe==0.7.0

mock==5.1.0

multiprocess==0.70.14

neo==0.12.0

numpy==1.24.4

opencv-python==4.8.0.74

packaging==23.1

pathos==0.3.0

Pillow==10.0.0

pkgutil_resolve_name==1.3.10

platformdirs==3.9.1

pluggy==1.2.0

pox==0.3.2

ppft==1.7.6.6

py==1.11.0

pylint==2.17.4

PyNN==0.11.0

pyparsing==2.4.7

pytest==7.4.0

pytest-cov==4.1.0

pytest-forked==1.6.0

pytest-instafail==0.5.0

pytest-progress==1.2.5

pytest-timeout==2.1.0

pytest-xdist==3.3.1

python-coveralls==2.9.3

python-dateutil==2.8.2

PyYAML==6.0.1

quantities==0.14.1

referencing==0.30.0

requests==2.31.0

rpds-py==0.9.2

scipy==1.10.1

six==1.16.0

tomli==2.0.1

tomlkit==0.11.8

typing_extensions==4.7.1

urllib3==2.0.4

websocket-client==1.6.1

wrapt==1.15.0

zipp==3.16.2

