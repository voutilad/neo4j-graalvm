# Neo4j + GraalVM = ?!
This is just me futzing around with trying to run Neo4j on GraalVM mostly so
I can test an idea about having dynamic stored procedures written in
JavaScript or Python.

# Questions I'm Looking to Answer
My current hypotheses, etc...

## Replacing OpenJDK with GraalVM
Licensing worries aside, from my reading it seems certain builds of GraalVM
should be compatible with the Java11 implementations provided by OpenJDK.

### My idea: swap out the guts in the Dockerfile
Should be easy then to take the official Oracle GraalVM base Docker image and
use it as the basis for the Neo4j image. Adapting the "official" Dockerfiles
should be relatively easy.

### Things I found swapping out OpenJDK for GraalVM
Some minor things so far:

1. Base image is Oracle Linux based, not a Debian-based userland
2. Given it's Oracle Linux, packages like `gosu` are not available
3. Some minor changes to the userland tools (`addgroup` -> `groupadd`)

Nothing substantial.

# License, etc.
The Dockerfiles are from the officially published repo for Neo4j images at
https://github.com/neo4j/docker-neo4j-publish, which are provided under the
Apache-2.0 license. Thus any such nonsense in this project is provided under
the same Apache-2.0 license terms.
