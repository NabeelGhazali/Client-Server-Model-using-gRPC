# Client-Server-Model-using-gRPC


## Abstract

This report for the assignment of programming on the cloud focusing on developing a client server program which serve a workload query scenario.  Client server communication can be done through three ways such as socket, remote procedure calls and pipes. This assignment uses the report procedure call developed by google known as gRPC. the development of gRPC based service is done using NodeJs. The client sends in a request for workload which the server intercepts and then the server responds back with the required data requested by the client. In our case we have four datasets namely DVD-testing, DVD-training, NDBench-testing and NDBench-training. The datasets contain 4 columns. The basic process of the service is that the client sends a request to the server for workload which includes RFW ID, Benchmark Type and Batch Unit etc. the server receives the request for this workload and sends a response back to the server with the data requested by the client from the datasets. These requests and responses will be discussed in detail in this report

## Introduction

GRPC is an open source remote procedural call (RPC) framework developed by google. There are a few reasons to use GRPC rather than using REST API. GRPC is a better choice when micro service, scalability, low latency and load balancing is concerned. GRPC uses Protocol Buffer for serializing structured data. There are four different types of remote procedural calls such as Unary, Server Streaming, Client Streaming and Bidirectional Streaming. Our main focus in the assignment is Unary RPC where the client sends in a request to the server and the server processes the request and sends the response back to the client.
The goal of the assignment is simple, to fetch the data from the server that is required by the clients. The clients make requests which includes RFW ID, Benchmark Type (such as NDBench or DVD), Workload Metric (data from the columns of the dataset), Batch ID, Batch Size, Data Type (training or testing data) and Data Analytics (max, min, std or percentiles). the server intercepts and interprets the request. Using the Benchmark Type and Data Type the server loads the file. The server then makes batches of the dataset and processes it to respond the client. The code to process the data and fetch the required data will be discussed more in the later sections.

## Data Model

The process flow design diagram of gRPC shows the communication between client and server. The transport protocol layer is the communication medium between client and server, there are many transport layers but the most commonly used in gRPC is HTTP/2. In HTTP/2, a single TCP connection is used for communication. In gRPC, client stub receives the local procedure call from the client which contains the message that needs to be sent to server. Then the serialization of message is done by the client stub which is known as marshalling. The message is serialized into binary form by the proto buffer, also known as protobuf which is only readable by machine. Then this client stub sends the message to the client library and the message is passed on to the server run time library by the transport layer (HTTP/2).

[image]
 
When the message is received by the server runtime library it calls the server stub method. This method involves in the unmarshalling the request message from the client and find the required information in the server. After completing the process, server send the response package from the server stub to the client runtime library through HTTP/2 and then client get the requested data.
 
## Data Serialization and Deserialization

Data serialization and deserialization is used to communicate over the client server program. Serialization converts the object into byte which is what the machine understands. The deserialization of the data is the reverse process which is converting the byte stream to an object which the client can interpret. There are different forms of serialization deserialization methods namely JSON, XML and Protocol Buffers. Protocol buffers are used by GRPC. using the binary format to communicate in the client server program is of an advantage as binary format is the closest to how machine represents data. Protocol buffers are fast and consumes less CPU which improves the latency and battery life of the devices.
The figure 1 and 2 both are snippets of the protobuf defined in the.proto file used in the assignment. The syntax keyword defines the version of the protobuff which is proto3 in this case. The message is an object which defines the schema and can contain nested messages. In this case we have message defined for Result, DataPoint, Batch, RFW and RFD. In each class we define the data type of the object. We then equate the objects with the sequence in which the objects will be serialized, this is how the protocol buffer maintains the compatibility.

[image]

Figure __ contains a service by the name of Batch Transfer Service which basically calls out remote procedure. The create batch function is called for the server to create batches in the data set and when the server has successfully created batches it returns a callback saying that the batches have been created. In get batches function the client send RFW request and gets RFD response. Similarly, is the case for the function analyze, Create batch and get batch are both request functions while analyze is the response function

[image]

## Implementation

This section entails a detailed discussion on the entire service, the working of client server and the working the servers end with the data.
There are a few libraries and functions that are very important. Protoloader library is a utility package for loading the protofile for use with GRPC. To access all the built-in functions and methods we used the GRPC library.  BindAsync function is used to bind the server at a specific port. In our case, we have an insecure credentials object because the SSL is disabled. The port in the bindAsync is the port on which the server should bind on using the format “address: port”. For the analysis on the datasets Percentile library has been imported.

Figure __ shows all the files included in the assignment including the client implementation, server implementation, datasets stored in json and .protofile.


[image]

Figure __ shows the client implementation file. the RFW is a variable which is used to make the request from the client side to the server. We can also see the create batches request function where we send the server a request to create batches on the data set. The dataset is identified by the server using the benchmark type and data type mentioned in RFW request of the client. The server in this case either responds with the callback message “batches have been created” or with an error. The similar is true for get batches request function where the client requests the data from the batches and the server responds either by the data requested or an error.

[image]

The server implementation file contains all the code necessary to give a response back to the client. The first task that server does after getting a request from the client for create batches is to read the file by identifying the benchmark type and data type. The server then applies the nested for loop to store the segmented data as batches in the list batches and responds with a callback message to the client that batches have been created.
Upon the get batches request from the client, the server responds with Batch ID, Batch size etc. the server then runs the data analytics on the data sets and provides the response to the client
