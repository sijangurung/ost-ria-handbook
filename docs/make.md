# Make

We use `make` to drive our build workflow. Make allows to abstract the build
workflow from our underlying technology (node, python, java, etc) and provides
a unified developer experience. This is configured using a `Makefile`.

The `Makefile` serves as a contract between development and CI/CD and should
include the following targets.

* `build`
* `configure`
* `lint`
* `test`

Add these targets on an as needed basis.

* `integration-test`
* `doc`
* `docker`

## Skeleton

```make
IMAGE = my-docker-image
TAG = latest

# build will execute when running just make
build: clean configure lint test
	echo "Build the software artifact"

clean:
	echo "Deleting dynamically created files"

configure:
	echo "Configure the local environment, download dependencies etc"

lint:
	echo "Verify code quality"

test:
	echo "Run unit tests and code coverage"

integration-test:
	echo "Run integration tests"

doc:
	echo "Build documentation artifact"

docker:
	echo "Building Docker image"
	docker build -t $(IMAGE):$(TAG) -f Dockerfile .
	echo "$(IMAGE):$(TAG)"

.PHONY: build clean configure lint test integration-test doc docker
```

* [GNU `make`][gnu-make] Documentation

## Editorconfig rules

```ini
[Makefile]
indent_style = tab
```

[gnu-make]: https://www.gnu.org/software/make/manual/make.html
