Ultimately automation should trigger on changes in the code repository.
To that end I need a GitHub Webhook to trigger the pipeline.

The entpoint that get the trigger could be either a server that I am running or a service that AWS provides.

If I run my own service I would need to keep that online on my own cost.
Running a t2.nano on AWS costs 60$ per year - really cheap - not cheap enough.

So the next thing I looked into were AWS Code Pipelines. The trigger reaches AWS and I do not need to run anything. After that the code pipeline executed for a few minutes and quits again. So the costs are so low I could not even put a number on it.

Setting up CI / CD was very simple. Effectively AWS automatically connects with the repo and sets up the hooks for you - all you need is to authorize AWS to connect to the Github API in your name.

Since I want to pull my gcc compiler from hub.docker.com I ran into an Issue where docker just would not want to run on the Pipeline Machine. It turns out there are a few configuration options hidden in the Environment section of the Code Pipeline configuration.

Image Override reveals the option to upgrade the pipeline.
I needed:
<ul>
	<li>aws/codebuild/standard 2.0</li>
	<li>Check Priviledged</li>
</ul>
Checking both these options made my pipeline run and complete successfully.

<code>
version: 0.2
phases:
install:
runtime-versions:
docker: 18
pre_build:
commands:
- docker pull gcc
build:
commands:
- docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp gcc make
artifacts:
files:
- ./main
</code>

The output artifacs ended up in an S3 bucket

If you just want to hang out you may have a chance to catch me on  <a href="https://www.twitch.tv/aypahyo" target="_blank">twitch</a> .
Get in touch on <a href="https://twitter.com/Aypahyo" target="_blank">twitter</a>

Here are all the links I ended up inspecting for this:

<a href="https://calculator.s3.amazonaws.com/index.html">S3 Caculator</a>
<a href="https://www.youtube.com/watch?v=3HKbXz0RwSg">Amazon CI/CD Practices</a>
<a href="https://www.youtube.com/watch?v=ry5GmEFa7P8">Expedia's Automated CI/CD</a>
<a href="https://dzone.com/articles/how-to-deploy-a-jenkins-cluster-on-aws-as-part-of">How to Deploy a Jenkins Cluster on AWS in a Fully Automated CI/CD Platform</a>
<a href="https://www.youtube.com/watch?v=O4uw7eIVrZs">Save up to 90% on CI/CD</a>
<a href="https://www.youtube.com/watch?v=7hxe_o6493s">Accelerating DevOps Pipelines with AWS</a>
<a href="https://ifritltd.com/2017/10/29/varrundocker-sock/">/var/run/docker.sock</a>
<a href="https://www.youtube.com/watch?v=jJ4WgX2aOcs">Setting up a CICD Pipeline for Containers on AWS</a>
<a href="https://docs.aws.amazon.com/codepipeline/latest/userguide/codepipeline-user.pdf">CodePipeline User Guide</a>
<a href="https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html">CodePipeline Pipeline Structure Reference</a>
<a href="https://medium.com/@vankhoa011/how-to-apply-ci-cd-by-using-github-codebuild-codepipeline-and-ecs-58192b8322a9">How to apply CI/CD by using GitHub, CodeBuild, CodePipeline and ECS</a>
<a href="https://docs.aws.amazon.com/codebuild/latest/userguide/sample-ecr.html">Amazon ECR Sample for CodeBuild</a>
<a href="https://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html">Troubleshooting CodeBuild</a>
<a href="https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html">Build Specification Reference for CodeBuild</a>
<a href="https://stackoverflow.com/questions/56231957/aws-codebuild-error-while-build-project-yaml-file-error-message-runtime-vers">Runtime version selection is not supported by this build image</a>
<a href="https://docs.aws.amazon.com/codebuild/latest/userguide/change-project.html">Change a Build Project's Settings in CodeBuild</a>
<a href="https://stackoverflow.com/questions/52949413/how-to-run-docker-compose-on-aws-codebuild">How to run docker-compose on AWS CodeBuild?</a>