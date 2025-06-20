# edkit

A tiny toolkit for generating ED25519 public-private key pairs in OpenSSH
format and for data signing.

## Usage

### Key generation

The most important command you'll need is `edkit generate`, which will create 2 files:

- `id_ed25519` (private key)
- `id_ed25519.pub` (public key, in OpenSSH format)

```bash
edkit generate
```

#### CLI options

| option      | Alias | Type                | Description                                                                                                                                                                                     |
| ----------- | ----- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--output`  | `-o`  | path                | Output path (default: `./id_ed25519`). A `.pub` suffix will be auto-appended for the public key. If you're using `--no-pub`, this can be set to `-` to instead output the private key to stdout |
| `--comment` | `-c`  | string              | Public key comment (default: `username@hostname`)                                                                                                                                               |
| `--format`  | `-F`  | `ssh`, `raw`, `pem` | Output format (default: `ssh`)                                                                                                                                                                  |
| `--force`   | `-y`  | flag                | Overwrite existing files                                                                                                                                                                        |
| `--no-pub`  |       | flag                | Don't write the public key                                                                                                                                                                      |

#### Public key generation

If you used the `--no-pub` option, or you somehow lost the public key, it is
possible to construct it from the private key. You can do this with:

```bash
edkit pubkey -k id_ed25519
# or
cat id_ed25519 | edkit pubkey -k -
```

### Signing

You can also use `edkit` with your generated keys to sign a file/message and to
verify this signature afterwards. Note that currently, only a detached
signature generation is supported (separate signature and original file).

```bash
edkit sign -k id_ed25519 -o message.sig -f message.txt
edkit verify -k id_ed25519.pub -s message.sig -f message.txt
```

#### CLI options (sign)

| option     | Alias | Type            | Description                                                             |
| ---------- | ----- | --------------- | ----------------------------------------------------------------------- |
| `--key`    | `-k`  | path            | Private key (default: `id_ed25519`). Use `-` to load the key from stdin |
| `--file`   | `-f`  | path            | The file to be signed. Use `-` to instead sign the passed stdin         |
| `--output` | `-o`  | path            | The output file. Use `-` to instead write the output to stdout          |
| `--format` | `-F`  | `raw`, `base64` | Signature format (default: `base64`)                                    |

#### CLI options (verify)

| option        | Alias | Type | Description                                                                        |
| ------------- | ----- | ---- | ---------------------------------------------------------------------------------- |
| `--key`       | `-k`  | path | Public key (default: `id_ed25519.pub`). Use `-` to instead load the key from stdin |
| `--file`      | `-f`  | path | The signed file. Use `-` to instead verify the passed stdin data                   |
| `--signature` | `-s`  | path | The signature file. Use `-` to instead load the signature from stdin               |

### More

For more flags and info, you can use `edkit help` or `edkit -h`. If you want
help with a specific subcommand, you can also use `edkit generate -h`.
