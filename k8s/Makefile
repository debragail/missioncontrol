VERSION=$(shell ./getVersion.js)
MINIKUBE_IP=$(shell minikube ip)

FORCE:

build: TIMESTAMP=$(shell date +%y%m%d-%H%M -u)
build: FORCE
	@echo Building with timestamp $(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.api -t davnetwork/dav-api:$(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.cassandra -t davnetwork/dav-cassandra:$(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.kafka -t davnetwork/dav-kafka:$(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.kafka-init -t davnetwork/dav-kafka-init:$(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.nginx -t davnetwork/dav-nginx:$(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.zookeeper -t davnetwork/dav-zookeeper:$(TIMESTAMP)
	docker build ../. -f ../dockers/Dockerfile.zookeeper-init -t davnetwork/dav-zookeeper-init:$(TIMESTAMP)

	docker push davnetwork/dav-api:$(TIMESTAMP)
	docker push davnetwork/dav-cassandra:$(TIMESTAMP)
	docker push davnetwork/dav-kafka:$(TIMESTAMP)
	docker push davnetwork/dav-kafka-init:$(TIMESTAMP)
	docker push davnetwork/dav-nginx:$(TIMESTAMP)
	docker push davnetwork/dav-zookeeper:$(TIMESTAMP)
	docker push davnetwork/dav-zookeeper-init:$(TIMESTAMP)

deploy-zookeeper-dev: FORCE
	cd zookeeper && ks env set dev --server=https://$(MINIKUBE_IP):8443 && ks apply dev --ext-str IMAGE_VERSION=$(VERSION)

deploy-zookeeper-prod: FORCE
	cd zookeeper && ks apply prod --ext-str IMAGE_VERSION=$(VERSION)

remove-zookeeper-dev: FORCE
	cd zookeeper && ks env set dev --server=https://$(MINIKUBE_IP):8443 && ks delete dev --ext-str IMAGE_VERSION=latest

remove-zookeeper-prod: FORCE
	cd zookeeper && ks delete prod --ext-str IMAGE_VERSION=latest

deploy-davnn-dev: FORCE
	@echo "Deploying version $(VERSION)"
	cd davnn && ks env set dev --server=https://$(MINIKUBE_IP):8443 && ks apply dev --ext-str IMAGE_VERSION=$(VERSION)

deploy-davnn-prod: FORCE
	@echo "Deploying version $(VERSION)"
	cd davnn && ks apply prod --ext-str IMAGE_VERSION=$(VERSION)

remove-davnn-dev: FORCE
	cd davnn && ks env set dev --server=https://$(MINIKUBE_IP):8443 && ks delete dev --ext-str IMAGE_VERSION=latest

remove-davnn-prod: FORCE
	cd davnn && ks delete prod --ext-str IMAGE_VERSION=latest

start-proxy: FORCE
	kubectl port-forward --namespace=davnn deployment/davnn 8080:80 9092:9092 7000:7000

start-proxy-zk: FORCE
	kubectl port-forward --namespace=davnn service/zookeeper 2181:2181
