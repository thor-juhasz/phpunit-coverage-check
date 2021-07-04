
# phpunit-clover-test-coverage-check

![CI workflow](https://github.com/thor-juhasz/phpunit-coverage-check/actions/workflows/ci.yaml/badge.svg)
[![GitHub license](https://img.shields.io/github/license/thor-juhasz/phpunit-coverage-check.svg)](https://github.com/thor-juhasz/phpunit-coverage-check/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/release/thor-juhasz/phpunit-coverage-check.svg)](https://github.com/thor-juhasz/phpunit-coverage-check/releases/)


A PHPUnit test coverage checker.

This command will parse a clover.xml report file (generated by PHPUnit),
to check that the test coverage meets a certain threshold (80% by default).

This library is loosely based on
[johanvanhelden's test coverage check](https://github.com/johanvanhelden/gha-clover-test-coverage-check).

<details>
<summary>Table of contents</summary>
## Table of contents

- [Installation](#installation)
- [Usage](#usage)
    * [Use in GitHub workflow](#use-in-github-workflow)
    * [Run manually](#run-manually)
- [Arguments](#arguments)
  * [filename](#filename-smallrequiredsmall)
- [Options](#options)
    * [threshold](#threshold-smalloptionalsmall)
    * [metric](#metric-smalloptionalsmall)
    * [suppress-errors](#suppress-errors-smalloptionalsmall)
- [Output](#output)
</details>

## Installation

Run this in your projects root directory:
```sh
composer require --dev thor-juhasz/phpunit-coverage-check
```

This will add a new PHP binary to your `vendor/bin/` directory, named `phpunit-coverage-check`.

## Usage

### Use in GitHub workflow

To use this library in a GitHub CI workflow, make sure to generate a `clover.xml` report file when running phpunit:
```yaml
      - name: Run PHPUnit Tests
        run: ./vendor/bin/phpunit --coverage-clover clover.xml
```

Then simply add this after the step than runs phpunit:
```yaml
      - name: Test coverage
        run: ./vendor/bin/phpunit-coverage-check -t 100 clover.xml
```

### Run manually

Generate a `clover.xml` report file with PHPUnit:
```sh
./vendor/bin/phpunit --coverage-clover clover.xml
```

Then you can run the binary provided by this library, passing the name of the clover.xml file as the first argument:
```sh
./vendor/bin/phpunit-coverage-check clover.xml
```

## Arguments

### filename <small>(required)</small>
The filename of the clover coverage XML file.

Example:
```sh
./vendor/bin/phpunit-coverage-check clover.xml
```

## Options

### threshold <small>(optional)</small>
Default value: 80

The threshold determines the lower value of acceptable test coverage. You can pass your desired threshold using
`--threshold N` or `-t N` for short, where `N` is a number ranging from 0 to 100:

Example:

```sh
# Requires 100% coverage
./vendor/bin/phpunit-coverage-check --threshold 100 clover.xml
# Requires 50% coverage
./vendor/bin/phpunit-coverage-check -t 50 clover.xml
# By default when passing no threshold option, it will require 80% coverage
./vendor/bin/phpunit-coverage-check clover.xml
```

### metric <small>(optional)</small>
Default value: elements

You can specify here which metric you want to use to read the code coverage. The supported metrics are:
  - elements
  - statements
  - methods

Example:

```sh
./vendor/bin/phpunit-coverage-check --metric statements clover.xml
./vendor/bin/phpunit-coverage-check -m methods clover.xml
# By default when passing no metric option, it will use elements
./vendor/bin/phpunit-coverage-check clover.xml
```

### suppress-errors <small>(optional)</small>

When the code coverage reported in the clover XML report is under the specified threshold, the command will exit with
code 1. When used in a CI workflow, this results in that job failing.

If you would like to use this tool in a CI workflow, without failing the job, you can pass the `--suppress-errors`
option (or `-s` for short).

Example:

```yaml
      # Even if the coverage is under the threshold, this will not fail the job
      - name: Test coverage
        run: ./vendor/bin/phpunit-coverage-check -t 100 clover.xml
```

## Output

Example outputs:
```sh
# When coverage is 100%, and default threshold used.
$ ./vendor/bin/phpunit-clover-test-coverage-check clover.xml
[OK] Code coverage is 100%, which is acceptable (requires >= 80% coverage)

# When coverage is 100%, and threshold is set to 100%.
$ ./vendor/bin/phpunit-clover-test-coverage-check clover.xml
[OK] Code coverage is 100%, which is acceptable (requires full coverage)

# When coverage is 1%, and default threshold used.
$ ./src/test-coverage-checker.php clover.xml
[ERROR] Code coverage is 1%, which is not acceptable (requires >= 80% coverage)

# When coverage is 1%, and threshold is set to 100%.
$ ./src/test-coverage-checker.php clover.xml
[ERROR] Code coverage is 1%, which is not acceptable (requires full coverage)
```
