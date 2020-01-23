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

If you just want to hang out you may have a chance to catch me on  [twitch](https://www.twitch.tv/aypahyo) .
Get in touch on [twitter](https://twitter.com/Aypahyo)

Here are all the links I ended up inspecting for this:

[S3 Caculator](https://calculator.s3.amazonaws.com/index.html)
[Amazon CI/CD Practices](https://www.youtube.com/watch?v=3HKbXz0RwSg)
[Expedia's Automated CI/CD](https://www.youtube.com/watch?v=ry5GmEFa7P8)
[How to Deploy a Jenkins Cluster on AWS in a Fully Automated CI/CD Platform](https://dzone.com/articles/how-to-deploy-a-jenkins-cluster-on-aws-as-part-of)
[Save up to 90% on CI/CD](https://www.youtube.com/watch?v=O4uw7eIVrZs)
[Accelerating DevOps Pipelines with AWS](https://www.youtube.com/watch?v=7hxe_o6493s)
[/var/run/docker.sock](https://ifritltd.com/2017/10/29/varrundocker-sock/)
[Setting up a CICD Pipeline for Containers on AWS](https://www.youtube.com/watch?v=jJ4WgX2aOcs)
[CodePipeline User Guide](https://docs.aws.amazon.com/codepipeline/latest/userguide/codepipeline-user.pdf)
[CodePipeline Pipeline Structure Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
[How to apply CI/CD by using GitHub, CodeBuild, CodePipeline and ECS](https://medium.com/@vankhoa011/how-to-apply-ci-cd-by-using-github-codebuild-codepipeline-and-ecs-58192b8322a9)
[Amazon ECR Sample for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-ecr.html)
[Troubleshooting CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html)
[Build Specification Reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)
[Runtime version selection is not supported by this build image](https://stackoverflow.com/questions/56231957/aws-codebuild-error-while-build-project-yaml-file-error-message-runtime-vers)
[Change a Build Project's Settings in CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/change-project.html)
[How to run docker-compose on AWS CodeBuild?](https://stackoverflow.com/questions/52949413/how-to-run-docker-compose-on-aws-codebuild)