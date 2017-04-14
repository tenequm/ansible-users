# Ansible Role tests

To run the test playbook in this directory:

  1. Install and start Docker.
  1. Download the test shim (see .travis.yml file for the URL) into `tests/test.sh`:
    - `wget -O tests/test.sh https://gist.githubusercontent.com/1nfinitum/16748392b3a23b78467514f0f0d857a5/raw/`
  1. Make the test shim executable: `chmod +x tests/test.sh`.
  1. Run (from the role root directory) `distro=[distro] playbook=[playbook] ./tests/test.sh`

If you don't want the container to be automatically deleted after the test playbook is run, add the following environment variables: `cleanup=false container_id=$(date +%s)`

## Reference
This style of testing was taken from the [Jeff Geerling's](https://github.com/geerlingguy) repositories and include two changes:
- Installation of OpenSSH server (needed for this `ssh-users` particular role).
- Setting allowed `changed` value to `7` instead of `0` for idempotance test, while it also tests deleting process.
