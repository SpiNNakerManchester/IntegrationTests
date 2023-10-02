[![Jenkins tests](https://img.shields.io/jenkins/tests?compact_message&jobUrl=http%3A%2F%2Fapollo.cs.man.ac.uk%3A8080%2Fjob%2FIntegration%2520Tests%2Fjob%2Fmain%2F&label=integration%20tests)](http://apollo.cs.man.ac.uk:8080/blue/organizations/jenkins/Integration%20Tests/activity/)

Release Info
============
This release was tested with the following python versions installed.

- appdirs==1.4.4
- astroid==2.15.8
- attrs==23.1.0
- certifi==2023.7.22
- charset-normalizer==3.3.0
- contourpy==1.1.1
- coverage==7.3.1
- csa==0.1.12
- cycler==0.12.0
- dill==0.3.7
- ebrains-drive==0.5.1
- exceptiongroup==1.1.3
- execnet==2.0.2
- fonttools==4.43.0
- graphviz==0.20.1
- httpretty==1.1.4
- idna==3.4
- importlib-resources==6.1.0
- iniconfig==2.0.0
- isort==5.12.0
- jsonschema==4.19.1
- jsonschema-specifications==2023.7.1
- kiwisolver==1.4.5
- lazy-object-proxy==1.9.0
- lazyarray==0.5.2
- matplotlib==3.7.3
- mccabe==0.7.0
- mock==5.1.0
- MorphIO==6.6.8
- multiprocess==0.70.15
- neo==0.12.0
- numpy==1.24.4
- opencv-python==4.8.1.78
- packaging==23.2
- pathos==0.3.1
- Pillow==10.0.1
- pkgutil_resolve_name==1.3.10
- platformdirs==3.10.0
- pluggy==1.3.0
- pox==0.3.3
- ppft==1.7.6.7
- py==1.11.0
- pylint==2.17.7
- PyNN==0.12.1
- pyparsing==2.4.7
- pytest==7.4.2
- pytest-cov==4.1.0
- pytest-forked==1.6.0
- pytest-instafail==0.5.0
- pytest-progress==1.2.5
- pytest-timeout==2.1.0
- pytest-xdist==3.3.1
- python-coveralls==2.9.3
- python-dateutil==2.8.2
- PyYAML==6.0.1
- quantities==0.14.1
- referencing==0.30.2
- requests==2.31.0
- rpds-py==0.10.3
- scipy==1.10.1
- six==1.16.0
- spalloc==1!7.1.0
- SpiNNaker-PACMAN==1!7.1.0
- SpiNNakerGraphFrontEnd==1!7.1.0
- SpiNNakerTestBase==1!7.1.0
- SpiNNFrontEndCommon==1!7.1.0
- SpiNNMachine==1!7.1.0
- SpiNNMan==1!7.1.0
- SpiNNUtilities==1!7.1.0
- sPyNNaker==1!7.1.0
- testfixtures==7.2.0
- tomli==2.0.1
- tomlkit==0.12.1
- typing_extensions==4.8.0
- urllib3==2.0.5
- websocket-client==1.6.3
- wrapt==1.15.0
- zipp==3.17.0

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
