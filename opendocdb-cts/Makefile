all: test build run lint

init:
	go mod tidy && go mod verify
	cd tools && go mod tidy && go mod verify && go generate

fmt:
	../bin/goimports -local=github.com/OpenDocDB/cts/opendocdb-cts -w .
	../bin/gofumpt -w .

test: fmt
	go test -count=1 ./...

build: fmt
	go run main.go --help

run-fmt:
	go run main.go --dir=../cts fmt
	go run main.go --dir=../cts convert ../cts-mongosh

run-mongodb:
	go run main.go --dir=../cts run --uri=mongodb://127.0.0.1:27001/qklck6njgh9o --golden
	go run main.go --dir=../cts run --uri=mongodb://127.0.0.1:27001/cts

run-ferretdb:
	go run main.go --dir=../cts run --uri=mongodb://127.0.0.1:27002/cts

lint: fmt
	../bin/go-consistent -pedantic ./internal/...
	../bin/golangci-lint run
	../bin/govulncheck -test -show=verbose,color ./...

env-up-detach:
	mkdir -p ../tmp/testscripts
	docker compose up --always-recreate-deps --build --force-recreate --pull=always \
		--remove-orphans --renew-anon-volumes --timeout=0 \
		--detach

env-logs:
	docker compose logs --follow

env-up: env-up-detach env-logs

env-down:
	docker compose down --remove-orphans -t 0 --volumes

docker-init:
	-docker buildx create --name=opendocdb-cts --driver=docker-container --bootstrap=true --use=false

docker-local:
	docker buildx build --builder=opendocdb-cts --tag=opendocdb-cts --load ..
