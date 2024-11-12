
ISSUE=defect-6683-map-read-write-crash-simple
THEORY=theory1

CADDY_VERSION=v2.8.4
TLS_CONSUL_VERSION=v1.5.0
CACHE_HANDLER_VERSION=v0.14.0

RACE_DETECTOR ?= 0
RACE_FLAG=$(if $(filter 1,$(RACE_DETECTOR)),-race,)
RACE_FILENAME=$(if $(filter 1,$(RACE_DETECTOR)),-race,)
BIN_FILENAME_WITH_VERSIONS_AND_DATE=LRTcaddy-$(CADDY_VERSION)-$(TLS_CONSUL_VERSION)-$(CACHE_HANDLER_VERSION)$(RACE_FILENAME)-$(DATE)
BUILD_DIR=build_tmp

PLATFORM=mac
ifeq ($(shell uname -m),x86_64)
	PLATFORM=amd64
endif	

CADDYBIN=bin/$(BIN_FILENAME_WITH_VERSIONS_AND_DATE)-$(PLATFORM)

LOG_DIR=log/$(ISSUE)/$(THEORY)-$(PLATFORM)

all: clean info 
	@echo "run caddy$(THEORY) in background"
	make run &
	sleep 3	 
	ps aux | grep "$(CADDYBIN)"
	@echo "run tests after 10sec startup delay"
	@echo "waiting..."
	sleep 10 
	@echo "running tests..."
	make tests 
	ps aux | grep "$(CADDYBIN)"
	make kill-caddy

.PHONY: zip
zip:
	7z a $(ISSUE)-$(THEORY).zip .


.PHONY: kill-caddy
kill-caddy:
	pkill -f "$(CADDYBIN)"

run: caddy$(THEORY) 

.PHONY: clean
clean: 
	rm -rf ./log/

info:
	@echo "make caddy$(THEORY)"
	@echo "On    PLATFORM=$(PLATFORM)"
	@echo "Using CADDYBIN=$(CADDYBIN)"
	@echo "Building log/$(LOG_DIR)"
	mkdir -p ./$(LOG_DIR)
	@echo "Logging caddy version"
	$(CADDYBIN) version > ./$(LOG_DIR)/caddy-version-$(ISSUE).log
	@echo "Logging caddy list-modules"
	$(CADDYBIN) list-modules > ./$(LOG_DIR)/caddy-list-modules-$(ISSUE).log
	@echo "Logging caddy build-info"
	$(CADDYBIN) build-info > ./$(LOG_DIR)/caddy-build-info-$(ISSUE).log


build: build-mac

## for local testing only		
.PHONY: build-mac
build-mac:
	GOOS=darwin GOARCH=arm64 xcaddy build $(CADDY_VERSION) \
		--output bin/$(BIN_FILENAME_WITH_VERSIONS_AND_DATE)-mac \
		--with github.com/christophcemper/caddy-netlify-redirects@v0.2.4 \
		--with github.com/caddyserver/transform-encoder \
		--with github.com/pteich/caddy-tlsconsul@$(TLS_CONSUL_VERSION) \
		--with github.com/caddyserver/cache-handler@$(CACHE_HANDLER_VERSION)


.PHONY: caddy$(THEORY)
caddy$(THEORY):
	$(CADDYBIN) \
	run --config ./CaddyfileDEV-$(ISSUE)-$(THEORY) \
	--watch 2>&1 | tee ./$(LOG_DIR)/caddyDEV-stdouterr-$(ISSUE)-$(THEORY)-$(PLATFORM).log


tests: caddy$(THEORY)-case1 caddy$(THEORY)-case2 caddy$(THEORY)-case3

.PHONY: caddy$(THEORY)-case1
caddy$(THEORY)-case1:
	curl -X POST -I -v https://localhost/caching-reverse-proxy/8a4899c7-03f5-4dca-86f2-3249e5c33ca2?cache=false | tee ./$(LOG_DIR)/caddy$(THEORY)-case1-$(PLATFORM).log

.PHONY: caddy$(THEORY)-case2
caddy$(THEORY)-case2:
	curl -X POST -I -v https://localhost/caching-reverse-proxy/8a4899c7-03f5-4dca-86f2-3249e5c33ca2?cache=true | tee ./$(LOG_DIR)/caddy$(THEORY)-case2-$(PLATFORM).log

.PHONY: caddy$(THEORY)-case3
caddy$(THEORY)-case3:
	curl -X POST -I -v https://localhost/caching-reverse-proxy/8a4899c7-03f5-4dca-86f2-3249e5c33ca2?cache=true | tee ./$(LOG_DIR)/caddy$(THEORY)-case3-$(PLATFORM).log

