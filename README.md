<div align="center">

# Execute ORT with a Github Action

[![Marketplace](https://img.shields.io/badge/GitHub-Marketplace-green.svg)](https://github.com/marketplace/actions/run-ort) [![Release](https://img.shields.io/github/release/edulix/ort-action.svg)](https://github.com/edulix/ort-action/releases)

This action allows you to run [ORT](https://oss-review-toolkit.org/). The OSS
Review Toolkit (ORT) aims to assist with the tasks that commonly need to be
performed in the context of license compliance checks, especially for (but not
limited to) Free and Open Source Software dependencies.

> THIS IS AN EXPERIMENTAL ACTION

</div>

## Environment
This action requires a java environment. (See example)

<!-- action-docs-description -->
## Description

With this action you can use [ORT](https://oss-review-toolkit.org/) to analyze
the licenses of your dependencies, fail if there's any licensing issues and/or
generate dependencies licensing reports in different formats.

<!-- action-docs-description -->

<!-- action-docs-inputs -->
## Inputs

| parameter | description | required | default |
| - | - | - | - |
| ort-version | edulix/ort docker hub tag to use. | `false` | `latest` |
| analyze | Set to `false` to disable the execution of the ORT `analyze` ORT Action. | `false` | `true` |
| evaluate | Set to `false` to disable the execution of the ORT `evaluate` ORT Action. | `false` | `true` |
| report | Set to `false` to disable the execution of the ORT `report` ORT Action. | `false` | `true` |
| verbosity | Verbosity level in ORT to use. Possible values: [`warn`, `info`, `performance`, `debug`]. | `false` | `warn` |
| package-curations-dir | Specifies path relative to the project directory for the curations directory. Used in `analyze` and `evaluate` actions. It's the `--package-curations-dir` option for ORT. | `false` | - |
| rules-file | Specifies path relative to the project directory for the rules of the `evaluate` action. It's the `--rules-file` option for ORT. | `false` | - |
| license-classifications-file | Specifies path relative to the project directory for the license classifications file of the `evaluate` action. It's the `--license-classifications-file` option for ORT. | `false` | - |
| reporters | List of reporters to run. | `false` | `Excel,StaticHtml,WebApp` |
| ort-extra-args | List of extra arguments for ORT, for all commands, set before the verb (before analyze, evaluate or report). | `false` | `""` |
| analyze-extra-args | List of extra arguments for the `analyze` action. | `false` | - |
| evaluate-extra-args | List of extra arguments for the `evaluate` action. | `false` | - |
| report-extra-args | List of extra arguments for the `report` action. | `false` | - |

<!-- action-docs-inputs -->

<!-- action-docs-outputs -->
## Outputs

| parameter | description |
| - | - |
| results-dir | output directory |
| analyzer-result | output file for the analyze step |
| evaluation-result | output file for the evaluate step |

<!-- action-docs-outputs -->

<!-- action-docs-runs -->
## Runs

This action is an `composite` action.


<!-- action-docs-runs -->

## GitHub workflow

```yml
  - uses: actions/checkout@v2
  - uses: actions/setup-java@v1
    with:
      java-version: '11.0.1'

  - name: Analyze licensing 
    id: ort-action
    uses: edulix/ort-action

  - uses: actions/upload-artifact@v2
    with:
      name: licenses
      path: ${{ steps.ort-action.outputs.results-dir }}
```

And a more complex example:

```yml
  - uses: actions/checkout@v2
  - uses: actions/setup-java@v1
    with:
      java-version: '11.0.1'

  - name: Analyze licensing
    id: ort-action
    uses: edulix/ort-action@develop
    with:
      package-curations-dir: .ort-data/curations-dir/
      rules-file: .ort-data/rules.kts
      license-classifications-file: .ort-data/license-classifications.yml
      reporters: AdocTemplate,PdfTemplate,Excel,StaticHtml,WebApp
      report-extra-args: >
        --report-option
        PdfTemplate=template.path=/project/.ort-data/disclosure_document.ftl
      ort-extra-args: -P ort.severeRuleViolationThreshold=HINT

  - uses: actions/upload-artifact@v2
    with:
      name: licenses
      path: ${{ steps.ort-action.outputs.results-dir }}
```