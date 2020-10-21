# Substra Cheatsheet

## Preflight Checks

Use `substra --version` to ensure your installation is working, with the corresponding version with the server, according to the [compatibility table](https://github.com/SubstraFoundation/substra#compatibility-table).

> Note: the `--force-reinstall` option might help if you need to install a new version of the package: `pip install substra==<VERSION> --force-reinstall`

You can then test that your substra CLI is able to reach the substra network with:

```sh
curl <DOMAIN_URL>/readiness

# demo example
curl substra-backend.node-1.com/readiness
```

> Note: If you do not see the "OK" message, we might need to check your configuration (hint: /etc/hosts).

You can also setup a profile, this is useful if you work with several configurations, but not required, so you can omit the `--profile <PROFILE_NAME>` option if you don't use a specific profile.
```sh
substra config --profile <PROFILE_NAME> <URL>

# demo example
substra config --profile node-1 http://substra-backend.node-1.com
```

> Note: the profile name is a local resource located in `~/.substra`, this means that you can use any name you want.

## login

```sh
substra login --profile <PROFILE_NAME> --username <USERNAME> --password <PASSWORD>

# demo example
substra login --profile node-1 --username node-1 --password 'p@$swr0d44'
```

## List & Get assests => describe assets

Assets you can list:
- node: nodes in substra network
- dataset: registered datasets
- objective: ML question
- algo
- compute_plan: the "blueprint" of tasks to be executed accross the substra network

```sh
substra list node
substra list dataset
substra list objective
substra list algo
substra list complute_plan

substra list traintuple
substra list testtupuble
```

Theses commands will return the **keys** of the assets that you will then need to use commands like `get`, `describe` or `download`.

You will then have to use:

```sh
substra get traintuple <KEY>
substra get testtuple <KEY>
substra get compute_plan <KEY>
substra get algo <KEY>
```

For example, you will get more information about this `testuple`, like its `STATUS`, `RANK`, `PERF` or associated `PERMISSIONS`:

```sh
substra get testtuple d8238915246f02c3b0f8cd4aa0a9546b31daa72477f98fddb5b2137a353d0c1c
KEY                         d8238915246f02c3b0f8cd4aa0a9546b31daa72477f98fddb5b2137a353d0c1c
TRAINTUPLE KEY              28d4c395daac2aa80f46792cbeb4572135cd0aa06042b47f400de65271eacc2f
TRAINTUPLE TYPE             traintuple
ALGO KEY                    7e039d490df04ed3f613191d3af004a1147f22b1f28b942f11a6c830caeb9e9b
ALGO NAME                   Titanic: Random Forest
OBJECTIVE KEY               1158d2f5c0cf9f80155704ca0faa28823b145b42ebdba2ca38bd726a1377e1cb
CERTIFIED                   True
STATUS                      doing
PERF                        0
DATASET KEY                 c1d9b42d9538f825109c38c4f599d47391347d198a0770331ba790b7ebcfaa40
TEST DATA SAMPLE KEYS       2 keys
RANK                        0
TAG
COMPUTE PLAN ID
LOG
CREATOR                     MyOrg1MSP
WORKER                      MyOrg1MSP
PERMISSIONS                 Processable by its owner only
```

## Register assets

- data samples
- dataset
- objective

## Run-local

TODO

## Tips

By default, `--pretty` formatting is used, but you might want to use a yaml or json output format with `-o` for `--output` with the arguments `yaml` or `json`.

Another perk is that you can directly the log verbosity with `--log-level` and one of these parameter `DEBUG, INFO, WARNING, ERROR, CRITICAL`.


## Help

Remember, you can use anytime the the substra CLI `--help` command which is well populated!

```sh
substra --help
substra <command> --help
```

## TODO

- link SDK & CLI methods
