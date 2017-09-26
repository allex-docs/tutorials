# Developing a Cloud with Two Services

In this lesson, we will develop an AllexJS cloud that will run 2 services:

1. A Config service that will hold the configuration for the Cloud. The only configuration parameter will be the `multiplier` parameter. This service will be an instance of the `allex_leveldbconfigservice` Service that is available as a part of the public AllexJS core. No development is needed for this service. It is just the configuration parameters for the Config instance that will be declared via the `propertyhash` for the `Config` in the `needs` file.
2. A Multiplier service that will multiply given numbers by the `multiplier` set in the Config service. This service will listen to the Config service, for the `multiplier` configuration parameter in particular. Special care will be taken for the _boundary cases_, when the Multiplier service might not be in possession of the `multiplier` configuration parameter value.

The structure of the cloud is

![Structure of the cloud](http://i.imgur.com/wfSob4j.png)

The functionality of this cloud resembles the functionality of the [Plain Multiplier Service](../basic_development/plainservice.md).
Of course, the functionality of this cloud could have been achieved by improving the Plain Multiplier Service, but the goal of this lesson is to introduce the basic development reasoning related to building _cloud friendly_ services.

## Building the Cloud

First you will [Initialize the Project](init.md).

Then you will [Develop the Multiplier Service](develop_service.md).

After that you should [Set the `multiplier` configuration parameter](set_multiplier.md).

Finally, you can [Consume the `Multiplier` service](consume_multiplier.md).

Because the structure and the purpose of the cloud is not trivial any more, we will present a [Testing Sequence](testing_sequence.md) that will walk the cloud through several indicative states.
