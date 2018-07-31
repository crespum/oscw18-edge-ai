# Edge computing AI resources for cubesats

## Proposal for [Open Source Cubesat Workshop 18](https://2018.oscw.space)

The number of cubesats and small satellites sent to orbit is rapidly growing. Not only that, but the market trends toward the launch constellations of hundreds of satellites in LEO. This situation will become very challenging in terms of operations and data management for all the New Space companies. In order to overcome these problems, we need to increase the level of autonomy of satellites so that they don't depend on regular human intervention to function neither on a ground to space telecommunication link. These factors are generally very hard to scale.

During the last years, we've witnessed big advances in the so called edge computing industry, which aims to give unprecedented computational power to devices in constrained environments. These devices are limited in many aspects, including power availability and/or connectivity, just as satellites are. The ultimate goal of edge computing is to reduce their dependency on a central node to extract and analyse the data gathered.

If we consider ground stations as the central nodes of constellations, and satellites the end devices, we can leverage these techniques for using them into space. There are many benefits in doing so. We are particularly interested in one of them, the usage of artificial intelligence onboard of small satellites. This option [has already been explored](https://pdfs.semanticscholar.org/92c0/2e88c6e3ce87967057118ef3319018e3e2fb.pdf) in the past by researches, but only now is becoming a reality for every company. Cubesat missions that embed high performance computing devices are already planned to test these devices in Earth orbit.

The availability of AI onboard can help small sats reach a new level of autonomy. Satellite operations can be simplified by using it to identify complex time-series or to detect anomalies that could harm the satellites and act accordingly without needing to download all the telemetry. There's also a lot of potential for applications that use a big amounts of data such as Earth Observation. Thanks to AI we could move part of the application layer that is currently being executed on the ground to the space segment. The images can be analysed onboard and we could download just the results. This would save a lot of bandwidth as well as help up with frequency coordination.

These are just two straight forward examples, but there are more. AI can also be extremely helpful on [deep space missions](https://www.jpl.nasa.gov/news/news.php?release=2018-090) or even for the [autonomous coordination of constellations](https://pdfs.semanticscholar.org/92c0/2e88c6e3ce87967057118ef3319018e3e2fb.pdf). It's an option worth being explored.

We found really difficult to compare edge computing tools on paper. Each manufacturer takes a different approach to describe their specifications, so we decided to do it empirically. We took three different COTS solutions (an NVIDIA Jetson TX1, an Intel Movidius and a library for microprocessors using Mbed OS called uTensor) and compared them in terms of accuracy, power consumption, processing time and other parameters.

For the comparison to be fair we created an AI model and exported it to the three platforms. Then we created a set of inputs and fed the model with them to infer the outputs. During this process we monitored the parameters under test. The results is what we will be presenting in this conference.

