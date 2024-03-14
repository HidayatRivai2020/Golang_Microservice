# Broker Service with Golang Microservice
- How to
	- Add folder into workspaces
	- building docker image for the Service
		- Multi-stage build using certified go docker image
- Front End Service
- Broker Service
	- Read json
	- Write json
	- Error json
- Create Make Up File


## Requirement
- go get github.com/go-chi/chi/v5
- go get github.com/go-chi/chi/cors

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
5. Create `docker-compose.yml` in project folder and write
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
	- use `http.ListenAndServce` to listend and servce the URL that user requested
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
3. Creating Routing
	- in `<api>` folder, create a new file called routes.go
	- import package `main
	- add function `<routes>` with return `<http.handler>`
	- use `receiver method` in the `<routes>` (i.e : `<app \*Config>`)
4. in `<routes.go>`, use `<chi new router>` variable
	- go to in `<routes>` method
	- create a new variable with the value of `chi.NewRouter`
	- specify who is allowed to connect 
		- use `Use` function
		- add `cors.Handler` function in `Use` as parameter
		- add `corsOptions` function in `cors.Handler` as parameter
		- in `corsOptions` :
			- add `AllowedOrigins` : connection origins
			- add `AllowedMethods` : connection method
			- add `AllowedHeaders` : connection headers
			- add `ExposedHeaders` : Exposion headers
			- add `AllowCredentioal` : connection credential
	- use `Use` function and add `middleware.HeartBeat` as parameter to ping connection
	- return `NewRouter` variable
5. in `<main.go>`, run the http `server`
	- import `net/http` package
	- create `constant` variable `<webPort>` 80
	- create `struct` `<Config>`
	- create new variable from `<Config>` (i.e : `<app>`)
	- define `http server` using `http.Server` and store it in variable (i.e : `<srv>`)
	- start the server using  `<srv>.ListenAndServe>`
	- add error handling
	- in `http server`, add `Addr` with `String` and `Handler` with `app.Routes()`
6. Create `<handlers>`
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
7. Add implementation in `<Broker>`
	- create `<jsonResponse>` and store it in variable (i.e : payload)
	- use `json.MarshalIndent` to create output in the format of `json` with indent
	- on the `ResponseWriter`
		- set Header using `Header.Set`
		- write header using `writeHeader`
		- write into response using `write`
8. Add routes
	- use `Use` function 
	- add `<"/">`and `<app.Broker>` as parameter
9. Run the broker Service
	- in terminal go run `<./cmd/api>`
	
## Create helper functions to deal with json
1. In `<cmd/api>` folder create `<helpers.go>`
2. Move `<jsonResponse> struct` from `<handler.go>` into `<helper.go>`
3. Remove all write Json logic from another service and use `app.writeJSON` instead

### Read Json
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

### Write Json
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

### Error json
1. Create `receiver function (<app *Config>)` to return error json (i.e : `<errorJSON>`)
2. Add some parameter :
	- `<w>` as `http.ResponseWriter`
	- `<err>` as `error`
	- `<status>` as `...any`
3. set statusCode with default value `http.StatusBadRequest`
4. set the `<payload>` jsonResponse
9. return the jsonResponse using `<app>.writeJSON`

## Create Make Up File
1. Create `MakeFile` in `<project>` folder
2. Implement logic to build up or teardown docker
3. Remove the duplicate docker build up
4. Run make using `make <command>`