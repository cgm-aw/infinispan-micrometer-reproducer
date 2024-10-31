# infinispan-micrometer-reproducer
Reproduces bug in Infinispan / Micrometer

## How to reproduce
This is an almost empty project created with https://start.spring.io, all I did was
adding `infinispan-spring-boot3-starter-embedded`.

Just start `MicrometerReproducerApplication#main()` and open up the Actuator endpoint:
http://localhost:8082/actuator/prometheus?includedNames=cache_puts_total

You will see this error in the logs:  
`java.lang.IllegalArgumentException: -1.0: counters cannot have a negative value`

Now edit the pom.xml, and switch to version `1.12.11` for `micrometer-registry-prometheus`.

Start the application again, open the Actuator endpoint and see actual data:  
`cache_puts_total{cache="default",cache_manager="springEmbedded",name="default",} -1.0`

Micrometer clearly changed the behavior to no longer support negative values. 
I see some solutions:
1. Disable the default cache -> I did not find any way to do this
2. Activate statistics for the default cache -> I did not find any way to do this, either
3. Report this to Micrometer -> I actually don't think this is a bug on their side, negative values are just not a real metric