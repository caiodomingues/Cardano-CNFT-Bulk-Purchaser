# Cardano NFT Bulk Purchaser

Utilize este comando para habilitar o script: `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

\*\*_É necessário possuir PowerShell Versão 7.x_\*\*

Esta ferramenta é usada para comprar Drops NFT da rede Cardano utilizando várias carteiras por meio do `cardano-cli`, para permitir compras rápidas em drops de alta demanda.

A ferramenta assume que o Drop CNFT utiliza ou permite várias compras enviadas para um único endereço e que o custo de cada CNFT é conhecido antes do drop.

Esta ferramenta irá alavancar o `cardano-cli.exe` que vem com a carteira Daedalus e se comunica com o blockchain usando o processo `cardano-node.exe` que é executado quando a carteira Daedalus está em execução. **Certifique-se de que o Daedalus esteja em execução antes de tentar usar esta ferramenta.**

## Módulos

Essa ferramenta tem 4 modos: Build, Verify, Send, Redeem

### Build

O módulo `Build` cria N novos endereços e chaves de pagamento que podem ser usados para comprar tokens CNFT. Certifique-se de financiar cada endereço com um único UTXO **cobrindo o custo** do CNFT, além de alguns ADA extras para cobrir as taxas de transação para envio e resgate.

### Verify

O módulo `Verify` verifica o conteúdo atual de cada endereço gerado para fins de verificação manual e armazena em cache o primeiro UTXO no disco para garantir que possa ser enviado o mais rápido possível.

### Send

O módulo `Send` enviará `X` ADA de cada carteira gerada para a carteira receptora especificada. Este módulo será utilizado para a compra do CNFT.

### BulkSend

O módulo `BulkSend` enviará `X` \* `N` ADA de uma única carteira gerada para a carteira do receptor em massa especificada. Este módulo pode ser usado para comprar CNFTs enviando várias transações de saída de uma única carteira.

### Redeem

O módulo `Redeem` extrairá todos os ADA e todos os CNFT e os enviará de volta para a carteira especificada.

## Argumentos

`-count`: O número de carteiras e endereços para Build, Verify, Send, Redeem.

`-build`: Defina a flag de compilação para executar o módulo `Build`.

`-verify`: Defina a flag de verificação para executar o módulo `Verify`.

`-cost`: O preço em ADA que o módulo `Send` enviará para o endereço `-receiver`.

`-receiver`: O endereço para o qual o ADA é enviado ao executar o módulo `Send`.

`-bulkcost`: O preço em ADA que o módulo `BulkSend` enviará para o endereço `-bulkreceiver` em cada transação de saída.

`-bulkreceiver`: O endereço ADA é enviado ao executar o módulo `BulkSend`.

`-wallet`: O endereço ADA e CNFTs são enviados ao executar o módulo `Redeem`.

`-safemode`: Defina o modo `Send` ou `Receive` para ser executado no modo de segurança, solicitando aprovação antes de enviar transações. Isso permitirá tempo extra para verificar novamente os argumentos da linha de comando para garantir que o ADA seja enviado para o local correto.

## Exemplos

--- Build 3 wallets ---

```sh
.\purchase.ps1 -count 3 -build

Wallet Address 1: addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
Wallet Address 2: addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab
Wallet Address 3: addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaac
What is the cost in ADA for the NFT mint?: 1
It is recommended to send at least 3 ADA to each wallet in order to cover transaction costs of sending and redeeming all assets.
```

--- Verifique se o conteúdo da carteira está preenchido e armazene em cache os UTXOs ---

```sh
.\purchase.ps1 -count 3 -verify

[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa] UTXO Data: Hash=3ee0391dbe1025eb78b28a45f84678e7c1b0e38d3628cd97eac5b317a4adb36c, Ix=0, Amount=3 ADA
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab] UTXO Data: Hash=6e04f8326e694aea13f25727cd4ce7d39eb8f63c0a6f546a8dd5007bb6cb2680, Ix=0, Amount=3 ADA
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaac] UTXO Data: Hash=51a3a06daa64063d58c9991a2eeceb76bc5ba46439831a6bec1603e2f634a8e4, Ix=0, Amount=3 ADA

```

--- Envie 1 ADA para um endereço CNFT ---

```sh
.\purchase.ps1 -count 3 -cost 1 -receiver addr1zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz -safemode

Warning!
Are you sure you want to proceed sending [1] ADA to the following address:
[addr1zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz]?
[Y] Yes  [N] No  [?] Help (default is "N"): Y
Using Cache File
[10-11-2021 20:33:17:838] Transaction successfully submitted.
Using Cache File
[10-11-2021 20:33:17:994] Transaction successfully submitted.
Using Cache File
[10-11-2021 20:33:18:134] Transaction successfully submitted.
```

--- BulkSend 3 ADA para um endereço CNFT, usando 3 transações de saída de 1 ADA cada ---

```sh
.\purchase.ps1 -count 3 -bulkcost 1 -bulkreceiver addr1zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz -safemode

Warning!
Are you sure you want to proceed sending [1*3] ADA to the following address:
[addr1zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz]?
[Y] Yes  [N] No  [?] Help (default is "N"): Y
Using Cache File
[10-11-2021 20:33:17:838] Transaction successfully submitted.
```

--- Verifique o conteúdo da carteira UTXOs recebidos ---

```sh
.\purchase.ps1 -count 3 -verify

[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa] UTXO Data: Hash=47e8be3e57052a64de982398110aa4b78538f728a06f20ec49f75ebde6401115, Ix=1, Amount=1.824731 ADA
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa] UTXO Data: Hash=e3b3bfbf2e688bfb9451319c9804cc5003d2b34eba860832de02d11e558571be, Ix=0, Amount=1.37928 ADA, Tokens=[1 tttttttttttttttttttttttttttttttttttttttttttttttttttttttt.TOKEN1]
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab] UTXO Data: Hash=4dfedb8a1344fea885fbb7e60f0af3e06f760995b9e7da84e154180d1efe651c, Ix=1, Amount=1.824731 ADA
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaab] UTXO Data: Hash=d87a8599c46780dfc22e33b6f8234862974262dc607584756a88b64a31557abb, Ix=0, Amount=1.37928 ADA, Tokens=[1 tttttttttttttttttttttttttttttttttttttttttttttttttttttttt.TOKEN2]
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaac] UTXO Data: Hash=4f0ef523cd03a9b2a999e138bd76eab9b95846ddc601256d16b25aff5ca82a8a, Ix=0, Amount=1.37928 ADA, Tokens=[1 tttttttttttttttttttttttttttttttttttttttttttttttttttttttt.TOKEN3]
[addr1aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaac] UTXO Data: Hash=c81c25bee1a4b8d451374d8e16e03b694fdc9be87bbf2b29c9e6ac044d0e83a7, Ix=1, Amount=1.824731 ADA
```

--- Resgate qualquer ADA restante e os CNFTs para a carteira do proprietário original ---

```sh
.\purchase.ps1 -count 3 -wallet addr1bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb

Warning!
Are you sure you want to proceed sending [3.025706] ADA and the assets [1 tttttttttttttttttttttttttttttttttttttttttttttttttttttttt.TOKEN1] to the following address:
[addr1bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb]?
[Y] Yes  [N] No  [?] Help (default is "N"): Y
[10-11-2021 20:34:21:104] Transaction successfully submitted.

Warning!
Are you sure you want to proceed sending [3.025706] ADA and the assets [1 tttttttttttttttttttttttttttttttttttttttttttttttttttttttt.TOKEN2] to the following address:
[addr1bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb]?
[Y] Yes  [N] No  [?] Help (default is "N"): Y
[10-11-2021 20:34:24:485] Transaction successfully submitted.

Warning!
Are you sure you want to proceed sending [3.025706] ADA and the assets [1 tttttttttttttttttttttttttttttttttttttttttttttttttttttttt.TOKEN3] to the following address:
[addr1bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb]?
[Y] Yes  [N] No  [?] Help (default is "N"): Y
[10-11-2021 20:34:26:824] Transaction successfully submitted.
```

## Doações

Se este script for útil para você, considere doar a ADA para o seguinte endereço. Lembrando que este repositório é um fork, o endereço abaixo é do criador original, e não meu (caiodomingues):

```txt
addr1q9afhw5v8rkydmvd34kl6mjvllr58lsf8kjv8wnyftf73g4utnxgcn0srryfpc4tmlq0n9lr9w5uhzqax88dneyhs48q84wugk
```
