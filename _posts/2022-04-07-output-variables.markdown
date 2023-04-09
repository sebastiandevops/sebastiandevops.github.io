---
layout: post
title: "Output variables in Azure Devops"
subtitle: "Using output variables during pipeline execution in Azure DevOps Classic Editor."
date: 2023-04-07 00:45:13 -0400
# background: '/img/posts/chilivim.png'
---

<style>
  p {
    text-align: justify;
  }
  .img-container {
    text-align: center;
  }
  img {
    width: 500px;
  }
  span {
    display: block;
  }
</style>

<p>Variables are a powerful azure devops component. You can define your own variables using libraries that you can import later on your pipelines and those libraries will be available through all pipelines within the organization.</p>

<div class="img-container">
  <img src="/img/posts/2023-04-07-output-variables/libraries.png">
</div>

<p>But this is not the only way to use variables in Azure, also you can define your own variables within the pipeline definitions. This key-value pairs will be available in the scope of your pipeline. Additionally, you can set key-value pairs in the specific pipeline tasks to use these variables just in that task.</p>

<p>The thing is that sometimes you need to get some value during the pipeline execution and store that value in a variable. But, what happens if you also need to use that variable in the pipeline's next step? For instance, if you export the variable, you will note that this value that you previously stored in some variable and exported it, is not available in the next step no matter what you did before. It is because this output variables live just in the scope of the task.</p>

<p>So, if you need to store some value during the pipeline execution, you can store it in a variable and then use an Ad-hoc script to publish to the output variables. In the below example, I will store the hostname in a variable called <code>MY_VAR</code> and then I will use <code>##vso</code> command to publish it. In order to accomplish this, I will use a Command Line Task to publish the output variable and then I will use a Bash Script task to retrieve that value from the previous task.</p>

#### Command Line Task:
```
export MY_VAR=$(hostname)
echo "This is the hostname stored in MY_VAR: $MY_VAR"
echo "##vso[task.setvariable variable=MY_OUTPUT_VAR;isOutput=true;]$MY_VAR"
```
<p>One important thing to consider is that you have to define a reference name as a namespace that will be used to get the right variable in the next task:</p>

<div class="img-container">
  <img src="/img/posts/2023-04-07-output-variables/reference_name.png">
</div>

<p>As command output you will get this in the raw log in the Command Line Task:</p>

```
Starting: Command Line Script
==============================================================================
Task         : Command line
Description  : Run a command line script using Bash on Linux and macOS and cmd.exe on Windows
Version      : 2.212.0
Author       : Microsoft Corporation
Help         : https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/command-line
==============================================================================
Generating script.
========================== Starting Command Output ===========================
/usr/bin/bash --noprofile --norc /home/vsts/work/_temp/33323ee0-3fd9-4343-966e-159b19d00c5d.sh
This is the hostname stored in MY_VAR: fv-az48-991
Finishing: Command Line Script
```
<p>And in the Bash Script task, in which you call the output variable from the previous task you will note that when we call MY_OUTPUT_VAR, it retrieves the same value as MY_VAR, which means that we successfully store MY_VAR as an output variable.</p>

<p>Also, you will note that in order to pick the variable you have to access there using the reference name and then the variable that you define in the previous taks.</p>

#### Bash Script Task:
```
echo "This is the hostname stored as output variable in MY_OUTPUT_VAR: $(OUT.MY_OUTPUT_VAR)"
```


```
Starting: Bash Script
==============================================================================
Task         : Bash
Description  : Run a Bash script on macOS, Linux, or Windows
Version      : 3.214.0
Author       : Microsoft Corporation
Help         : https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/bash
==============================================================================
Generating script.
========================== Starting Command Output ===========================
/usr/bin/bash /home/vsts/work/_temp/60b34d90-0d1c-4f71-8aed-d027a0da0da2.sh
This is the hostname stored as output variable in MY_OUTPUT_VAR: fv-az48-991
Finishing: Bash Script
```
