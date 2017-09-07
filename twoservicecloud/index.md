# Developing a Cloud with Two Services

In this lesson, we will develop an AllexJS cloud that will run 2 services:

1. A Config service that will hold the configuration for the Cloud. The only configuration parameter will be the `multiplier` parameter. This service will be an instance of the `allex_leveldbconfigservice` Service that is available as a part of the public AllexJS core.
2. A Multiplier service that will multiply given numbers by the `multiplier` set in the Config service. This service will listen to the Config service, for the `multiplier` configuration parameter in particular. Until the value of the `multiplier` configuration parameter is obtained, multiplication requests will fail. Once the `multiplier` configuration parameter is obtained, multiplication requests will succeed with the result of multiplying the number provided by the value of the `multiplier` configuration parameter. 

First you will [Initialize the Project](init.md).

Then you will [Develop the Multiplier Service](multiplier.md).

After that you should [Set the `multiplier` configuration parameter](set_multiplier.md).

Finally, you can [Consume the `Multiplier` service](consume_multiplier.md).


