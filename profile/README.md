It is preconised to see this doc on our postman workspace for a better links to requests.
Group SE2-04 : Hugo MOREAU - Anthony PABOEUF - Jules VANIN - Thibault HENRION

The following documentation concerns the architecture project given in EFREI's 2024 software architecture course given for M2 the Software Engineering major.

**The goal of this service** is to manage the payment system for a bowling company having multiple bowling parks all across the country.  
In a nutshell, the payment system is the following : when at a given bowling alley in a given bowling park, customers can scan the alley's QR code and order from the bowling's park catalog directly from their smartphones. At the end of the bowling session, by scanning the QR code again the customers can see how much they need to pay and choose the way they want to pay (a given amount, a given part of the total amount, for their specific order(s).

# Description

**The solution is composed of** 7 microservices and 1 mock of a payment processor (i.e. Stripe). The microservices are the following :

- User Microservice
    

Git : [https://github.com/ArchitectureProject/UserAuthentMicroservice](https://github.com/ArchitectureProject/UserAuthentMicroservice)

The user microservice was made using SpringBoot and PostgreSQL. It aims to **manage the users** of the app. **Those users can be customers or agents** (managers of a bowling park). It is the microservice that manage authentication by having **a login method that returns a JWT** used all across the app to manage authorizations. It also **exposes the public key** with which those JWTs are signed for the other microservices to validates the bearer tokens.

- BowlingPark Microservice
    

Git : [https://github.com/ArchitectureProject/BowlingParkMicroservice](https://github.com/ArchitectureProject/BowlingParkMicroservice)

The bowling park microservice was made using C# and PostgreSQL. It aims to **manage the different bowling parks** in the app and **get informations from alleys's QR codes** or by manager's ids.

- Catalog Microservice
    

Git : [https://github.com/ArchitectureProject/CatalogMicroservice](https://github.com/ArchitectureProject/CatalogMicroservice)

The catalog microservice was made using SpringBoot and PostgreSQL. It aims to **manage the catalogs and the differents products of each BowlingPark**. The catalogs are the lists of products with their price that each BowlingPark offers to it's customers.

- Session Microservice
    

Git : [https://github.com/ArchitectureProject/SessionMicroservice](https://github.com/ArchitectureProject/SessionMicroservice)

The session microservice was made using C# and PostgreSQL. It aims to **manage the sessions in each bowling park alley**. A session is a "tab" at a given time in a given bowling alley of a given bowling park. This is **where the orders and the total amount of the bill are linked to the QRCode.** It also keeps track of a potential occuring payment for this session, **ensuring that no others payments are launched while another one is processing**. From it you can **order**, **pay in three different manners**, get the **currents session's information** and **close the current session** if everything have been paid. It also **notify every users in the session when a customer pay any amount** using web sockets.

> Websockets are done using Microsoft's technology : SignalR in ASP .NET. This powerful library can adapt to the client's capabilities by using different real-time transports, such as Server Sent Events, Forever Frame, Long polling or Websockets mentioned above. 
  

- Order Microservice
    

Git : [https://github.com/ArchitectureProject/OrdersMicroservice](https://github.com/ArchitectureProject/OrdersMicroservice)

The order microservice was made using SpringBoot and PostgreSQL. It aims to **manage the orders of the customers**. A order is a list of products with quantities and their respective prices. Every order is linked to the customer that passed it. **An agent can check the list of it's bowling park orders**.

- Mail Microservice
    

Git : [https://github.com/ArchitectureProject/EmailSenderMicroservice](https://github.com/ArchitectureProject/EmailSenderMicroservice)

The mail microservice was made using SpringBoot (Thymeleaf) and gmail's service. **It sends email when a payment has been processed**, informing the customer if the payment have been successful or not and for which amount.

- Payment Microservice
    

Git : [https://github.com/ArchitectureProject/PaymentMicroservice](https://github.com/ArchitectureProject/PaymentMicroservice)

The payment microservice was made using SpringBoot and PostgreSQL. It k**eeps track of every payment attempt** (succesfull or not) and **send messages to a MOM queue when credit card payment needs to be processed** by an asynchronous payment service.

- Payment Service Mock
    

Git : [https://github.com/ArchitectureProject/MockPaymentService](https://github.com/ArchitectureProject/MockPaymentService)

This is not really a microservice, this is a mock of a payment processing service. It **reads messages from a queue containing waiting Credit Cards payments**. **It then have 1 chance out of 5 to reject the credit card payment and then informs the Payment Microservice using another queue**.

# Installation

Needed : Docker  
Following ports will be used : 8080, 8081, 8082, 8083, 8084, 8000, 8001, 5432, 5433, 5434, 5435, 5436, 5437, 5672, 15672

To install and launch the application locally please clone the following git repository on your machine : [https://github.com/ArchitectureProject/environnement](https://github.com/ArchitectureProject/environnement).  
Then go to the main folder of the just cloned project and execute the following command :

`docker-compose up -d`.

It might take a while as you need to download images from docker hub and then execute all of them, it might be ready when everything is eather "Healthy", "Running" or "Started" (except for the subnetwork that will only be "Created").  
Then our app should locally be up to go ! You are free to use our docker collection to interact with it. However please follow the following instructions to set up the data.

# Data Setup

_/!\\ Please be sure that you are using the Architecture Project environment ! If not please verify if you have imported it from the project files we provided you._

**First we need to create both an Agent and a Customer.**  
You can use those requests :

[Create Agent](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-6758182e-7256-411e-b8d1-440c04a14b8e)

_/!\\ For the customer, please use the email you want the system to send mail notifications to !_

[Create Customer](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-3fa4e4c1-932b-4f62-9e53-410b69f5ff1f)

**Then you need to log them using the login requests with the correct credentials** (the one used to create them).  
_/!\\ You will receive a token for each login, please change the token values accordingly in the postman environment !_

[Login agent](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-79027acd-07e7-442e-a202-c62ff70f190d)

[Login customer](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-56972cc7-0899-4c56-ad42-966d17dcdfaa)

**Now you can create a new BowlingPark using the following request**, please use the id of the agent user you created in the "managerId" field. You can retrieve it with the [Get all users request.](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-0335826a-3878-4b52-a240-9755bd6160c6)

[Create bowling park](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-b48079cf-d9ae-475b-aab9-b706e559446b)

**Once the bowling park is created, you must create its catalog**, please use the bowlingPark id of the on eyou just in the last part of the URL of the request.

[Create catalog](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-0be5504c-c8bd-4be5-9a7b-e5660b504184)

**You then can add new products to the catalog** by using either one of those requests :  
Using the catalog id :

[Create new products in catalog](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-a6011227-c8b6-4738-a9c6-89617b067e6d)

Using the bowling id :

[Create a new product in the catalog using bowling id](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-34731458-89cb-474f-bc3d-5b50250bab53)

You can now use the basic operations of our application, please be sure that you are using the correct toekns and ids throughout your whole test.

# Basic operations

Here is a quick non-exhaustive exploration of the things you might want to test once the data setup is finished.

_**/!\\ Please keep in mind that there is a chance out of 5 for a credit card payment to be decline at random. If the payed amount is not updated and you don't know why, check in the payment attempt database the state of the payment.**_

[Get all payment attempts](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-37ec0f11-f6f9-4a8d-9563-900533b0bc0b)

_**/!\\ An email is sent at every payment, indicating if it has been refused or not**_

## Create or get a session from a QR Code

Please use a QR Code from an existing bowling park alley, you can get all Bowling Park to find one.  
This request either returns you an existing session if one is currently going at this specific QR code's alley or create a new session at this QR code's alley.

[Create or get session](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-1f70cfdf-82c1-4a13-8956-a5cdab8bf680)

## Pass an order

Please use existing products and an existing session's QR code.

[Pass an order](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-634de14b-f51a-4b3c-982d-39584784047d)

## Pay the totality of the bill

Please use an existing session's QR code. Credit card infos are for the sake of reality but can be replaced by any string.  
[Attempt Payment Total](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-9971aede-3d8a-4927-bd9b-9beee92dd909)

## Make a customer pay for its orders

Please use an existing session's QR code. Credit card infos are for the sake of reality but can be replaced by any string.

[Attempt Payment customer orders](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-36550e4f-093d-4637-ba99-f7d04cd5e4fe)

## Make a customer pay a given amount (credit card)

Please use an existing session's QR code. Credit card infos are for the sake of reality but can be replaced by any string. Do not forget to modify the amount.

[](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-36550e4f-093d-4637-ba99-f7d04cd5e4fe)[Attempt payment amount (credit card)](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-36c3d6b6-1da9-4505-9334-40a1d8609754)

## Make a customer pay a given amount (cash)

Please use an existing session's QR code. Do not forget to modify the amount.

[Attempt payment amount (cash)](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-a498eff5-18d8-4dfc-8c0d-ca23f076e78f)

## Close a session

Please use an existing session's QR code. A session won't be able to be closed if the totality of the bill haven't been paid for.

[Close session](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-572555c5-e2c6-4444-8389-cf0e98df6048)

## List the products of a catalog

Please use an existing QR code of a bowling with a catalog.

[Get Catalog By QrCode](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-9ec40268-ef5d-49f0-bf67-c6d67f292903)

## For a manager : see the orders for its park only

Please use the token of a manager of an existing bowling park (a bowling park having order preferably).

[Get Order By Bowling Park Manager](https://go.postman.co/workspace/3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/documentation/10636365-53830453-a9c1-4ecb-abd8-3b5ce86f517d?entity=request-44e3736c-a1ae-46ec-b060-a23e751e2f01)

# Payment concurrency

Payment concurrency is managed in the session microservice via a flag (ActuallyProcessingPayment) which is updated at the start of a payment called by the customer and is released again after the end of the payment microservice validation process (whether it fails or not).

# About web socket notifications

You can test notifications and web sockets in the other postman collection of this workspace (called WebSockets) as HTTP requests and Web Sockets connections can't be in the same collection.

[WebSockets collection](https://blue-spaceship-107508.postman.co/workspace/Architecture-Project~3c356e23-0f1e-42ba-8a57-ebc67fcf1d4e/collection/65eca98e576521afcbda8a51?action=share&creator=10636365&active-environment=10636365-fcaf7d07-7ccf-43ea-a928-0d330fa24219)

> In the postman example, two messages are sent to the server in order to receive notifications corresponding to the correct session, the first to open the connection to the server and the second to specify which qrCode (session) we wish to connect to. 
  

This is an example of using the notification server part of the session microservice.

Connecting to the server and listening to the notifications sent by the latter can be done via WebSocket directly, but Microsoft recommends using the **SignalR** library directly in the corresponding language, as it allows you to adapt the transport layer directly to the capabilities of the client/server (if WebSocket is not available, for example).

# With minikube for scalability

The following steps will let you launch our app in minikube to ensure scalability.  
Needed : Docker, Minikube, Kompose

Start minikube :

`minikube start`

Enable ingress :

`minikube addons enable ingress`

Navigate to the folder where the docker-compose file of our project is (from the environment repository) and transform it into kubernetes manifests :

`kompose convert`

Then apply the kubernetes manifests :

`kubectl apply -f .`

Then set up ingress with the correct ip, you can get the needed ip with the following command :

`minikube ip`

Then deploy a Horizontal Pod Scaler (HPA) by enabling it :  
`minikube addons enable metrics-server`

And creating it :

`kubectl autoscale deployment --cpu-percent=50 --min=2 --max=10`

To get the deployment name please use th following command and identify the deployment relative to our app :

`kubectl get deployments`
