[![Jenkins tests](https://img.shields.io/jenkins/tests?compact_message&jobUrl=http%3A%2F%2Fapollo.cs.man.ac.uk%3A8080%2Fjob%2FIntegration%2520Tests%2Fjob%2Fmain%2F&label=integration%20tests)](http://apollo.cs.man.ac.uk:8080/blue/organizations/jenkins/Integration%20Tests/activity/)


# IntegrationTests
Integration Tests unified over all repositories

In particular, tests:
* [sPyNNaker](https://github.com/SpiNNakerManchester/sPyNNaker)
* [SpiNNaker Graph Front End](https://github.com/SpiNNakerManchester/SpiNNakerGraphFrontEnd)
* [sPyNNaker New Model Template](https://github.com/SpiNNakerManchester/sPyNNaker8NewModelTemplate)
* [PyNN8 Examples](https://github.com/SpiNNakerManchester/PyNN8Examples)
* [Introductory Lab Scripts](https://github.com/SpiNNakerManchester/IntroLab)
* [Cortical microcircuit](https://github.com/SpiNNakerManchester/microcircuit_model) (overnight only)

# Testing a branch
The SpiNNaker software stack uses branch name matching to coordinate between repositories. To trigger an integration test for a branch, simply create a branch in this repository with same name, and a PR (can be Draft) for that branch. Typically such branches just use a trivial whitespace edit of this file and are tagged `jenkins trigger` so that the team knows not to merge them in this repo. You're _strongly_ encouraged to link to the PRs for the other branches in your branch here (or at least to the lead PR). Once the tests pass, we generally close the PR in this repo and delete the branch without merging it.

This method of working means that this repo seems to have a strange commit pattern.
