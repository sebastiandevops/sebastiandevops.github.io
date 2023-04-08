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
</style>

<p>Variables are a powerful azure devops component. You can define your own variables using libraries that you can import later on your pipelines and those libraries will be available through all pipelines within the organization.</p>

<div class="img-container">
  <img src="/img/posts/2023-04-07-output-variables/libraries.png">
</div>

<p>But this is not the only way to use variables in Azure, also you can define your own variables within the pipeline definitions. This key-value pairs will be available in the scope of your pipeline. Additionally, you can set key-value pairs in the specific pipeline tasks to use this variables just in that task.</p>

<p>The thing is that sometimes you need to get some value during the pipeline execution and store that value in a variable. But, what happens if you also need to use that variable in the pipeline's next step? For instance, if you export the variable, you will note that this value that you previously stored in some variable and exported it, is not available in the next step no matter what you did before. It is because this output variables lives just in the scope of the task.</p>

<p></p>
