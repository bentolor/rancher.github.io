---
title: Adding Services
layout: rancher-default
---

## Adding Services
---

With Rancher, you can add multiple services in a stack to make an application. With this guide, we'll assume you've already created a [stack]({{site.baseurl}}/rancher/rancher-ui/applications/stacks/), set up your [hosts]({{site.baseurl}}/rancher/rancher-ui/infrastructure/hosts/), and are ready to build your application. 

We'll walk through how to create a Wordpress application linked to a MySQL database. Inside your stack, you add a service by clicking the **Add Service** button. Alternatively, if you are viewing the stacks at the stack level, the same **Add Service** button is visible for each specific stack. 

You will need to provide a **Name** and if desired, **Description** of the service. In the **Scale** section, you can use the slider for the specific number of containers you want launched for a service. Alternatively, you can select **Always run one instance of this container on every host**. With this option, your service will scale for any additional hosts that are added to your [environment]({{site.baseurl}}/rancher/configuration/environments/). If you have scheduling rules in the **Advanced Options** -> **Scheduling**, Rancher will only start containers on the hosts that meet the host labels rules. If you add a host to your environment that doesn't meet the scheduling rules, a container will not be started on the host.

Provide the **Image** to use. You can use any image on [DockerHub](https://hub.docker.com/) as well as any [registries]({{site.baseurl}}/rancher/configuration/registries) that have been added to Rancher. The syntax for image name would match any `docker run` commands. 

Syntax of image names. By default, we pull from the docker registry. If no tag is specified, we will pull the latest tag. 

`[registry-name]/[namespace]/[imagename]:[version]`

We'll start by creating our MySQL database service with only 1 container.

### Service Options

Just like adding individual [containers]({{site.baseurl}}/rancher/rancher-ui/infrastructure/containers/), any options that `docker run` supports, Rancher also supports! Port mapping and service links are shown on the main page, but other options are within the **Advanced Options** section. 

Assuming that your host is using its public IP, when we are mapping ports, we are creating the ability to access the container through the host IP. In the **Port Map** section, you will define the public ports that will be used to communicate with the container. You will also be defining which port will be exposed on the container. When mapping ports for a container to a host, Rancher will check to see if there are any port conflicts. 

When using port mapping, if the scale of your service is more than the number of hosts with the available port, your service will be stuck in an activating state. The service will continue to try and if host/port becomes available, the container will start and finish activating.

If other services have already been created, you can add links to the service. Linking services will link all containers in one service to all containers in another service. It acts just like the `--link` functionality in a `docker run` command. 

For the MySQL service, we'll need to add the `MYSQL_ROOT_PASSWORD` as an environment variable and provide the key and value. This field is located in the **Advanced Options**.

There is also the ability to add labels to every container in a service as well as apply scheduling rules. More details about labels and scheduling can be read [here]({{{{site.baseurl}}/rancher/rancher-ui/scheduling/).

### Starting Services

After filling out the information for your service, click **Create**. Creating the service will not automatically start the service. Currently, any linked services that are dependent on environment variables will need to be started in the correct order. This means that you will need to start the database before any services that are linked to the database. <!--This allows you to create multiple services and when your application is ready, you can start all services at once!-->

Now that we've launched our database, we'll add the Wordpress service to our stack. This time, we'll launch 3 containers in our service using the Wordpress image. We will not expose any ports in our Wordpress service as we will want to load balance this application. Since we've already created the database service, we'll pick the database service in the **Service Links** and select the name _mysql_. Just like Docker, Rancher will set up the environment variables in the WordPress image when linking two containers together, by naming the database as _mysql_.

Click on **Create** and our Wordpress app is ready to be started! In our wordpress app, it shows us that the database service is linked. 

<!-- ### Starting Services

There are several ways to start services. You can immediately start it after creating the service by clicking on the **Start** link within the service or even using the **Start** option in the service's dropdown menu. You can also wait until after you have created all your services and start them all at once using the **Start Services** in the dropdown menu of the Stack. -->

### Load Balancing Services

At this point, it would make sense to load balance our Wordpress service. Let's move on to how to [add a load balancer]({{site.baseurl}}/rancher/rancher-ui/applications/stacks/adding-balancers/) into our stack.
