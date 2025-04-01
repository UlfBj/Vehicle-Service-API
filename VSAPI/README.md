# VSAPI Overview
The VSAPI is a technology agnostic API that defines Services structured in Service Groups. The services are used to invoke Service requests to change the vehicle state, to get information about the current vehicle state, or to obtain vehicle configuration data.
The Services are structured in Service Groups such as Seat, HVAC, etc. A service is defined by its procedure signature that is defined in a langue agnostic format that supports simple translation into most programming languages.
```
<output-datatype> procedure-name(<input-datatype input-parameter-name>)
```
Where
* ‘output-datatype’ declares the data type of the output parameter. There can be zero or one output parameter. A complex data type may be used that defines multiple output parameters.
* ‘procedure-name’ is the name of the procedure that is invoked to actuate the service.
* ‘input-datatype input-parameter-name’ are the data type and name of an input parameter. There can be zero or more input parameters, separated by a comma.

The datatypes used in the signatures can be any of the common datatypes such as int/uint, intx /uintx(x=8/16/32/64), float, double, boolean, string, but also complex datatypes such as structs with members being any of the common datatypes, or complex datatypes.

The implementations of these procedures are out-of-scope for the CVSAPI. There can be multiple implementations of the procedure, that can differ on e. g. the vehicle interface that is used for serialization and transport of the messages between the calling client and the server actuating the service. The procedure typically executes in the context of the client, while the server actuating the service typically executes in a different procedure, and likely on a different execution platform, se the figure below.

The figure below shows an architecture where the service endpoint is deployed on the vehicle.
![VSAPI architecture with remote service endpoint](/images/VSAPI-architecture-remote-endpoint.jpg)
Service endpoint implementations that are deployed at the vehicle are typically OEM proprietary.
Common and public service endpoint implementations can alternatively be deployed at the client side in an architecture as shown in the figure below.
![VSAPI architecture with remote service endpoint](/images/VSAPI-architecture-local-endpoint.jpg)

# VSAPI Common Rules
The VSAPI builds on the following rule set:
* The output parameter shall at least contain a status parameter of the datatype status_t.
* The datatype status_t shall be an enumeration with the following semantics:
   * NOT_STARTED = 1
   * ONGOING = 2
   * SUCCESS = 0
   * FAILURE = -1
* The status_t value FAILURE may be extended with multiple negative values.
   * The symbolic value shall be FAILURE_X where X shall refer to the error reason.
* All service procedures shall be synchronous, i. e. they shall return immediately.
* If the service execution has a temporal duration such that its termination is later than the procedure return, then the output parameter shall contain a ‘session identifier’, a reference to the invoked execution of the service.
* All service groups shall expose a procedure that a client can use to asynchronously terminate an ongoing service execution. The procedure shall have the signature:
   * status_t <service-group-name>_Terminate(int sessionId)
* The input to a service that returns a session identifier shall include a pointer to a procedure to which the service can issue callbacks. A client may set this pointer to the nil value in which case the service will not issue any callbacks.
* A service that returns a session identifier shall issue asynchronous callbacks during its execution. It must issue a callback a final at its termination, whether the termination has been successful or failed and it may issue more callbacks. The callback must contain a status_t input parameter and may contain other input parameters.
* If procedures of a service group require input that is dependent on the configuration of the target vehicle, then the service group shall expose procedures that enables a client to retrieve the required configuration data. These procedures shall have the signature:
```
    <output-datatype> <service-group-name>_Property_X()
```
Where X describes the wanted type of configuration data, and ‘output-datatype’ shall contain the requested configuration data together with a status_t parameter.
* A service may include in the output parameter data that represents the vehicle signals that may be changed by the execution of the service. The format of this data shall be a string array where each string represents the path of the signal as defined by the [COVESA VSS]() project.
