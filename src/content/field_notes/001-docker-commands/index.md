---
title: "CMD and ENTRYPOINT"
description: "Navigating start commands"
date: "Mar 18 2024"
draft: false
---
Let's start with a simple command:
```sh
echo hello world
```

Now have a look at the following combinations of dockers `CMD` and `ENTRYPOINT`:

```dockerfile
FROM alpine AS example_0
ENTRYPOINT ["echo", "Hello"]
CMD ["World"]

FROM alpine AS example_1
ENTRYPOINT ["echo", "Hello", "World"]

FROM alpine AS example_2
CMD ["echo", "Hello", "World"]

FROM alpine AS example_3
ENTRYPOINT echo "Hello ${2:1:-1}"
CMD "World"

FROM alpine AS example_4
ENTRYPOINT echo "Hello World"

FROM alpine AS example_5
CMD echo "Hello World"
```

If I run these images, they will all output `Hello World`. Don't believe me? Use the docker-compose in the gist and try it out for yourself. After running `docker compose up` you will see the following:

![Docker compose output showing 5 containers printing hello world](./001-docker-commands-01.png)

I always need to remember the differences between these permutations and get confused about the correct choice. So, let's demystify this madness, break down some key choices, and identify sensible defaults.

## Shell vs Exec
### Links
- Docker [docs](https://docs.docker.com/reference/dockerfile/#shell-and-exec-form)

### Shell Style
Shell style commands run in a sub process, the syntax is more liberal. The following is an example of the shell syntax:

```docker
ENTRYPOINT echo "Hello ${2:1:-1}"
```

### Exec Style
Exec-style commands are passed through an array. The syntax is stricter, and the command is expected to be in the order of `command`, `flags`, and `arguments`. Exec commands run in the same main process. A new layer is added to the image for each exec command.

```docker
ENTRYPOINT ["echo", "Hello", "World"]
```

### Sensible Defaults
- `RUN`: Use shell style because it improves readability and provides a command shell by default.
- `CMD` and `ENTRYPOINT`: Use exec style so that your executables run in the main process. Also, exec style is the only way to pass `CMD some_arg` and `docker run some_arg` arguments to the executable.

## CMD and ENTRYPOINT

### ENTRYPOINT
The entry point is the executable of a docker container. Container developers use the entry point to set the default behaviour. The entry point is much harder to override using the docker run command. Here are some example entries:

```docker
# official nodejs image
ENTRYPOINT ["docker-entrypoint.sh"]

# custom python image
ENTRYPOINT ["python3"]

# npm based image
ENTRYPOINT ["npm"]
```

### CMD
The [docker documentation](https://docs.docker.com/reference/dockerfile/) specifies that:

> "The CMD instruction sets the command to be executed when running a container from an image."

When used in conjunction with `ENTRYPOINT`, the `CMD` instructions set arguments to run for an executable.

If `npm` was set as the executable, some example commands might be `start`, `test`, and `format`. Here is another example using Python. I set the executable to be  `ENTRYPOINT [ "python" ]` this allows for the following docker-compose setup:

```yaml
test:
    build:
      context: .
    command: ["-m", "pytest"]

  black:
    build:
      context: .
    command: ["-m", "black", "--check", "."]

  flake8:
    build:
      context: .
    command: ["-m", "flake8", "."]
```

### Sensible Defaults
When selecting an executable for your project, consider combinations that support different utilities and applications such as formatting, testing, and running applications. If you can, be specific and narrow down your choices, for example, using `ENTRYPOINT ["npm"]`. If you want to maximize compatibility, you can set your entry point as an interactive base or shell prompt, for example, `ENTRYPOINT ["/bin/bash"]`.
