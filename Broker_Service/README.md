# Broker Service with Golang Microservice
- Requirement
- How to
	- Add folder into workspaces
	- building docker image for the Service
		- Multi-stage build using certified go docker image
	- Create MakeFile
	- Create routing
	- Define And Start Server
	- Create helper functions to deal with json
		- Read json
		- Write json
		- Error json
- Front End Service
- Broker Service
	- Update broker for a Standard JSON format (After Creating Authentication service)
- Authentication service
	- Update front-end to Implement Auth Services

## Requirement
- go get github.com/go-chi/chi/v5
- go get github.com/go-chi/chi/cors
- go get github.com/jackc/pgconn
- go get github.com/jackc/pgx/v4
- go get github.com/jackc/pgx/v4/stdlib
- go get go.mongodb.org/mongo-driver/mongo
- go get github.com/vanng822/go-premailer/premailer
- go get github.com/xhit/go-simple-mail/v2
- go get github.com/rabbitmq/amqp091-go
- go get google.golang.org/grpc
- go get google.golang.org/protobuf
- go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.27
- go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2

## How to

### Add folder into workspace
- create folder `<name>`
- in vs code click `file` then click `add folder into workspace`
- select folder
- in terminal : `go mod init <name>`
	
### Building Docker image for the service

#### Multi-Stage build using a certified go docker image
1. Add `<project>` folder into workspace
2. Add `dockerfile` in service 
	- `<service>.dockerfile`
3. Add base `go image` in `dockerfile`
	- add base go image
		- `FROM golang:1.18-alphine as builder`
	- make folder in `docker`
		- `RUN mkdir /app`
	- copy everything in current folder into created folder
		- `COPY . /app`
	- set working directory
		- `WORKDIR /app`
	- build go code
		- `RUN CGO_ENABLED=0 go bild -o <service>App ./cmd/api`
	- make the folder executable
		- `RUN chmod +x /app/<service>App`
4. Build tiny `docker image` in `dockerfile`
	- add latest alphine
		- `FROM alphine:latest`
	- make folder in docker
		- `RUN mkdir /app`
	- Copy from the first docker into this Docker
		- `COPY --from=builder /app/<service>App /app`
	- Add command to run
		- `CMD["app/<service>App"]`
5. Create `docker-compose.yml` in project folder and add services
	- `version`
	- `services -> build -> context`
	- `services -> build -> dockerfile`
	- `services -> restart -> dockerfile`
	- `services -> ports -> dockerfile`
	- `services -> deploy -> mode`
	- `services -> deploy -> replicas`
6. Run command to execute `docker-compose.yml`
	- `docker-compose up -d`
7. Add button and implementation
	- add button using html
	- use `javascript` and `DOM`
	- add a `constant variable` with the `method`
	- `fetch` the `url` with the `method` and implement algorithm using `DOM`
8. Create MakeFile	

### Create Make File
1. Create `MakeFile` in `<project>` folder
2. Implement logic to build up or teardown docker
3. Remove the duplicate docker build up
4. Run make using `make <command>`
	
### Create routing
1. in `<api>` folder, create a new file called routes.go
2. import package `main
3. add function `<routes>` with return `<http.handler>`
4. use `receiver method` in the `<routes>` (i.e : `<app \*Config>`)
5. `<routes.go>`, use `<chi new router>` variable
6. go to in `<routes>` method
7. create a new variable with the value of `chi.NewRouter`
8. specify who is allowed to connect 
	- use `Use` function
	- add `cors.Handler` function in `Use` as parameter
	- add `corsOptions` function in `cors.Handler` as parameter
	- in `corsOptions` :
		- add `AllowedOrigins` : connection origins
		- add `AllowedMethods` : connection method
		- add `AllowedHeaders` : connection headers
		- add `ExposedHeaders` : Exposion headers
		- add `AllowCredentioal` : connection credential
9. use `Use` function and add `middleware.HeartBeat` as parameter to ping connection
10. return `NewRouter` variable

### Define And Start Server
1. in `<main.go>`, run the http `server`
2. import `net/http` package
3. create `constant` variable `<webPort>` 80
4. create `struct` `<Config>`
5. create new variable from `<Config>` (i.e : `<app>`)
6. define `http server` using `http.Server` and store it in variable (i.e : `<srv>`)
7. start the server using  `<srv>.ListenAndServe>`
8. add error handling
9.in `http server`, add `Addr` with `String` and `Handler` with `app.Routes()`

	
### Create helper functions to deal with json
1. In `<cmd/api>` folder create `<helpers.go>`
2. Move `<jsonResponse> struct` from `<handler.go>` into `<helper.go>`
3. Remove all Json logic from another service and use `<helpers.go>` instead

#### Read Json
1. Create `receiver function (<app *Config>)` to read json (i.e : `<readJSON>`)
2. Add some parameter :
	- `<w>` as `http.ResponseWriter`
	- `<r>` as `http.Request`
	- `<data>` as `any`
3. Add limitation of Json File size using `http.MaxBytesReader`
4. Add `json.NewDecoder`
5. Decode the data using `<decoder>.Decode`
6. Add error handling using return `<error>`
7. `Decode` the `&struct{}{}`
8. Add error handling return `<error>.New`
9. return `nil`

#### Write Json
1. Create `receiver function (<app *Config>)` to write json (i.e : `<writeJSON>`)
2. Add some parameter :
	- `<w>` as `http.ResponseWriter`
	- `<status>` as `int`
	- `<data>` as `any`
	- `<headers>` as `...httpHeader`
3. Use `json.Marshal` to return json encoding
4. Add error handling using return `<error>`
5. Get the value of all `headers`
6. Set the header using `Header.set`
7. write header using `WriteHeader`
8. Add error handling using return `<error>`
9. return `nil`

#### Error json
1. Create `receiver function (<app *Config>)` to return error json (i.e : `<errorJSON>`)
2. Add some parameter :
	- `<w>` as `http.ResponseWriter`
	- `<err>` as `error`
	- `<status>` as `...any`
3. set statusCode with default value `http.StatusBadRequest`
4. set the `<payload>` jsonResponse
9. return the jsonResponse using `<app>.writeJSON`

	
## Front End Service
1. Add `<front-end>`folder into workspace
2. create `<main.Go>`File (main file for the front-end service)
	- create a folder for the microservice (i.e : `<front-end>`)
	- create a folder an create a new file called main.go (i.e : `<front-end/cmd/web/main.go>`)
	- Create empty main function
3. create render function (function to render a page)
	- import `html\template`
	- create a new function in called `<render>`
	- create an new `slice string` to store all the path (i.e : `<templateSlice>`)
	- add 2 parameters : `<resp>` as `http.ResponseWriter` and `<page>` as `String`
	- create a slice string for the `<partial file>` path (i.e : `<base, header, footer>`)
	- append the `<partial file>` and  `<page>`into `<templateSlice>`
	- use `template.ParseFiles` to the `<templateSlice>`
	- add an error handling
	- execute the parsed `template`
	- add an error handling
4. Add a `<handler>` to execute `<render>` function when user access a specific `URL`
	- import the `http` package
	- in main main function, use `http.HandleFunc` to run `<render>` function when user access specific URL
	- use `http.ListenAndServe` to listen and serve the URL that user requested
	- add an error handling
5. Create the template File
	- create the partial files
		- `<base.layout.gohtml>` : the base layout for the template
		- `<header.partial.gohtml>` : html header
		- `<footer.partial.gohtml>` : copyright
	- create the page files
		- `<test.page.gohtml>` : test page file
			- Title
			- Sent 
			- Received
	- use `.gohtml` extention instead of `html`
	
## Broker Service
1. Add `<Broker>` folder into workspace
2. Creating API file
	- create folder (i.e : `<cmd/api>`)
	- create new file `<main.go>`
	- import package `main`
	- add function `main()`
3. Create Routing
4. Define and start server
5. Create `<handlers>`
	- createa a new file `<handlers.go>`
	- import `net/http`
	- import `encoding/json`
	- create function `<Broker>` with parameter `<resp>` as `http.ResponseWriter` and `<req>` as `http.Request`
	- use receiver method in the `<Broker>` (i.e : `<app \*Config>`)
	- create new `struct` (i.e `<jsonResponse>`)
	- add parameter in `<jsonResponse>`
		- `<Error>` as `bool`
		- `<Message>` as `string`
		- `<Data>` as `any`
6. Add implementation in `<Broker>`
	- create `<jsonResponse>` and store it in variable (i.e : payload)
	- use `json.MarshalIndent` to create output in the format of `json` with indent
	- on the `ResponseWriter`
		- set Header using `Header.Set`
		- write header using `writeHeader`
		- write into response using `write`
7. Add routes
	- use `Use` function 
	- add `<"/">`and `<app.Broker>` as parameter
8. Run the broker Service
	- in terminal go run `<./cmd/api>`

### Update broker for a Standard JSON format (After Creating Authentication service)
1. Add routes
2. Create `<HandleSubmission>` function in handlers.go
3. Add Authentication service
	- Create json that will be sent into auth Microservice
	- call the service
	- verify if the response status code is correct
	- read response body and store it in variable
	- decode the json from auth service
	- write JSON response

## Authentication Service
1. Create `<authentication>` folder and add it into workspace
2. Init the service : `go mod init authentication`
3. Create folder `<cmd/api>` in `<authentication>` folder
4. Create `main.go` with package `main` and function `main`
5. Create `data` folder and add `models.go`
6. Create routing 
7. Add properties `DB` and `Models` ini `<Config>`
7. Define and start server
8. Import connection server : `pgconn, pgx, stdlib`
9. Create Open Database to Postgress
	- create a new function in main.go (i.e `<openDB>`) and return `*sql.db` and error
	- open connection using sql.open
	- add erorr handling
	- ping the connection using `<db>.Ping`
	- add erorr handling
	- return `db, nil`
10. Create Connect to Database
	- create a new function in main.go (i.e `<connectToDB>`) and return `*sql.db`
	- get environtment with `os.Getenv("DSN")` and store it in variable
	- use function `<openDB>`
	- return the `<connection>` if there is no Error
	- sleep a few second and continue the loop if no connection
	- return error after few attempts
11. Call `<connectToDB>` in `main` function
12. Add error handler
13. set `DB` and `Models` with the `<connection>`
14. Add `postrgres` image into `docker-compose.yml`
15. Add `Authentication` service into `docker-compose.yml`
16. Set the Database
17. Create helper
18. Update `<routes.go>`
19. Create `<handlers.go>` to read JSON, get user by email, and check if the password matches

### Update front-end to Implement Auth Services
1. Add button and implementation
	- add button using html
	- use `javascript` and `DOM`
	- add a `constant variable` with the `method`
	- `fetch` the `url` with the `method` and implement algorithm using `DOM`
2. make docker with `make up_build`
3. Make front End with `make start`


## Logger Service
1. Create `<logger>` folder and add it into workspace
2. Init the service : `go mod init log-service`
3. Create folder `<cmd/api>` in `<logger>` folder
4. Create `main.go` with package `main` and function `main`
5. Create mongo Constant
6. create mongo `Client`
7. Create Config
8. Connect to mongo
9. Create `<connectToMongo>` function
	- Create connection options using `ApplyURI`
	- Set Authenticationus using `SetAuth`
10. Connect to mongo using `Connect`
11. In `Main.go`, use `context.WithTimeOut` to disconnect
12. Close connection using `Disconnect`
13. Create `data` folder and add `models.go`
14. Add properties `Mongo` and `Models` in `<Config>`
15. Create routing 
16. Create `handlers.go`
	- WriteLog : to write the logger
17. Create `helpers.go` 
18. Define and start server
19. Add `mongo` image into `docker-compose.yml`
20. Add `logger` service into `docker-compose.yml`
21. Update Make File
22. Update Broker Service to use Logger
	- update `<RequestPayload>` and `<LogPayload>`
	- add new action "log"
	- add function `<logItem>`
	- create new POST requested
	- set header
	- get client response
	- add error handling
	- return payload
23. Update the front end to post the logger via broker
	- add button
	- add javarscript implementation using DOM`
	- add click event listener to the button
	- add payload
	- add headers
	- add body
	- fetch the data
24. Update the authentication to use basic logging
	- create new function `<logRequest>` in `<handlers.go>`
	- create new `struct <entry>`
	- create `new request`
	- add error handling
	- add `client request`
	- add error handling
	- `return nil`
	- call `<logRequest>` in `main.go`
	
## Mail Service
1. Add `mail` image into `docker-compose.yml`
2. Create `<mail-service>` folder and add it into workspace
3. Init the service : `go mod init log-service`
4. Create folder `<cmd/api>` in `<mail-service>` folder
5. Create `main.go` with package `main` and function `main`
5. Create `helpers.go`
6. Create `Routes.go`
7. Define and start server
8. Create `mailer`
9. Create `template`
10. Add `<mail-service>` into `<docker-compose.yml>` and 
11. Update Broker service to handle `mail`
	- add `mail` payload
	- add `mail` submission
	- Create `<sendMail>` function 
		- call mail Service
		- post to mail Service
		- set the header
		- `Do` the requested
		- get the `statusCode`
		- send back response

### Mailer
1. Create `<mailer.go>`
2. Create `<mail>` struct
3. Create `<Message>` struct
4. create `<SendSMTPMessage>` function
5. Create `<buildHTMLMessage>` function
6. Create `<inlineCss>` function
7. Create `<buildPlainTextMessage>`
8. Create new email with `NewMsg` function
9. Set the value for the email
10. Send the email using `Send`

### Template
1. Create `<templates>` folder
2. Create `<mail.html.gohtml>`
3. Create `<mail.plain.gohtml>`
4. Update `<routes.go>`
5. Create `<Handler>`
	- Create `<SendMail>` function
	- Create `<CreteMail>` function
	
## listener
1. Create `<listener-service>` folder and add it into workspace
2. Init the service : `go mod init listener-service`
3. Create folder `<cmd/api>` in `<listener-service>` folder
4. Create `main.go` with package `main` and function `main`
5. Add `rabbitmq` into `<docker-compose.yml>`
5. Connect to `rabbitmq`
	- Create `connect` function
	- use `Dial` to connect with `rabbitmq` inside loop
	- create `error handling`
	- return `connection` if no problem
	- `defer close` the connection
6. Create `<event> package`
7. Create `<Consumer.go>
	- create struct `<Consumer>`
	- create `<NewConsumer>` function
	- get the setup
	- create `<Setup>` function
		- get the channel
		- return `DeclareExchange`
	- create `payload` struct
	- create `Listen` function
		- get the channel
		- defer close channel
		- get the `<DeclareRandomQueue>`
		- Bind the `topic queue`
		- add `error handling`
		- Consume from channel
		- create goroutine to handle payload
	- create `<handlePayload>`
	- create `<logEvent>`
8. Create `<Event.go>
	- create `<DeclareExchange>` function
		- return `ExhangeDeclare` 
	- create `<DeclareRandomQueue>` function
		- return `<QueueDeclare>` 
9. Start listening for messages
10. Create Consumer
11. Watch the queue and consume events
12. Create `rabbitmq` docker image and update the `makefile`
13. Update `Broker service` to interact with `rabbitmq`
14. Writing logic to emit events to `rabbitmq` in broker-service
	- Create `<emitter.go>`
	- Create `<setup>` function
		- Create `channel`
		- Defer `Close` channel
		- return `<DeclareExchange>`
	- Create `<push>` function
		- create channel
		- Defer `Close` channel
		- Publish channel
	- Create `NewEventEmitter`
		- create emitter `setup`
		- return emitter
15. Adding log in the broker-service via `rabbitmq`
	- update `<handler.go>`
	- add `<logEventViaRabbit>` function
		- add `pushToQueue`
		- add error handling
		- return `response`
	- add `<pushToQueue>` function
		- create `NewEventEmitter`
		- set `log payload`
		- return json
		- push emitter
	- Change action to use `<logEventViaRabbit>`
		
## Add RPC
- In `cmd/api` add `rpc.go`
- Create `RPC struct`
- Create `RPC payload`
- Create `logInfo` function
- Create log into `mongodb`
- In `main.go` create `rpcListen` function
	- Use function `listen`
	- defer close listen
	- `accept` the listener
	- `serve connection`
	- reguster the RPC server
- Call RPC from broker
	- Create `logItemViaRPC` function in `handlers.go`
	- dial rpc
	- call rpc
	- return response
	
## grpc in logger service
- Define a protocol
	- create a folder
	- create a `.proto` file
	- create `log`, `logResponse`, and `logRequest`
- Install protoc in cmd
- in `<logs>` folder set up the protoc
	- `protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative logs.proto`
- in `cmd/api` create grpc.go
	- create `<LogServer>` struct
	- create `<WriteLog>` Function
	- use `GetLogEntry`
	- write the log
	- insert
	- add error handling
	- return LogResponse
- Create GRPC listener
	- Create `gRpcListen` function
	- use `netListen`
	- add error handling
	- use `RegisterLogServiceServer` function
	- use `serve`
	- add error handling
- Create the Client Code
	- in `broker-service` create `logs` folder
	- create `logs.proto`
	- `protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative logs.proto` in logs folder
	- in `handlers.go` create `LogViaGRPC` function
	- use `readJSON`
	- add error handling
	- grpc `Dial`
	- `defer close` connection
	- Use `NewLogServiceClient(conn)`
	- `defer cancel` when timeout
	- write log
	- add error handling
	- use `writeJson`
	- add `routes`
- Update The Front End