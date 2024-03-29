# lavocado - libvirt test framework based on Avocado

The lavocado aims to be an alternative test framework for the libvirt project
using as muscles only Avocado and python-libvirt. This can be used to write
unit, functional and integration tests and it is inspired on the libvirt-tck
framework, but instead of Perl it written in Python.

The idea is to provide to the test writers helper classes to avoid boilerplate
code, improve code readability and maintenance.

## Disclaimer

**For now, this framework is assuming that you are running the tests in a clean
and fresh environment. Although we take care when creating and removing domains
and networks, be aware that these tests may affect your system.** 

For future versions we have plans to integrate this with `lcitool` in order to
prepare the environment before executing the tests.

## Requirements

The libvirt interactions will all be done via the libvirt Python bindings
(libvirt-python), we also rely on Avocado, not only to execute the tests but
also to generate multiple output format artifacts (that can be uploaded to CI
environments) and also the image fetch, so we don't need to handle with local
cache, checksum and any other test's requirement task.

In order to install the requirements, please execute the following command:

```bash
  $ pip3 install -r requirements.txt
```

If you wish you can run this command under a virtual environment.

## Features

 * Parallel execution: If your tests are independent of each other, you can run
   those in parallel, when getting images, Avocado will create a snapshot to
   avoid conflicts. Also domains created by tests have unique names to avoid
   collisions.

 * Creating domains easily: You can use the `.create_domain()` method to create
   a generic domain which is based on a Jinja2 template. If you want to use an
   already existing XML domain description, no problem, use
   `Domain.from_xml_path()` instead.

 * Multiple output formats: TAP, HTML, JSON, xUnit (Gitlab CI ready).

 * Test tags: Ability to mark tests with tags, for filtering during execution
   and/or results.

 * Different isolation models: By default the avocado runner will spawn each
   test on local processes (subprocess), but we can configure for specific use
   cases, running the tests on different isolation models (containers for
   instance).

 * Requirements resolver: Avocado has implemented the 'requirements resolver'
   feature that makes it easy to define test requirements. Currently, The
   RequirementsResolver can handle `assets` (any local or remote files) and
   `packages` but new requirements are on the roadmap, including `ansible
   playbooks`. For now, it is still only an experimental feature though.

 * Test result artifacts for future analysis: you can take a look at `avocado
   jobs` command or inspect your `~/avocado/test-results/` folder for more
   detailed results.

 * Multiple test execution with variants: the variants subsystem is what allows
   the creation of multiple variations of parameters, and the execution of
   tests with those parameter variations.

 * Clear separation between tests and bootstrap stages: If something fails
   during the setUp() metod execution, your test will be not marked as FAIL,
   instead it will be flagged as ERROR. You can also use some decorators
   (@cancel_on, @skipUnless, ...) around your test to avoid false positves.

## Running

After installing the requirements, you can run the tests with the following
commands:

```bash
  $ export PYTHONPATH=.:$PYTHONPATH
  $ avocado run --test-runner='nrunner' ./tests/domain/*.py
```

Please note that the Next Runner (nrunner) will be the default runner soon in
Avocado. So `--test-runner='nrunner'` option will no longer be needed.

Or, if you prefer, you can call it with `make`:

```bash
  $ make libvirt-tests
```

## Writing Tests

You can write your tests here the same way you write for the [Avocado
Framework](https://avocado-framework.readthedocs.io/en/latest/).
Avocado supports "simple tests" (just executables) and "instrumented tests"
(Python tests).

See the `tests/` folder for some references and ideas. In addition, feel free
to read the [Avocado Test Writer’s
Guide](https://avocado-framework.readthedocs.io/en/latest/guides/writer/) to
play with some advanced features of the framework.
