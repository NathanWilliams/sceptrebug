# Intro
This is a minimal case to demonstrate a bug in Sceptre where values are incorrectly shared between StackGroups.

# Issue

Sceptre caches template values between StackGroups, resulting in incorrect rendering.

In the `sceptre` directory there are two StackGroups for different environments.

In the `dev` environment in `dev/config.yaml` a variable `env` is set.
In the `ci` environment this has been omitted.

## Expected behaviour

Trying to generate / launch the `ci/simple` stack should result in an error.

## Actual behaviour

The stack only fails some of the time.
Some times it generates with the `env` value from the `dev` stack.

# Executing
 *NOTE* Sceptre collects it's file list in a `set`, which are non-deterministic in Python.
 As such, the following may need to be run a number of times to see the bug.

 ```
 cd sceptre
 sceptre generate ci/simple
 ```

# Example output

Correct behaviour:
```
$ sceptre sceptre generate ci/simple
"'env' is undefined"
```

Incorrect behaviour:
```
$ sceptre generate ci/simple
---
AWSTemplateFormatVersion: '2010-09-09'
Description: Simple template for testing Sceptre


Resources:
  Type: AWS::SSM::Parameter
  Properties:
    Name: !Sub '/testing/dev'
    Type: String
    Value: "Sceptre env: dev"
```
