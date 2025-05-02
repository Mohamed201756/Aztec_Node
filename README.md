# Aztec_Node

# How to Run a Sequencer Node (Linux & Windows)

> هذه المقالة تحتوي على طريقة تشغيل عقدة Sequencer على نظامي **Linux** و **Windows**.

---

## Background

The Aztec sequencer node is critical infrastructure responsible for ordering transactions and producing blocks.

When transactions enter the network, the sequencer node bundles them into blocks, checking various constraints such as gas limits, block size, and transaction validity. Before a block can be published, it must be validated by a committee of other sequencer nodes (validators in this context) who re-execute the transactions to verify their correctness. These validators attest to the block's validity by signing it, and once enough attestations are collected (two-thirds of the committee plus one), the sequencer can submit the block to L1.

The **archiver** component complements this process by maintaining historical chain data. It continuously monitors L1 for new blocks, processes them, and maintains a synchronized view of the chain state.

---

## Prerequisites

Make sure you:

- Have the `aztec` tool installed.
- Are using the correct version by running:  
  ```bash
  aztec-up alpha-testnet
  ```
- Are running a **Linux** or **MacOS** machine (Windows مدعوم أدناه).
- Join the [Discord](https://discord.gg) community for support.

---

## Setting Up Your Sequencer

This guide uses the `aztec start` command.

The `aztec start` tool assigns default values based on the `--network` flag and launches a docker container running the sequencer.

You will need:

### RPCs

- **L1 Execution Client** (example: Geth, Nethermind).
- **L1 Consensus Client** (example: Lighthouse, Prysm).
- **Blob Sink Server** (optional, for better blob handling).

### Ethereum Keys

- Private key via `--sequencer.validatorPrivateKey`
- Public address via `--sequencer.coinbase`

> **Disclaimer:** يفضل إنشاء مفتاح خاص جديد لأغراض الأمان.

### Networking

- Forward UDP/TCP traffic on port `40400`.
- Configure your router for a static DHCP IP.
- Pass your external IP via `--p2p.p2pIp`.

### Sepolia ETH

- احصل على Sepolia ETH من [Sepolia Faucet](https://faucet.sepolia.dev/) أو عبر مجتمع الديسكورد.

---

## Running the Sequencer (Linux)

```bash
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls https://example.com \
  --l1-consensus-host-urls https://example.com \
  --sequencer.validatorPrivateKey 0xYourPrivateKey \
  --sequencer.coinbase 0xYourAddress \
  --p2p.p2pIp 999.99.999.99 \
  --p2p.maxTxPoolSize 1000000000
```

> **Tip:** لمعرفة الآي بي العام الخاص بك:  
> ```bash
> curl ifconfig.me
> ```

---

## Running the Sequencer (Windows)

على Windows استخدم الـ Command Prompt أو PowerShell وقم بتشغيل:

```powershell
aztec start --node --archiver --sequencer `
  --network alpha-testnet `
  --l1-rpc-urls https://example.com `
  --l1-consensus-host-urls https://example.com `
  --sequencer.validatorPrivateKey 0xYourPrivateKey `
  --sequencer.coinbase 0xYourAddress `
  --p2p.p2pIp 999.99.999.99 `
  --p2p.maxTxPoolSize 1000000000
```
> ملاحظة: في PowerShell استخدم العلامة `\`` بدلاً من `\` لتقسيم الأوامر على عدة أسطر.

---

## Register as a Validator

```bash
aztec add-l1-validator \
  --l1-rpc-urls https://eth-sepolia.g.example.com/example/your-key \
  --private-key your-private-key \
  --attester your-validator-address \
  --proposer-eoa your-validator-address \
  --staking-asset-handler 0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2 \
  --l1-chain-id 11155111
```

> **Warning:** قد تواجه رسالة تفيد بأن عدد المسجلين اليوم قد اكتمل. حاول لاحقًا.

---

## Advanced Configuration

### Using Environment Variables

يمكنك إنشاء ملف `.env` يحتوي على:

```
ETHEREUM_HOSTS=https://example.com
L1_CONSENSUS_HOST_URLS=https://example.com
```

ثم تقوم بتشغيل:

```bash
source .env
aztec start --network alpha-testnet --archiver --node --sequencer
```

---

### Using Docker Compose

ملف `docker-compose.yml` نموذجي:

```yaml
name: aztec-node
services:
  network_mode: host
  node:
    image: aztecprotocol/aztec:0.85.0-alpha-testnet.5
    environment:
      ETHEREUM_HOSTS: ""
      L1_CONSENSUS_HOST_URLS: ""
      DATA_DIRECTORY: /data
      VALIDATOR_PRIVATE_KEY: $VALIDATOR_PRIVATE_KEY
      P2P_IP: $P2P_IP
      LOG_LEVEL: debug
    entrypoint: >
      sh -c 'node --no-warnings /usr/src/yarn-project/aztec/dest/bin/index.js start --network alpha-testnet start --node --archiver --sequencer'
    ports:
      - 40400:40400/tcp
      - 40400:40400/udp
      - 8080:8080
  volumes:
    - /home/my-node/node:/data
```

---

## Troubleshooting

### L1 Access from Docker

إذا كنت تستخدم Node محلي:

- استخدم `host.docker.internal` بدلاً من `localhost`:

```bash
--l1-rpc-urls http://host.docker.internal:8545
```

- أو استخدم:

```yaml
network_mode: "host"
```
> ⚠️ ملاحظة: `network_mode: host` تعمل فقط على Linux.

---

## Final Notes

- يمكنك تشغيل عقدة Sepolia الخاصة بك عبر Geth أو Reth.
- تابع مجتمع [Aztec Discord](https://discord.gg) للتواصل مع الشبكة.

---

# 🎉 Happy Sequencing!
