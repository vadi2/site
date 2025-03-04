---
title: "Project Structure"
weight: 10
description: >
  Description of the folder structure expected by SUSHI for a FSH project
---

## Minimal Project

The simplest FSH project contains only a configuration file and an **input/fsh** subdirectory of FSH files containing FHIR Shorthand definitions. It might look like this:

```text
my-project
├── input
|    └── fsh
|        ├── file1.fsh
|        ├── file2.fsh
|        └── file3.fsh
└── sushi-config.yaml
```

Each FSH file can contain multiple FSH definitions of varying types. FSH file names are not significant, but must end with the **.fsh** extension. In addition, FSH files can be organized into subdirectories. This provides authors the flexibility to organize their FSH definitions in whatever way makes sense to then.

The **sushi-config.yaml** file provides project configuration data to SUSHI. It is described further in the [Configuration](/docs/sushi/configuration/) documentation.

## Initializing a SUSHI Project

Setting up a fully-featured FSH project can be complex, so SUSHI provides an `--init` argument to do it automatically. When `sushi --init` is run, SUSHI will request project information from the user:

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>For SUSHI 3.0.0 and later, use `sushi init`.
{{% /small-pageinfo %}}

```text
Name (Default: ExampleIG): my-project
Id (Default: fhir.example): my.id
Canonical (Default: http://example.org): http://myid.org
Status (Default: draft): active
Version (Default: 0.1.0): 2.0.0
Publisher Name (Default: Example Publisher): MyPublisher
Publisher Url (Default: http://example.org/example-publisher): http://my-publisher.org
Initialize SUSHI project in C:\Users\shorty\dev\my-project? [y/n]: y
```

These values are then used to generate a project structure compatible with the FHIR IG Publisher:

```text
my-project
├── .gitignore
├── _genonce.bat
├── _genonce.sh
├── _updatePublisher.bat
├── _updatePublisher.sh
├── ig.ini
├── input
|   ├── ignoreWarnings.txt
|   ├── fsh
|   |   └── patient.fsh
|   └── pagecontent
|       └── index.md
└── sushi-config.yaml
```

In addition to the **input/fsh** folder, `init` creates an **input/pagecontent** folder containing a dummy home page for your IG. The files **ig.ini** and **ignoreWarnings.txt** are required by the [template-based IG Publisher](https://build.fhir.org/ig/FHIR/ig-guidance/using-templates.html). The `.bat` and `.sh` scripts which allow you to [run the IG Publisher](/docs/sushi/running/#downloading-the-ig-publisher) from your command line. Finally, a default `.gitignore` file for integration with GitHub is provided. From this point on, you can modify the configuration and definitions as necessary.

## Using the HL7 IG Publisher and Auto-Builder

This project structure integrates with the HL7 IG Publisher [Auto-Builder](https://github.com/FHIR/auto-ig-builder/blob/master/README.md). When the IG Publisher detects an **input/fsh** subdirectory, it will automatically run SUSHI on the project directory and output the SUSHI results to a **fsh-generated** directory (e.g., **my-project/fsh-generated** in the example above). It will then continue with the normal IG Publisher process.



This approach allows a GitHub repository to be configured such that whenever changes to FSH files are pushed to GitHub, the [Auto-Builder](https://github.com/FHIR/auto-ig-builder/blob/master/README.md) will pick them up, run the SUSHI/IG Publisher process, and publish the resulting IG to [http://build.fhir.org](http://build.fhir.org).

SUSHI provides support for several of the files and directories required by the [template-based IG Publisher](https://build.fhir.org/ig/FHIR/ig-guidance/) for building Implementation Guides. Some IG customizations can be configured using additional properties in the **sushi-config.yaml** file. A FSH project integrated into the template-based IG Publisher may look like this:

```text
my-project
├── ig.ini
├── input
|   ├── fsh
|   |   ├── file1.fsh
|   |   ├── file2.fsh
|   |   └── file3.fsh
│   ├── ignoreWarnings.txt
│   ├── images
│   │   ├── myDocument.pdf
│   │   ├── myGraphic.png
│   │   └── mySpreadsheet.xlsx
│   ├── includes
│   │   └── menu.xml
│   └── pagecontent
│       ├── 1_mySecondPage.md
│       ├── 2_myThirdPage.md
│       ├── 3_myFourthPage.md
│       └── index.md
├── package-list.json
├── sushi-ignoreWarnings.txt
└── sushi-config.yaml
```

  {{% alert title="Tip" color="success" %}}
  Examples of **package.json**, **ig.ini**, **package-list.json**, **ignoreWarnings.txt** and **menu.xml** files can be found in the [sample IG project](https://github.com/FHIR/sample-ig) provided for this purpose. In addition, more general guidance can be found in [Guidance for HL7 IG Creation](https://build.fhir.org/ig/FHIR/ig-guidance/).
  {{% /alert %}}

You can populate your project as follows:

* **sushi-config.yaml**: This file provides configuration data to SUSHI. It is described further in the [Configuration](/docs/sushi/configuration/) documentation.
* **input/fsh/\*.fsh**: FSH files contain the FHIR Shorthand definitions for all the resources and examples in your IG.
* **ig.ini**: Configuration file required for the FHIR IG Publication process. NOTE: As of the SUSHI 1.0 release, this file MUST use a template based on `fhir.base.template#current`. Specific template versions (i.e., other than `#current`) are expected to work in the future.  For now, any of the following should work:
  * `template = fhir.base.template#current`
  * `template = hl7.base.template#current`
  * `template = hl7.fhir.template#current`
  * `template = hl7.davinci.template#current`
  * `template = hl7.cda.template#current`
* **input/ignoreWarnings.txt**: This file is used to suppress specific QA warnings and information messages produced by the FHIR IG Publisher (as opposed to SUSHI).
* **input/images/\***: Put anything that is not a page in the IG, such as images, spreadsheets or zip files, in the **input/images** subdirectory. These files can be referenced by user-provided pages or menus.
* **input/includes/menu.xml**: If present, this file will be used for the IG's main menu layout. Note that the presence of this file will block usage of the `menu` property in **sushi-config.yaml**.
* **input/pagecontent/\***: Put either markup (.xml) or markdown (.md) files with the narrative content of your IG in the **input/pagecontent/** subdirectory. These files are the sources for the html pages that accompany the automatically-generated pages of your IG. The header and footer of these pages are automatically generated, so your content should not include these elements. Any number of pages can be added. In addition to stand-alone pages, you can provide additional text for generated artifact pages. The naming of these files is significant:
  * **index.xml\|md**: This file provides the content for the IG's main page.
  * **N\_pagename.xml\|md**: If present, these files will be generated as individual pages in the IG. The leading integer (N) determines the order of the pages in the table of contents. Adding a leading integer is optional, and in the absence of a leading integer, SUSHI will sort the pages alphabetically. The order of the pages can also be explitly specified with the `pages` property in **sushi-config.yaml**.
  * **{artifact-file-name}-intro.xml\|md**: If present, the contents of the file will be placed on the relevant page _before_ the artifact's definition.
  * **{artifact-file-name}-notes.xml\|md**: If present, the contents of the file will be placed on the relevant page _after_ the artifact's definition.
* **input/{supported-resource-input-directory}/\*** (not shown above): JSON or XML files in [supported resource directories](https://build.fhir.org/ig/FHIR/ig-guidance/using-templates.html#root.input) (e.g., **profiles**, **extensions**, **examples**, etc.) can be referenced by FHIR artifacts defined in FSH, and will be added to the generated **ImplementationGuide.json** file. If there are additional subfolders (e.g., input/resources/nested), use the `path-resource` parameter in **sushi-config.yaml** to tell the IG Publisher which additional input paths to process (see [Specifying Additional Resource Paths](/docs/sushi/tips/#specifying-additional-resource-paths) for details).
* **package-list.json**: This optional file, described [here](https://confluence.hl7.org/display/FHIR/FHIR+IG+PackageList+doco), should contain the version history of your IG.
* **sushi-ignoreWarnings.txt**: This optional file can be used to suppress warnings logged by SUSHI. Errors and informational logs from SUSHI cannot be ignored. This file should be placed either at the root of the project (e.g., **my-project/sushi-ignoreWarnings.txt** in the example above), or within the **input** directory (e.g., **my-project/input/sushi-ignoreWarnings.txt**). Warnings will be ignored if they completely match the contents of any line of the file (one line per warning, case-sensitive). Regular expressions can also be specified, one per line, indicated by starting and ending the line with `/`. For example:

  ```
  Instance PatientExample1 is not an instance of a resource, so it should only be used inline on other instances, and it will not be exported to a standalone file. Specify "Usage: #inline" to remove this warning.
  /Detected the following non-conformant Resource definitions.*/
  ```
  Any warning which exactly matches the contents of the first line will be ignored. The second line specifies that any warning beginning with `Detected the following non-conformant Resource definitions` will be ignored.

  {{% alert title="Tip" color="success" %}}
  SUSHI does log several multi-line warnings, but these warnings cannot be specified directly in the **sushi-ignoreWarnings.txt** file, since the warnings to ignore must be specified line by line. To ignore these warnings, a regular expression should be used.
  {{% /alert %}}
