Azure Load Balancer management scripts
=======================================

Scope
------

This descirbes how to use the scripts for manageing azure loadbalancers. These
scripts use the `az`  CLI v2.0 to execute commands. Some assumptions are made
to properly use these commands:

- `az login` has been executed successfully
- `az account show` shows the proper account to be used

Assumptions
-----------

The assumptions are that :

- The VM's are already created on the same network
