---
title: Enviar transações usando o serviço Azure Blockchain
description: Tutorial sobre como usar o serviço Azure Blockchain para implantar um contrato inteligente e enviar uma transação particular.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9037c7b5498a5e0a37b05e5ee09891bf8066393d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417491"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Tutorial: Enviar transações usando o serviço Azure Blockchain

Neste tutorial, você criará nós de transação para testar a privacidade do contrato e da transação.  Você usará o Truffle para criar um ambiente de desenvolvimento local, além de implantar um contrato inteligente e enviar uma transação particular.

Você aprenderá a:

> [!div class="checklist"]
> * Adicionar nós de transação
> * Usar o Truffle para implantar um contrato inteligente
> * Enviar uma transação
> * Validar a privacidade da transação

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir [Criar um membro do blockchain usando o portal do Azure](create-member.md)
* Concluir [Início Rápido: Usar o Truffle para se conectar a uma rede de consórcio](connect-truffle.md)
* Instale o [Truffle](https://github.com/trufflesuite/truffle). O Truffle exige a instalação de várias ferramentas, incluindo [Node.js](https://nodejs.org) e [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Instale o [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). O Python é necessário para o Web3.
* Instalar o [Visual Studio Code](https://code.visualstudio.com/Download)
* Instalar a [extensão Solidity do Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Criar nós de transação

Por padrão, você tem um nó de transação. Adicionaremos mais dois. Um dos nós participa da transação particular. O outro não é incluído na transação particular.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Navegue até o membro do Azure Blockchain e selecione **Nós de transação > Adicionar**.
1. Conclua as configurações para um novo nó de transação chamado `alpha`.

    ![Criar nó de transação](./media/send-transaction/create-node.png)

    | Configuração | Valor | DESCRIÇÃO |
    |---------|-------|-------------|
    | NOME | `alpha` | Nome do nó de transação. O nome é usado para criar o endereço DNS para o ponto de extremidade do nó de transação. Por exemplo, `alpha-mymanagedledger.blockchain.azure.com`. |
    | Senha | Senha forte | A senha é usada para acessar o ponto de extremidade do nó de transação com a autenticação básica.

1. Selecione **Criar**.

    O provisionamento de um novo nó de transação leva cerca de 10 minutos.

1. Repita as etapas 2 a 4 para adicionar um nó de transação chamado `beta`.

Você pode continuar com o tutorial enquanto os nós são provisionados. Quando o provisionamento for concluído, você terá três nós de transação.

## <a name="open-truffle-console"></a>Abrir o console do Truffle

1. Abra um prompt de comando ou o shell do Node.js.
1. Altere o caminho para o diretório do projeto do Truffle conforme o [Início Rápido: Usar o Truffle para se conectar a uma rede de consórcio](connect-truffle.md) de pré-requisito. Por exemplo,

    ```bash
    cd truffledemo
    ```

1. Inicie o console de desenvolvimento interativo do Truffle.

    ``` bash
    truffle develop
    ```

    O Truffle cria um blockchain de desenvolvimento local e fornece um console interativo.

## <a name="create-ethereum-account"></a>Criar conta Ethereum

Use o Web3 para se conectar ao nó de transação padrão e criar uma conta do Ethereum. Obtenha a cadeia de conexão do Web3 no portal do Azure.

1. No portal do Azure, navegue até o nó de transação padrão e selecione **Nós de transação > Código de exemplo > Web3**.
1. Copie o JavaScript de **HTTPS (Chave de acesso 1)** ![Código de exemplo do Web3](./media/send-transaction/web3-code.png)

1. Cole o código JavaScript do Web3 do nó de transação padrão no console de desenvolvimento interativo do Truffle. O código criará um objeto Web3 que é conectado ao nó de transação do serviço Azure Blockchain.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Você pode chamar métodos no objeto Web3 para interagir com o nó de transação.

1. Crie uma conta no nó de transação padrão. Substitua o parâmetro de senha por sua própria senha forte.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Anote o endereço da conta retornado e a senha. Você precisará do endereço da conta do Ethereum e da senha na próxima seção.

1. Saia do ambiente de desenvolvimento do Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Configurar o projeto do Truffle

Para configurar o projeto do Truffle, você precisa de algumas informações sobre o nó de transação do portal do Azure.

### <a name="transaction-node-public-key"></a>Chave pública do nó de transação

Cada nó de transação tem uma chave pública. A chave pública permite que você envie uma transação particular ao nó. Para enviar uma transação do nó de transação padrão para o nó de transação *alfa*, é necessária a chave pública do nó de transação *alfa*.

Obtenha a chave pública da lista de nós de transação. Copie a chave pública para o nó alfa e salve o valor para mais tarde no tutorial.

![Lista de nós de transação](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Endereços do ponto de extremidade do nó de transação

1. No portal do Azure, navegue para cada nó de transação e selecione **Nós de transação > Cadeias de conexão**.
1. Copie e salve a URL do ponto de extremidade de **HTTPS (Chave de acesso 1)** para cada nó de transação. Você precisará dos endereços do ponto de extremidade para o arquivo de configuração do contrato inteligente mais adiante no tutorial.

    ![Endereço do ponto de extremidade da transação](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Editar o arquivo de configuração

1. Inicie o Visual Studio Code e abra a pasta do diretório do projeto do Truffle usando o menu **Arquivo > Abrir Pasta**.
1. Abra o arquivo de configuração `truffle-config.js` do Truffle.
1. Substitua o conteúdo do arquivo pelas informações de configuração a seguir. Adicione variáveis que contenham os endereços dos pontos de extremidade e os dados da conta. Substitua as seções de colchete angular pelos valores coletados nas seções anteriores.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gas: 0,
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        }
      }
    }
    ```

1. Salve as alterações em `truffle-config.js`.

## <a name="create-smart-contract"></a>Criar um contrato inteligente

1. Na pasta **contracts**, crie um arquivo chamado `SimpleStorage.sol`. Adicione os códigos a seguir.

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. Na pasta **migrations**, crie um arquivo chamado `2_deploy_simplestorage.js`. Adicione os códigos a seguir.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Substitua os valores nos colchetes angulares.

    | Valor | DESCRIÇÃO
    |-------|-------------
    | \<alpha node public key\> | Chave pública do nó alfa
    | \<Ethereum account address\> | Endereço da conta do Ethereum criado no nó de transação padrão

    Neste exemplo, o valor inicial do valor **storeData** é definido como 42.

    **privateFor** define os nós para os quais o contrato está disponível. Neste exemplo, a conta do nó de transação padrão pode converter transações particulares para o nó **alfa**. Adicione chaves públicas para todos os participantes da transação privada. Se você não incluir **privateFor:** e **from:** , as transações do contrato inteligente serão públicas e poderão ser vistas por todos os membros do consórcio.

1. Salve todos os arquivos selecionando **Arquivo > Salvar Tudo**.

## <a name="deploy-smart-contract"></a>Implantar o contrato inteligente

Use o Truffle para implantar `SimpleStorage.sol` na rede do nó de transação padrão.

```bash
truffle migrate --network defaultnode
```

O Truffle compila primeiro e, em seguida, implanta o contrato inteligente **SimpleStorage**.

Saída de exemplo:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Validar a privacidade do contrato

Devido à privacidade do contrato, os valores do contrato só podem ser consultados nos nós declarados em **privateFor**. Neste exemplo, podemos consultar o nó de transação padrão porque a conta existe nesse nó. 

1. Usando o console do Truffle, conecte-se ao nó de transação padrão.

    ```bash
    truffle console --network defaultnode
    ```

1. No console do Truffle, execute o código que retorna o valor da instância do contrato.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Se a consulta do nó de transação padrão for bem-sucedida, o valor 42 será retornado. Por exemplo:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Saia do console do Truffle.

    ```bash
    .exit
    ```

Como declaramos a chave pública do nó **alfa** em **privateFor**, podemos consultar o nó **alfa**.

1. Usando o console do Truffle, conecte-se ao nó **alfa**.

    ```bash
    truffle console --network alpha
    ```

1. No console do Truffle, execute o código que retorna o valor da instância do contrato.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Se a consulta do nó **alfa** for bem-sucedida, o valor 42 será retornado. Por exemplo:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Saia do console do Truffle.

    ```bash
    .exit
    ```

Como não declaramos a chave pública do nó **beta** em **privateFor**, não poderemos consultar o nó **beta** devido à privacidade do contrato.

1. Usando o console do Truffle, conecte-se ao nó **beta**.

    ```bash
    truffle console --network beta
    ```

1. Execute um código que retorna o valor da instância do contrato.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. A consulta do nó **beta** falha, pois o contrato é particular. Por exemplo:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Saia do console do Truffle.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Enviar uma transação

1. Crie um arquivo chamado `sampletx.js`. Salve-o na raiz do projeto.
1. O script a seguir define o valor da variável **storedData** do contrato como 65. Adicione o código ao novo arquivo.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Substitua os valores nos colchetes angulares e, em seguida, salve o arquivo.

    | Valor | DESCRIÇÃO
    |-------|-------------
    | \<alpha node public key\> | Chave pública do nó alfa
    | \<Ethereum account address\> | Endereço da conta do Ethereum criado no nó de transação padrão.

    **privateFor** define os nós para os quais a transação está disponível. Neste exemplo, a conta do nó de transação padrão pode converter transações particulares para o nó **alfa**. Você precisa adicionar as chaves públicas para todos os participantes da transação particular.

1. Use o Truffle para executar o script para o nó de transação padrão.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. No console do Truffle, execute o código que retorna o valor da instância do contrato.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    Se a transação for bem-sucedida, o valor 65 será retornado. Por exemplo:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Saia do console do Truffle.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Validar a privacidade da transação

Devido à privacidade da transação, as transações só podem ser executadas nos nós declarados em **privateFor**. Neste exemplo, podemos executar transações, pois declaramos a chave pública do nó **alfa** em **privateFor**. 

1. Use o Truffle para executar a transação no nó **alfa**.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Execute um código que retorna o valor da instância do contrato.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    Se a transação for bem-sucedida, o valor 65 será retornado. Por exemplo:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Saia do console do Truffle.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado pelo serviço Azure Blockchain.

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir.
1. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou dois nós de transação para demonstrar a privacidade do contrato e da transação. Você usou o nó padrão para implantar um contrato inteligente particular. Você testou a privacidade consultando valores de contrato e executando transações no blockchain.

> [!div class="nextstepaction"]
> [Como desenvolver aplicativos de blockchain usando o serviço Azure Blockchain](develop.md)
