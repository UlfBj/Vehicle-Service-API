# Vehicle-Service-API (VSAPI)
The VSAPI is a technology agnostic API that defines Services structured in Service Groups. The services are used to invoke Service requests to change the vehicle state, to get information about the current vehicle state, or to obtain vehicle configuration data.
The Services are structured in Service Groups such as Seat, HVAC, etc. A service is defined by its procedure signature that is defined in a langue agnostic format that supports simple translation into most programming languages.

The initial input to the definition of the VSAPI came from the [COVESA CVI - Capabilities](https://wiki.covesa.global/display/WIK4/Capabilities+Project) project,
which at that point contained a definition of the [Seating capabilities](https://wiki.covesa.global/display/WIK4/Capabilities+Project+-++Seating+Capabilities).

The VSAPI is defined in the REDME files in the directory structure in the VSAPI directory, with separate directories for each service group.
There is also for each service group examples of VSAPI implementations in the C and Go languages.
Please observe that these are not complete implementations, but intended to provide some guidance for how implementations may look like.

The VSAPI is currently in an incubation phase, it needs to be extened with many more service group APIs,
more examples ini different languages, etc. before it becomes a complete Vehicle Service API.
