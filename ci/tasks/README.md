# CI tasks:

The main goal of CI tasks is to execute small automation tasks, which will be called by makefile targets.
CI tasks should be used as bridge between local development and remote CI.

# e2e-tests

This script is used in CI also for local development. It is called by makefile.

# sonobuoy_e2e

This scripts helps with running sonobuoy in the CI. Requires python 3.6+

### Basic usage 
Run the tests `sonobuoy_e2e.py run --kubeconfig /path/to/kubeconfig`
Collect the results to $(pwd)/results/ `sonobuoy_e2e.py collect --kubeconfig /path/to/kubeconfig`
Cleanup the sonobuoy pods`sonobuoy_e2e.py cleanup --kubeconfig /path/to/kubeconfig`
 