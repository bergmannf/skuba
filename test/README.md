# End to End tests for skuba

The test can be run locally or in CI.

# Summary:

- [Requirements](#requirements)
- [Howto](#howto)
- [Testing Guidelines](#testing-guidelines)
- [Specification/API](#specification)


# Requirements:

- The infrastructure should be already deployed (using Terraform is not mandatory; only the IP addresses are needed). For more information see mandatory variables for the number of instances required.

- Running _ssh-agent_ socket at `/tmp/ssh-agent-sock`. You can do that by running: `./ci/tasks/setup-ssh.py`. Otherwise, you can try manually, by typing: `eval $(ssh-agent -a /tmp/ssh-agent-sock); ssh-add ci/infra/id_shared`. 

- You can verify whether the key has been loaded correctly into the _ssh-agent_ by issuing the command: `ssh-add -L`.

- Last but not least, you should be able to connect non-interactively to (either master or worker) node as a _sles_ user : `ssh sles@<node-ip>`.

# Howto 

You can run the `e2e-tests` in 2 ways:

1) with IP adress with the supported env. variables (see later)

`CONTROLPLANE=10.86.2.23 MASTER00=10.86.1.103 WORKER00=10.86.2.130 make test-e2e` 

2) without IP but with a terraform state,  specify the platform you have deployed the infrastructure.
`IP_FROM_TF_STATE=TRUE PLATFORM=openstack make test-e2e`

This method will read the tfstate file and read the IPs of host and pass them to `ginkgo`

Boths methods are convenients: 1) method is usefull when we don't have the terraform state.

3) Use a custom ginkgo binary:

`~/go/src/github.com/SUSE/skuba> GINKGO_BIN_PATH="$PWD/ginkgo" IP_FROM_TF_STATE=TRUE PLATFORM=openstack make test-e2e`
In the following example we assume you have builded ginkgo from vendor.

# Testing Guidelines:

## Assertions:

- Use `Should`  for assertion`gomega.Expect(err).Should(gomega.BeNil()` over `to`.

- Use `Expect` over `Ω`

## Misc:

### Loops/Timeouts:

Don't create a customized loop for checking each minute that a condition is satisfied. Instead use `eventually`.
For examples, look in the test codebases,  we already use `eventually` in some tests.

Upstream doc: https://onsi.github.io/gomega/#eventually

# Specification

## create new ENV variables
Syntax: use `_` underscores for separating words and use UPPERCASE for naming.

Adding NEW variables:

- IPs of nodes when needed can be added in the python and golang code.

In general adding new behaviour variable should be discussed within the team, since we need to keep the variable minimalist as possible.

All needed variable should have been already implemented, only the NODE ips variable should be added. 

When you create a var specify also the behaviour and the usage: mandatory/optional and default value if any.


## Variables  supported:

### Cluster:

- `CLUSTERNAME`: - optional - name of the cluster. Default `e2e-cluster`
- `CONTROLPLANE`: - mandatory - IP of host which will be the controlplane
- `MASTER00`: - mandatory - IP of 1st master
- `WORKER00`: - mandatory - IP of 1st worker

SEE 1) example in HOW TO RUN

As showed, in future we will have `WORKER01`, `MASTER01`, `MASTER02` etc. 

### Skuba setup

- `SKUBA_DEBUG`: - optinal - Debug level. Default value is `3`
- `SKUBA_USERNAME`: - optional - username used by `skuba` to connecto to machines - default `sles`

### Environment setup variables:

- `IP_FROM_TF_STATE`: - optional - if set to `TRUE` this will read terraform states. Default: false
- `PLATFORM`: - optional - this specify the provider used. (libvirt, openstack, vmware, etc). Default: None.
- `SKUBA_BIN_PATH`: - optional - for specify the full path of a skuba binary. ( e.g if you use an RPM). Default: GOPATH
- `GINKGO_BIN_PATH`: - optional -  use this var for passing a fullpath to a ginkgo bin which will be used by tests. Default: your Path
SEE 2) example in HOW TO RUN


# Internal info

The Env. variables are set by `ci/tasks/e2e-tests.py`.

This script pass ENV. Variables to ginkgo and call the ginkgo binary which will run the e2e tests.

In this way we can run easy tests locally or in remote CI
