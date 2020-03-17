
# sample-tekton

## Tekton Pipelines

See:
- https://github.com/tektoncd/pipeline
- https://github.com/tektoncd/pipeline/blob/master/docs/install.md
- https://github.com/tektoncd/pipeline/blob/master/docs/tutorial.md
- https://github.com/tektoncd/cli

Install Tekton Pipeline:

~~~
$ cd tekton-pipelines
$ curl -L -o tekton-pipeline-release.yaml        https://github.com/tektoncd/pipeline/releases/download/v0.11.0-rc2/release.yaml
$ curl -L -o tekton-pipeline-release.notags.yaml https://github.com/tektoncd/pipeline/releases/download/v0.11.0-rc2/release.notags.yaml
$ kubectl apply -f tekton-pipeline-release.yaml
$ kubectl -n tekton-pipelines get pods
~~~

Install Tekton CLI:
- TektonCD CLI v0.8.0 is compatible with TektonCD Pipeline v0.10.1.
- Also create a link so that `kubectl` will discover it as a plugin.

~~~
$ curl -LO https://github.com/tektoncd/cli/releases/download/v0.8.0/tkn_0.8.0_Linux_x86_64.tar.gz
$ sudo tar xvzf tkn_0.8.0_Linux_x86_64.tar.gz -C /usr/local/bin/ tkn
$ sudo chown root:root /usr/local/bin/tkn
$ sudo ln -s /usr/local/bin/tkn /usr/local/bin/kubectl-tkn
$ rm tkn_0.8.0_Linux_x86_64.tar.gz
~~~

Tutorial

~~~
$ cd tekton-pipelines
$ kubectl apply -f tutorial/echo-hello-world.yaml
$ kubectl apply -f tutorial/echo-hello-world-task-run.yaml 
$ tkn taskrun describe echo-hello-world-task-run
$ tkn taskrun logs echo-hello-world-task-run
~~~

## Tekton Dashboard

See: https://github.com/tektoncd/dashboard

Install Tekton Dashboard:

~~~
$ cd tekton-dashboard
$ curl -L -O https://github.com/tektoncd/dashboard/releases/download/v0.5.3/tekton-dashboard-release.yaml
$ curl -L -O https://github.com/tektoncd/dashboard/releases/download/v0.5.3/tekton-dashboard-release-readonly.yaml
$ kubectl apply -f tekton-dashboard-release-readonly.yaml
~~~

Access Dashboard locally:

~~~
$ kubectl port-forward -n tekton-pipelines svc/tekton-dashboard 8081:9097
# open "http://localhost:8081"
~~~

## Tekton Triggers

See: 
- https://github.com/tektoncd/triggers
- https://github.com/tektoncd/triggers/blob/master/docs/install.md
- https://github.com/tektoncd/triggers/tree/master/docs/getting-started
- https://github.com/tektoncd/triggers/blob/master/examples/README.md

Install Tekton Triggers:

~~~
$ cd tekton-triggers
$ curl -L -o tekton-triggers-release.yaml https://github.com/tektoncd/triggers/releases/download/v0.3.1/release.yaml
$ kubectl apply -f tekton-triggers-release.yaml
$ kubectl -n tekton-pipelines get pods
~~~

## Sample Pipeline 1

~~~
$ cd sample-1/
$ kubectl create namespace sample-1
$ kubectl -n sample-1 apply -f sample-pipeline.yaml
$ kubectl -n sample-1 apply -f sample-pipelinerun.yaml
$ kubectl -n sample-1 delete pipelinerun my-run-1

$ kubectl -n sample-1 apply -f sample-trigger-rbac.yaml
$ kubectl -n sample-1 apply -f sample-trigger.yaml
$ kubectl -n sample-1 port-forward svc/el-my-listener 8082:8080
$ curl 'http://localhost:8082/' -i -X POST \
  -H 'Content-Type: application/json' \
  -H 'X-MY-HEADER: my-value-from-header' \
  -d '{ "head_commit": { "id": "master" }, "repository": { "url": "https://github.com/phoswald/sample-tekton.git" }, "my-param": "my-value-from-payload" }'
$ curl 'http://localhost:8082/' -i -X POST \
  -H 'Content-Type: application/json' \
  -H 'X-MY-HEADER: my-value-from-header' \
  -d '{ "head_commit": { "id": "959f8cb5361cafde34a989f26626b1de37345acb" }, "repository": { "url": "https://github.com/phoswald/sample-tekton.git" }, "my-param": "my-value-from-payload" }'
~~~

Run or Build Website locally:

~~~
$ docker run -it --rm -p 8082:80 -v $(pwd)/html:/usr/share/nginx/html:ro nginx:alpine

$ docker build -t sample-website . 
$ docker run -it --rm -p 8082:80 sample-website:latest
~~~
