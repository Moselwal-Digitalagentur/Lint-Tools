# Moselwal Digitalagentur Lint Tools

<details>
<summary>Table of Content</summary>

[TOC]

</details>

## Overview
Welcome to the Moselwal Digitalagentur Lint Tools repository. This project provides a set of GitLab CI/CD components designed to streamline and automate the linting process for various projects. The lint tools are designed to be flexible, with configurable inputs to tailor the CI/CD pipeline to your specific needs.

## License
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.

## Components

### Component: lint:commit-msg
This job is responsible for linting commit messages to ensure they meet the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start linting"`              | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End linting"`                | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_BRANCH != $CI_DEFAULT_BRANCH || $CI_PIPELINE_SOURCE == 'merge_request_event'
```

##### Default `needs-config`
```yaml
- job: build:extension:backend
  optional: true
- job: build:site:test:backend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:composer:normalize
This job is responsible for normalizing the `composer.json` file to ensure it adheres to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start linting"`              | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End linting"`                | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - composer.json
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - composer.json
```

##### Default `needs-config`
```yaml
- job: build:extension:backend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]


### Component: lint:composer:psr-verify
This job is responsible for verifying that the `composer.json` file adheres to PSR standards by running `composer dumpautoload` with the `--strict-psr` flag.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start PSR verification"`     | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End PSR verification"`       | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - composer.json
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - composer.json
```

##### Default `needs-config`
```yaml
- job: build:extension:backend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]


### Component: lint:composer:validate
This job is responsible for validating the `composer.json` file to ensure it is correct and adheres to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start validation"`           | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End validation"`             | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - composer.json
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - composer.json
```

##### Default `needs-config`
```yaml
- job: build:extension:backend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:eslint
This job is responsible for running ESLint to ensure that the JavaScript code adheres to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                           | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|---------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                                | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules           | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs           | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/node:14-alpine` | `$CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX/$NODE_VERSION-alpine`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                      | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start ESLint"`                 | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End ESLint"`                   | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - ./**/package.json
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - ./**/package.json
```

##### Default `needs-config`
```yaml
- job: build:extension:frontend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:json
This job is responsible for linting JSON files to ensure they adhere to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start JSON linting"`         | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End JSON linting"`           | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - ./**/*.json
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - ./**/*.json
```

##### Default `needs-config`
```yaml
- job: build:extension:backend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:phplint
This job is responsible for linting PHP files to ensure they adhere to the project's coding standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start PHP linting"`          | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End PHP linting"`            | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_BRANCH != $CI_DEFAULT_BRANCH || $CI_PIPELINE_SOURCE == 'merge_request_event'
  exists:
    - ./**/*.php
```


##### Default `needs-config`
```yaml
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:stylelint
This job is responsible for running Stylelint to ensure that the CSS/SCSS code adheres to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                           | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|---------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                                | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules           | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs           | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/node:14-alpine` | `$CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX/$NODE_VERSION-alpine`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                      | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start Stylelint"`              | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End Stylelint"`                | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - ./**/package.json
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - ./**/package.json
```

##### Default `needs-config`
```yaml
- job: build:extension:frontend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

#### Artifacts Settings

```yaml
artifacts:
expire_in: 60 minutes
reports:
junit: ${CI_PROJECT_DIR}/${NPM_BUILD_DIR}/reports/stylelintreport.junit.xml
```

### Component: lint:typoscript
This job is responsible for linting TypoScript and TSconfig files to ensure they adhere to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start TypoScript linting"`   | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End TypoScript linting"`     | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - ./**/*.typoscript
    - ./**/*.tsconfig
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - ./**/*.typoscript
    - ./**/*.tsconfig
```

##### Default `needs-config`
```yaml
- job: build:extension:frontend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:xlf
This job is responsible for linting XLF (XML Localization Interchange File Format) files to ensure they adhere to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start XLF linting"`          | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End XLF linting"`            | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - ./**/*.xlf
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - ./**/*.xlf
```

##### Default `needs-config`
```yaml
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: lint:yaml
This job is responsible for linting YAML files to ensure they adhere to the project's standards.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `test`                                                                 |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `needs-config`              | (Optional) Configure the jobs that need to run before this job.                                    | See below for default needs         | See below for default needs                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `test`                    | `docker`                                                               |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start YAML linting"`         | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End YAML linting"`           | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
  exists:
    - ./**/*.yaml
    - ./**/*.yml
- if: $CI_COMMIT_REF_NAME != 'release'
  exists:
    - ./**/*.yaml
    - ./**/*.yml
```

##### Default `needs-config`
```yaml
- job: build:extension:backend
  optional: true
- job: build:prepare
  optional: true
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Needs**: $[[ inputs.needs-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

## Contributing
Please read about CI/CD components and best practices at: https://docs.gitlab.com/ee/ci/components    
We welcome contributions to improve the Moselwal Digitalagentur Release Tools. Please read our [Contributions Documentation](CONTRIBUTING.md)

## Support
For support, please contact [Moselwal Digitalagentur GmbH](mailto:support@moselwal.de).