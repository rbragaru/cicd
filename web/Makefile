appname = frontend-app
version = 1.0
account = $(shell aws sts get-caller-identity --query 'Account' --output text)


stage = dev
include configs/$(stage)


build:
	docker build -t $(appname):$(version) .

login:
	aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $(account).dkr.ecr.us-east-1.amazonaws.com

push: login 
	docker tag $(appname):$(version) $(account).dkr.ecr.us-east-1.amazonaws.com/$(appname):$(version)
	docker push $(account).dkr.ecr.us-east-1.amazonaws.com/$(appname):$(version)

deploy:
	helm upgrade --install $(appname) k8s-chart/ --namespace=$(namespace) --create-namespace \
		--set applicationName=$(appname) \
		--set replicaCount=$(replicas) \
		--set image.repository=$(account).dkr.ecr.us-east-1.amazonaws.com/$(appname) \
		--set image.tag=$(version) \
		--set port=8080
