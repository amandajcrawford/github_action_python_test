This is an example GitHub Action workflow that sends python test coverage data to coveralls.io. This workflow uses the [Coveralls](https://github.com/marketplace/actions/coveralls-github-action) and [ReportGenerator](https://github.com/marketplace/actions/reportgenerator) GitHub Actions.

```
name: Python package

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:

    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.7, 3.8]

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pipenv
        pipenv --python ${{ matrix.python-version }}
        pipenv install --dev 
    - name: Run tests
      run: |
        pipenv run coverage run --source my_python_package  setup.py test
        pipenv run coverage xml -o coverage.xml
    - name: Generate lcov report 
      uses: danielpalme/ReportGenerator-GitHub-Action@4.6.4
      with:
        reports: coverage.xml
        targetdir: lcov/
        reporttypes: lcov
    - name: Coveralls
      uses: coverallsapp/github-action@master
      with:
        github-token: ${{ secrets.GITHUB_TOKEN }}
        path-to-lcov: lcov/lcov.info

```
