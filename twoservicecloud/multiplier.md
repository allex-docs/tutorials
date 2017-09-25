# Developing the Multiplier Service

The Multiplier Service has a twofold task:

1. Keep listening on the Config Service for the `multiplier` property of its `state`.
2. Respond to RMIs coming from clients in order to multiply the numbers they provide by the value of the `multiplier` Config `state` property.

We'll solve these tasks independently, one by one.

First we will make the Multiplier Service [Listen to the Config Service](listentoconfig.md).

Then we will [Implement the multiply Method](implementmultiply.md).
