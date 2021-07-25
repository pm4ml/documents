# How to install and use Antora site generator with PDF Exporter

This repository holds documentation resources relating to Payment Manager OSS.

If you wish to generate a doc site and PDFs out of these documents locally, on your computer:

1. Clone the repository.
2. [Install the Antora site generator](#install-antora) following the instructions below.
3. Use the Antora site generator following the instructions [here](#generate-a-doc-site-with-PDFs) or [here](#generate-a-doc-site-without-PDFs).

## Install Antora

### Directory

We highly recommend that you install this software and its prerequisites directly in the directory where your site’s Antora playbook file (e.g., playbook.yml) is located.
If you install them globally on your system, conflicts between other instances of the dependencies, Asciidoctor PDF, and core Antora components will likely occur.
The documentation for this site generator assumes that you are executing the commands from your playbook directory; in other words, from the directory where your Antora playbook is located.

### Prerequisites

#### Node.js

This custom Antora site generator (=the one that includes the Antora PDF Exporter) requires Node.js (commands: `node` and `npm`).
First, install [Node.js](https://nodejs.org/en/) 12, preferably using [nvm](https://github.com/nvm-sh/nvm#installing-and-updating).

```
 $ nvm install 12
```

**NOTE:** When using nvm for Windows, you have to specify the full Node.js version, such as 12.19.0.

Run the `node` command to verify Node.js is properly installed and active:

```
 $ node -v
``` 

You should also be able to run the `npm` command:

```
 $ npm -v
```

#### Ruby

Antora PDF Exporter relies heavily on Asciidoctor PDF, and Asciidoctor PDF requires Ruby (commands: `ruby` and `bundle`).
Next, install [Ruby](https://ruby-lang.org) 2.7, preferably using [RVM](https://rvm.io/).

```
 $ rvm install 2.7
```

Run the `ruby` command to verify Ruby is properly installed and active:

```
 $ ruby -v
```

You should also be able to run the `bundle` command:

```
 $ bundle -v
```

#### hexapdf

To optimize PDFs, [hexapdf](https://hexapdf.gettalong.org/) is used.
It is a pure Ruby application, so you don't need to worry about installing an extra system command.
However, before introducing it, it's important to point out that its license is AGPL.
If that's okay with you, read on to learn how to use it.

First, add the *hexapdf* gem to your _Gemfile_:

```
gem 'hexapdf'
```

Next, run the `bundle` command again to install it into your project:

```
 $ bundle
```

Now you'll need to configure the optimizer to prefer hexapdf by providing your own implementation of the `Optimizer` class.
Start by creating a Ruby file named _optimizer-hexapdf.rb_ and populate it with the following code:

**NOTE**: In this repo, this step has already been done for you, the _optimizer-hexapdf.rb_ file has already been created.

```
require 'hexapdf/cli'

class Asciidoctor::PDF::Optimizer
  def initialize(*)
    app = HexaPDF::CLI::Application.new
    app.instance_variable_set :@force, true
    @optimize = app.main_command.commands['optimize']
  end

  def optimize_file path
    options = @optimize.instance_variable_get :@out_options
    options.compress_pages = true
    @optimize.execute path, path
    nil
  rescue
    # retry without page compression, which can sometimes fail
    options.compress_pages = false
    @optimize.execute path, path
    nil
  end
end
```

**NOTE:** In this repo, the next 2 steps (modifying `command` and setting `optimize`) have already been done for you, you can skip these steps.

To activate your custom optimizer, you must pass this script to Asciidoctor PDF by adding the `-r` flag to the value of the `command` key in _pdf-config.yml_:

```
command: bundle exec asciidoctor-pdf -r ./optimizer-hexapdf.rb
```

You'll also need to set the `optimize` attribute under the `attributes` key.
The value of this attribute can be blank since `hexapdf` doesn't offer different optimization modes:

```
asciidoc:
  attributes:
    optimize: ''
```

Now Asciidoctor PDF will automatically optimize the PDFs it generates by running hexapdf on them.

### Install the Antora PDF Exporter

To install the custom site generator that includes the PDF Exporter, you must create a _package.json_ file and define the required Antora components. 

**NOTE**: In this repo, this step has already been done for you, the _package.json_ file has already been created.

In your playbook directory, create the file _package.json_ in your playbook directory and populate it with the following contents:

```
{
  "private": true,
  "dependencies": {
    "@antora/cli": "~2.3",
    "@antora/site-generator-default": "~2.3",
    "@antora/site-generator-with-pdf-exporter": "git+https://gitlab.com/opendevise/oss/antora-site-generator-with-pdf-exporter#v2.3.0-alpha.2"
  }
}
```

Now, install the Antora CLI (command: `antora`) and generators into your project using the `npm` command from Node.js.
In your terminal, enter the following command.

```
 $ npm i --no-optional
```

**IMPORTANT:** The `--no-optional` flag is important as it prevents conflicting versions of the Antora packages from being installed.

To verify you've installed the site generator correctly, run the `antora` command with the version flag (`-v`):

Linux or macOS

```
 $ $(npm bin)/antora -v
```

Windows CMD

```
 $ for /f usebackq %bin in (`npm bin`) do %bin\antora -v
``` 

Windows PowerShell
```
 $ cmd /c "$(npm bin)/antora -v"
``` 

These commands essentially amount to executing `node_modules/.bin/antora -v`, which you can also use.

### Install Asciidoctor PDF

The site generator with the PDF Exporter uses Asciidoctor PDF to generate PDF files.
You can install Asciidoctor PDF however you like, but we recommend using the `bundle` command to install and manage it.

In your playbook directory, create the file _Gemfile_ and populate it with the following contents:

**NOTE:** In this repo, this step has already been done for you, the _Gemfile_ has already been created.

```
source 'https://rubygems.org'

gem 'asciidoctor-pdf', github: 'asciidoctor/asciidoctor-pdf' <1>
gem 'prawn-table', github: 'prawnpdf/prawn-table' <2>
gem 'rouge' <3>
```

<1> This entry requests the unreleased version of *asciidoctor-pdf* hosted on GitHub.

<2> Although *prawn-table* is an automatic dependency of Asciidoctor PDF, it must be listed explicitly since several bug fixes are only available in the unreleased version hosted on GitHub.

<3> *rouge* provides syntax highlighting for source blocks and is the recommended option when using Asciidoctor PDF.

Now, install the *asciidoctor-pdf* gem and its dependencies into your project using the `bundle` command:

```
 $ bundle --path=.bundle/gems
```

**IMPORTANT:** The `--path` flag is important as it scopes the dependencies to your playbook directory (rather than installing them globally).

**NOTE:** On Windows, you may need to replace the forward slash in the `--path` value with a backslash (i.e., `--path=.bundle\gems`).

To verify you've installed Asciidoctor PDF correctly, run the `asciidoctor-pdf` command as follows:

```
 $ bundle exec asciidoctor-pdf -v
```

All you have left to do is to create a PDF configuration file and you'll be ready to start generating PDFs by running the custom Antora site generator with the PDF Exporter enabled.

## Configure Antora PDF Exporter

**NOTE:** For this particular repo, just use the `pdf-config.yml` and `pdf-theme.yml` files provided. There is no need to change anything in them, they are ready to be used as-is. The below information is provided for the sake of completeness only.

**IMPORTANT:** The Antora PDF exporter depends on your site's [Antora playbook file](https://docs.antora.org/antora/latest/playbook/).
Your playbook directory must contain a playbook file (e.g., _antora-playbook.yml_) and a PDF configuration file (_pdf-config.yml_).

In addition to an Antora playbook file, the Antora PDF exporter is configured using a file named _pdf-config.yml_.
This YAML file defines the PDF configuration keys and AsciiDoc attributes that are only applied to the PDFs.
The AsciiDoc attributes defined in _pdf-config.yml_ override any matching attributes in the playbook file, component descriptor files, or pages according to Antora's [attribute precedence rules](https://docs.antora.org/antora/latest/playbook/asciidoc-attributes/#precedence-rules).

The PDF theme, if used, is specified by the `pdf-theme` attribute defined in _pdf-config.yml_.
It's set and assigned under the `attributes` key.

```
asciidoc:
  attributes:
    pdf-theme: ./pdf-theme.yml
```

## Generate a doc site with PDFs

To run the Antora site generator with the PDF exporter enabled, execute the following command in your terminal from your playbook directory.

Linux or macOS

```
 $ $(npm bin)/antora --clean --generator=@antora/site-generator-with-pdf-exporter antora-playbook.yml
```

Windows CMD
```
 $ for /f usebackq %bin in (`npm bin`) do %bin\antora --clean --generator=@antora/site-generator-with-pdf-exporter antora-playbook.yml
```

Windows Powershell

```
 $ cmd /c "(npm bin)/antora --clean --generator=@antora/site-generator-with-pdf-exporter antora-playbook.yml"
```

Let's break down this command.

* `$(npm bin)/antora` (and the like) is the base call to Antora.
The prefix `$(npm bin)/` tells the shell to use the `antora` command that is installed inside the playbook project.
* The `--clean` option flag removes all of the files and folders in the build directory prior to generating your site and PDFs.
* The `--generator` flag specifies the name of the custom site generator, `@antora/site-generator-with-pdf-exporter`.
This generator will be used instead of the default Antora generator.
* `antora-playbook.yml` is the required playbook argument.
The site generator uses the playbook file to generate your site and your PDFs.
Notice that you don't specify the name of the PDF configuration file; Antora will locate that file automatically.

Once this command is executed, each PDF will be written to the build directory.
The build directory is where the artifacts for the PDF files are assembled and the PDF files are generated.
By default, the directory location is _./build/pdf_.
This location can be customized by assigning a new value to the `build_dir` key in the _pdf-config.yml_ file.

The PDFs are also published to your site by default unless you deactivate the `publish` key in your _pdf-config.yml_ file.
The PDFs are always available for your review in the build directory, even when `publish` is assigned the value `false`.

## Generate a doc site without PDFs

In some cases, you may not need any PDFs and you just wish to view a document or the whole doc site locally. In that case, run the Antora site generator without the PDF exporter enabled, and execute the following command in your terminal from your playbook directory.

Linux or macOS

```
 $ antora playbook.yml
```

Windows CMD
```
 $ antora playbook.yml
```

Windows Powershell

```
 $ antora playbook.yml
```

Once this command is executed, the doc site will be written to the build directory.
The build directory is where the artifacts for the doc site are assembled and the HTML files are generated.
In this doc set, the directory location is _./build_. To open your doc site in a browser, click the `index.html` file in _./build/site_.
