# docker-repolinter

A Docker image that runs [Repolinter](https://github.com/todogroup/repolinter/) against a repository.

The basic linting attempts to be uncontroversial and only includes very basic rules. Details are provided below to build out more specific rulesets for an organisation or collection of projects.

## The Default Rules

The basic rules are that:

* a license file must be present;
* a `README` must be present;
* no binary files should be present, which includes:
  * Windows executables;
  * Windows DLLs;
  * and Node modules (see [Only git the important bits](https://devcenter.heroku.com/articles/node-best-practices#only-git-the-important-bits)).

## Running a Container

The working directory in the image is `/repolinter/repo`, which is where the host directory to be checked should be mapped to.

### Using Docker

To lint the 'current' repo (i.e., the current working directory) using Docker, run:

```shell
docker run --rm -it -v ${PWD}:/repolinter/repo markbirbeck/repolinter
```

### Using Docker Compose

To lint the 'current' repo using Docker Compose, set up a `docker-compose.yml` file that includes the following:

```yaml
version: "3.2"

services:
  repolinter:
    image: markbirbeck/repolinter
    volumes:
      - .:/repolinter/repo
```

and then run:

```shell
docker-compose run repolinter
```

## Overriding the Configuration

The configuration file is located in the directory above the repo to be checked (i.e., `/repolinter`), so that it doesn't interfere with whatever is being linted. If there is a `repolint.json` file in the current directory then `repolinter` will pick it up, but ideally the config should *not* be part of the project being checked--the rules that a repo must adhere to should be externally enforced.

The quickest way to achieve this is to create a config file based on [the default ruleset](https://github.com/todogroup/repolinter/blob/master/rulesets/default.json), and then to map it into a running container:

```shell
docker run --rm -it \
  -v ${PWD}:/repolinter/repo \
  -v /path/to/my/repolint.json:/repolinter/repolint.json \
  markbirbeck/repolinter
```

However, if you have a policy that must be enforced across a number of repos within your company or organisation then another model is to create a Docker image that includes the config. Your `Dockerfile` might look something like this:

```dockerfile
FROM markbirbeck/repolinter

LABEL maintainer="someone@somewhere.com"
LABEL version="0.1.0"

# Copy in custom configuration:
#
COPY repolint.json /repolinter/
```

You now have a Docker image that will enforce your policy, and that can be shared and used in CI/CD processes.
