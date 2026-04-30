# Using the API

In addition to [the pyinfra CLI](../cli.md), pyinfra provides a full Python API. As of `v3` this API can be considered mostly stable. See [the API reference](reference.md).

You can also reference [pyinfra's own main.py](https://github.com/pyinfra-dev/pyinfra/blob/3.x/src/pyinfra_cli/main.py), and the [pyinfra API source code](https://github.com/pyinfra-dev/pyinfra/tree/3.x/src/pyinfra/api).

## Full Example

[A full example of how to use the API can be found here](https://github.com/pyinfra-dev/pyinfra-examples/blob/main/.old/api_deploy.py). (Note: this is not yet tested and is pending future updates)

## Basic Localhost Example

```python
from pyinfra.api import Config, Inventory, State
from pyinfra.api.connect import connect_all
from pyinfra.api.operation import add_op
from pyinfra.api.operations import run_ops
from pyinfra.api.facts import get_facts
from pyinfra.facts.server import Os
from pyinfra.operations import server

# Define your inventory (@local means execute on localhost using subprocess)
# https://docs.pyinfra.com/en/3.x/apidoc/pyinfra.api.inventory.html
inventory = Inventory((["@local"], {}))

# Define any config you need
# https://docs.pyinfra.com/en/3.x/apidoc/pyinfra.api.config.html
config = Config(SUDO=True)

# Set up the state object
# https://docs.pyinfra.com/en/3.x/apidoc/pyinfra.api.state.html
state = State(inventory=inventory, config=config)

# Connect to all the hosts
connect_all(state)

# Start adding operations
result1 = add_op(
    state,
    server.user,
    user="pyinfra",
    home="/home/pyinfra",
    shell="/bin/bash",
)
result2 = add_op(
    state,
    server.shell,
    name="Run some shell commands",
    commands=["whoami", "echo $PATH", "bash --version"]
)

# And finally we run the ops
run_ops(state)

# add_op returns an OperationMeta for each op, letting you access stdout, stderr, etc. after they run
host = state.hosts.inventory['@local']
print(result1.changed, result1[host].stdout, result1[host].stderr)
print(result2.changed, result2[host].stdout, result2[host].stderr)

# We can also get facts for all the hosts
# https://docs.pyinfra.com/en/3.x/apidoc/pyinfra.api.facts.html
print(get_facts(state, Os))
```
