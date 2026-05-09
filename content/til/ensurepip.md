+++
title = "Bootstrapping pip"
date = 2026-05-08
description = "How to bootstrap pip from a python virtual environment"

[extra]
toc = true

[taxonomies]
tags = ["python", "docker", "ensurepip", "venv"]
+++

Today I needed to setup [pip](https://pypi.org/project/pip/) in a python virtual environment inside a Docker container to quickly install a couple dependencies for testing without doing a full rebuild. Unfortunately, `pip` wasn't included as a dependency for the docker container because additional python dependencies didn't normally need to be installed during regular operation. But, luckily for us [there's a python package for that](https://xkcd.com/353/). Enter stage left, [ensurepip](https://docs.python.org/3/library/ensurepip.html).

Activate your virtual environment wherever it resides e.g. `source ./.venv/bin/active` and make a call to the `ensurepip` module with `python -m ensurepip`. You should get output that looks something like this:

```
Looking in links: /tmp/tmpznyyt9jn
Processing /tmp/tmpznyyt9jn/pip-26.0.1-py3-none-any.whl
Installing collected packages: pip
Successfully installed pip-26.0.1
```

There you go, `pip` ready and rarin' to go
