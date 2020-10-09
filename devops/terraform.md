# Terraform

### Add profile to AWS Vault

```
aws-vault add <profile>
```

### Authenticate aws-vault to access AWS console for a time duration

```
aws-vault exec <profile> --duration=12h
```

### Run Terraform through docker-compose

```
docker-compose run --rm tf <command>
```

### <command> values and their function

| Command    | Function                                               |
| ---------- | ------------------------------------------------------ |
| `init`     | Initialize `.terraform` project                        |
| `fmt`      | Format/Prettify `.tf` file                             |
| `validate` | Validate `.tf` file                                    |
| `plan`     | Compare `initial` to `final` state and propose changes |
| `apply`    | `plan` and apply the proposed changes                  |
| `destroy`  | Destroy all resources                                  |
