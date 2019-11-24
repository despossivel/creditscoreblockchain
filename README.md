# Confiança descentralizada com Ethereum (cenário de pontuação de crédito)

Este é um Ethereum DApp legível, escrito em Solidity (e Node.js), baseado em um cenário minimalista de compartilhamento de pontuação de crédito. Ele mostra como as informações podem ser compartilhadas entre os participantes de uma blockchain.

Faz parte do cenário de exemplo [Confiança descentralizada no Centro de Arquitetura do Azure] [arquitetura].

Além do contrato inteligente, este repositório fornece todo o código necessário para compilar, implantar e se comunicar com o contrato inteligente na blockchain. Ele pode ser usado como ponto de partida para seu projeto de confiança descentralizado. A compilação, implantação e vários outros componentes também podem ser usados ​​como clichê e ferramentas no seu próprio projeto.

[Ethers.js] [ethers] é usado em todo este aplicativo porque eu gosto mais do que o Web3.js. mais comum.

Ao ler este README e as várias partes de arquivos referidas neste repositório, você entenderá os contratos inteligentes e como ele funciona dentro de uma blockchain para permitir troca e compartilhamento confiáveis ​​de informações.

## Requerimento

Você precisará ter uma blockchain Ethereum.

Você pode criar uma blockchain privada no Azure para seguir este guia. Consulte o [cenário de exemplo de confiança descentralizada no Azure Architecture Center] [arquitetura] para obter mais informações.

Para fins de desenvolvimento, você pode seguir este guia para iniciantes com Ganache:

```console
$ ganache-cli -i 10101010 -g 0
```

Para executar o código neste guia, você precisará do Node.js e do NPM.


## Instalação

Apenas clone este repositório e execute `npm install`:

```console
$ git clone https://github.com/vitoc/creditscoreblockchain.git
$ cd creditscoreblockchain
$ npm install
```
## Terminal JSON-RPC

A primeira coisa a obter e configurar é o terminal JSON-RPC. Esse é o meio pelo qual acessamos a API do blockchain.

Se você seguiu o [exemplo no centro de arquitetura do Azure] [arquitetura], essas informações estão no email enviado a você após a implantação bem-sucedida:

<img src = "https://github.com/vitoc/creditscoreblockchain/blob/master/media/mail.png" width = "500" />

Coloque esse terminal em um arquivo `constants.js` que você precisará criar no diretório em que este repositório é clonado:

```js
const constants = {
    JSON_RPC_ENDPOINT: 'http://ethzgpjre-dns-reg1.southeastasia.cloudapp.azure.com:8540'
}

module.exports = constants;
```
> Observe que o `constants.js` não está no repositório clonado, você precisará criar um para esse fim.

## ID da cadeia

Isso é para garantir que estamos lidando com a corrente certa!

Se você seguiu o [exemplo no centro de arquitetura do Azure] [arquitetura], esta é a `ID da rede 'que você especificou ao criar a blockchain privada por meio do Portal do Azure.

Coloque isso no arquivo `constants.js` também:

```js
const constants = {
    JSON_RPC_ENDPOINT: 'http://ethzgpjre-dns-reg1.southeastasia.cloudapp.azure.com:8540',
    CHAIN_ID: 10101010// <== YOUR CHAIN ID HERE
}

module.exports = constants;
```

## Compilando

A ABI e o bytecode necessários para implantar o contrato inteligente em sua blockchain Ethereum estão incluídos neste repositório. Também está incluída uma ferramenta que você pode usar para (re) compilar rapidamente o contrato novamente quando fizer alterações, etc. É assim que usá-lo:


```console
$ node compile.js contract.sol :contractName
```

Para o cenário, para recompilar, faça:

```console
$ node compile.js CreditScore.sol :CreditScore
```
## Carteiras

Por conveniência, este aplicativo lê as chaves de 3 carteiras necessárias em 3 arquivos:

* bankA.wallet
* bankB.wallet
* personA.wallet

Para fins de teste, você pode gerar a carteira acima executando:

```console
$ node generate_wallet.js
```

Obviamente, você mesmo pode criar essas chaves privadas com as ferramentas de sua preferência.

## Implantando o contrato na blockchain

Com isso, agora podemos implantar o contrato no blockchain:

```console
$ node deploy_contract.js bankA.wallet
```

Aqui, usamos a carteira do banco A para criar a transação para implantação, para que ela seja a proprietária do contrato. Uma vez feito, o endereço do contrato será mostrado a você. Anote isso!

## Como obter endereços de bancos e pessoas

Há uma ferramenta bacana neste repositório que mostra o endereço de um banco ou de uma pessoa no blockchain quando recebe a chave privada da entidade:

```console
$ node address.js bankB.wallet
Wallet address: 0xa8F782D3dAAAAA1F1452aA9e9628cD9YYYYYAa63
```

Você precisará dos endereços impressos na tela por vários motivos nas chamadas subseqüentes à blockchain.

## Permitir que outros bancos atualizem as pontuações de crédito

Há uma verificação de segurança no contrato para permitir que apenas bancos registrados atualizem as pontuações (certamente não queremos que as pessoas atualizem suas próprias pontuações!).

Atualmente, o banco A é a única entidade com permissão para atualizar as pontuações de crédito. Para permitir que mais bancos B façam isso também:

```console
$ node enroll.js [YOUR CONTRACT'S ADDRESS] [BANK B'S ADDRESS]
```

## Criando / atualizando a pontuação de crédito de uma pessoa

Aqui é onde finalmente criamos uma pontuação de crédito para uma pessoa.

Por uma questão de exemplo, vamos atribuir uma pontuação de crédito para a pessoa A. Primeiro, precisamos obter o endereço da carteira da pessoa A:

$ node address.js personA.wallet

Em seguida, atribuímos uma pontuação de 80 à pessoa A (conforme identificado pelo endereço da carteira):

```console
$ node score.js [YOUR CONTRACT'S ADDRESS] [PERSON A'S WALLET ADDRESS] 80
```

Você pode substituir 80 por qualquer pontuação que desejar (tem que ser um número inteiro positivo). Uma transação será criada para armazenar a pontuação de crédito mais recente da pessoa na blockchain.

## Recuperando a pontuação

Se você digitar o código do comando abaixo, verá que é necessária uma carteira para visualizar os registros na blockchain:

```console
$ node retrieve.js [YOUR CONTRACT'S ADDRESS] [PERSON A'S ADDRESS]
Credit Score: 80
```

## É isso aí!

O objetivo aqui é fornecer uma explicação completa de como criar um contrato até o ponto em que o contrato pode ser usado para armazenar e compartilhar informações com base na confiança descentralizada na blockchain. Dê uma olhada no código nos comandos acima para ver como [Ethers.js] [ethers] é usado com o Node.js para obter as funcionalidades do DApp.

## Obrigado

Se você gosta disso, considere me seguir no [Twitter] [twitter].

[architecture]: https://docs.microsoft.com/en-us/azure/architecture/example-scenario/apps/decentralized-trust
[ethers]: https://github.com/ethers-io/ethers.js/
[twitter]: https://twitter.com/vitoc/