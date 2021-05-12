---
title: "helm"
date: 2021-05-12 11:34
---
[toc]



# Helm

[Helm](https://helm.sh/) is a package manager and application management tool for Kubernetes that packages multiple Kubernetes resources into a single logical deployment unit called a **Chart**.

Helm helps you to:

- Achieve a simple (one command) and repeatable deployment
- Manage application dependency, using specific versions of other application and services
- Manage multiple deployment configurations: test, staging, production and others
- Execute post/pre deployment jobs during application deployment
- Update/rollback and test application deployments



# Installation

Before we can get started configuring Helm, we’ll need to first install the command line tools that you will interact with. To do this, run the following:

```sh
curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

We can verify the version

```sh
helm version --short
```

Let’s configure our first Chart repository. Chart repositories are similar to APT or yum repositories that you might be familiar with on Linux, or Taps for Homebrew on macOS.

Download the `stable` repository so we have something to start with:

```sh
helm repo add stable https://charts.helm.sh/stable
```

Once this is installed, we will be able to list the charts you can install:

```sh
helm search repo stable
```

Finally, let’s configure Bash completion for the `helm` command:

```sh
helm completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
source <(helm completion bash)
```



# Charts

Helm uses a packaging format called [Charts](https://helm.sh/docs/topics/charts/). A Chart is a collection of files and templates that describes Kubernetes resources.

Charts can be simple, describing something like a standalone web server (which is what we are going to create), but they can also be more complex, for example, a chart that represents a full web application stack, including web servers, databases, proxies, etc.

Instead of installing Kubernetes resources manually via `kubectl`, one can use Helm to install pre-defined Charts faster, with less chance of typos or other operator errors.

Chart repositories change frequently due to updates and new additions. To keep Helm’s local list updated with all these changes, we need to occasionally run the [repository update](https://helm.sh/docs/helm/helm_repo_update) command.

To update Helm’s local list of Charts, run:

```
# first, add the default repository, then update
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

And you should see something similar to:

```
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```





## SEARCH charts REPOSITORIES

Now that our repository Chart list has been updated, we can [search for Charts](https://helm.sh/docs/helm/helm_search/).

To list all Charts:

```sh
helm search repo
```

That should output something similar to:

```
NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION
stable/acs-engine-autoscaler            2.2.2           2.1.1                           Scales worker...
stable/aerospike                        0.3.2           v4.5.0.5                        A Helm chart...
...
```



You can see from the output that it dumped the list of all Charts we have added. In some cases that may be useful, but an even more useful search would involve a keyword argument. So next, we’ll search just for `nginx`:

```sh
helm search repo nginx
```

That results in:

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
stable/nginx-ingress            1.30.3          0.28.0          An nginx Ingress ...
stable/nginx-ldapauth-proxy     0.1.3           1.13.5          nginx proxy ...
stable/nginx-lego               0.3.1                           Chart for...
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop...
...
```



This new list of Charts are specific to nginx, because we passed the **nginx** argument to the `helm search repo` command.

Further information on the command can be found [here](https://helm.sh/docs/helm/helm_search_repo/).





## ADD THE BITNAMI REPOSITORY

In the last slide, we saw that nginx offers many different products via the default Helm Chart repository, but the nginx standalone web server is not one of them.

After a quick web search, we discover that there is a Chart for the nginx standalone web server available via the [Bitnami Chart repository](https://github.com/bitnami/charts).

To add the Bitnami Chart repo to our local list of searchable charts:

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Once that completes, we can search all Bitnami Charts:

```sh
helm search repo bitnami
```

Which results in:

```
NAME                                    CHART VERSION   APP VERSION             DESCRIPTION
bitnami/bitnami-common                  0.0.8           0.0.8                   Chart with...
bitnami/apache                          4.3.3           1.10.9                  Chart for Apache...
bitnami/cassandra                       5.0.2           3.11.6                  Apache Cassandra...
...
```

Search once again for nginx

```sh
helm search repo nginx
```

Now we are seeing more nginx options, across both repositories:

```
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/nginx                           5.1.6           1.16.1          Chart for the nginx server
bitnami/nginx-ingress-controller        5.3.4           0.29.0          Chart for the nginx Ingress...
stable/nginx-ingress                    1.30.3          0.28.0          An nginx Ingress controller ...
```

Or even search the Bitnami repo, just for nginx:

```sh
helm search repo bitnami/nginx
```

Which narrows it down to nginx on Bitnami:

```
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/nginx                           5.1.6           1.16.1          Chart for the nginx server
bitnami/nginx-ingress-controller        5.3.4           0.29.0          Chart for the nginx Ingress...
```

In both of those last two searches, we see

```
bitnami/nginx
```

as a search result. That’s the one we’re looking for, so let’s use Helm to install it to the EKS cluster.





# Appendix

https://www.eksworkshop.com/beginner/060_helm/helm_nginx/searchchart/

