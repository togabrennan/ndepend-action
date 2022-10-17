# NDepend GitHub Action

This GitHub Action lets run an NDepend analysis after each rebuild and explore results in a shared interactive HTML+js report (see some sample reports [here](
https://www.ndepend.com/sample-reports/). Reports generated by the GitHub action contain links to code source hosted by GitHub. 

This Action can also fail the build upon some Quality Gate(s) failure.

With a Developer license, the standalone app VisualNDepend.exe or the NDepend Visual Studio extension can download the NDepend Github Action analysis results locally for further exploration.

**The NDepend GitHub Action runs only on the worker machine and zero data (source code, account, analysis result, or anything) is shared remotely with ndepend.com or any other third-party. Indeed, the NDepend analysis result is stored as an artifact of your build.**

## Activate the trial license for your repository

Get the license trial data from [this page on ndepend.com](https://www.ndepend.com/activation_githubaction)

Got to the settings of your repository
![alt text](https://docs.github.com/assets/cb-27528/images/help/repository/repo-actions-settings.png)

From the left side bar goto Secrets->Actions, create a new secret named NDependLicense and paste the license data retrieved from the ndepend website.

## Usage

The ndepend action must be added after your build and unit testing actions, note that only the license parameter is mandatory.

```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
```
After the analysis, the ndepend result is stored as an artifact of your build, and to explore it you have two options:

- Download the zipped ndepend artifact from your build summary, and open the generated HTML + js report it contains.
- Access the result from VisualNDepend as explained [here](https://www.ndepend.com/docs/ndepend-github-action). 


## Action options

### Custom NDepend Configuration:
By default the ndepend action uses the out of the box configuration, so this parameter is not mandatory. However, if you need to use a custom configuration you have to specify the `customconfig` parameter 

```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      customconfig: project.ndproj
```
The project.ndproj file must be uploaded to your repository, its path is relative to your repository root. And its name could be any name of your choice. 

### Visual Studio Solution:
By default the ndepend action parse the .sln existing in your repository. However, if many .sln files are found you have to secify which solution to parse. For that you can use `solution` parameter 

```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      solution: src/mysolution.sln
```
The solution path is relative to your repository root. 

### Comparison baseline:
NDepend can report Code &amp; Quality Diff since a baseline. To define the ndepend baseline you have to specify the `baseline` parameter 

```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      baseline: recent
```

The baseline parameter could have these values:

- recent: to compare with the most recent build analyzed by ndepend.
- branchname_recent: To compare with the most recent build of the branch with name 'branchname', for example if you want to compare with the recent build of the branch named `mydev`
```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      baseline: mydev_recent
```
- run number: The run number of a build already analyzed by NDepend., for example if you want to compare with the build having the run number 30
```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      baseline: 30
```

### Quality check:
A Quality Gate is a code quality goal. Such quality goal must be enforced before releasing and eventually, before committing to source control.

A Quality Gate can be seen as a PASS/FAIL criterion for software quality.

A dozen of default [Quality Gates](https://www.ndepend.com/default-rules/NDepend-Rules-Explorer.html?ruleid=PercentageCoverage#!) are proposed by NDepend. They are related to measures like technical debt amount, code coverage or amount of issues with particular severity.

To break the build if at least one quality gate failed, just specify the `stopIfQGFailed` parameter 


```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      stopIfQGFailed: true
```

NDepend makes it easy to modify existing Quality Gates or create your own ones. This is explained [here](https://www.ndepend.com/docs/devops-quality-gate-strategy#create-your-own-quality-gates).

### Coverage folder:
Code coverage data is imported from coverage result files generated by the coverage tools.
To import coverage data, you have to specify the `coveragefolder` parameter 

```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      coveragefolder: ${{ env.GITHUB_WORKSPACE }}/CoverageOut
```
This way the NDepend default rules about [code coverage](https://www.ndepend.com/default-rules/NDepend-Rules-Explorer.html?ruleid=ND1600#!) will be executed. See this [documentation](https://www.ndepend.com/docs/code-coverage ) about coverage technologies and formats supported by NDepend and other code coverage data usage.

### NDepend result artifact retention:
The NDepend action store the analysis result as artifact . However, to optimize the storage of your GitHub account you can use the `retention-days` parameter to specify the retention period of the NDepend artifacts.

```yaml
- name: NDepend
    uses: ndepend/ndepend-action@v1
    with:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  
      license: ${{ secrets.NDependLicense }}
      retention-days: 10
```

