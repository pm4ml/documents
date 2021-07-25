# Contributor's Guide

The documentation of the Payment Manager OSS product is open source and you can contribute in one of the following ways:

* submit an issue to request the fixing of a documentation bug (for example, a typo)
* fix a bug yourself and submit a pull request (PR)
* write content that describes a new component or functionality and submit a PR

## Prerequisites

### Authoring tools

Payment Manager OSS documents are written in AsciiDoc and the documentation site itself is built using a static site generator called Antora. Basic familiarity with these tools is a prerequisite to contributing. If you have worked with Markdown and a static site generator before, it should be fairly easy to acquire the required skills.

AsciiDoc and Antora self-study resources are available here:

* [AsciiDoc Writer's Guide](https://asciidoctor.org/docs/asciidoc-writers-guide/)
* [AsciiDoc Syntax Quick Reference](https://docs.asciidoctor.org/asciidoc/latest/syntax-quick-reference/)
* [Antora documentation](https://docs.antora.org/)

### Git and GitHub

Given that the documents are stored in GitHub, some familiarity with Git versioning and GitHub is also recommended.

Git and GitHub self-study resources are available here:

* [Comprehensive Git guide](https://git-scm.com/book/en/v2)
* [Collection of learning resources recommended by GitHub](https://docs.github.com/en/get-started/quickstart/git-and-github-learning-resources)

### Software checklist

Before you start, ensure you have the following in place:

* You have a GitHub account.
* You have installed Git.
* You have installed Antora. For detailed instructions, see [How to install and use Antora site generator with PDF Exporter](/antora_instructions_for_doc_contributors.md). Installing Antora is required so you can build the document locally to check if you are happy with your changes before submitting a PR.
* You have installed an AsciiDoc editor. For suggestions, see: https://docs.asciidoctor.org/asciidoctor/latest/tooling/

## Submit an issue in GitHub

In the case of small editorial fixes (for example, to correct a typo), you have the option to report the documentation bug by raising an issue directly in the [pm4ml/documents](https://github.com/pm4ml/documents/issues) GitHub repo. 

For detailed instructions on how to submit an issue against a repository, see the following GitHub documentation: https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue#creating-an-issue-from-a-repository

On submitting the issue, the documentation team is notified. As soon as we fix and close the issue, you will get an email notification from GitHub to the email address you provided when creating your GitHub account.

## Fix a bug / write content and submit a pull request

If you choose to create/update content yourself, then the process at a high level is as follows:

1. Clone the [pm4ml/documents](https://github.com/pm4ml/documents/issues) GitHub repo.
1. Create your branch. The default branch (`master`) is protected so we are asking that you create your own branch.
1. Create/update content.
1. Build doc site locally to check if all is OK.
1. Submit your changes to your branch via a pull request. Request a review from vandaillyes-modusbox.
1. Wait until your PR is reviewed.
1. Once your PR has been approved, merge your content to the `master` branch.