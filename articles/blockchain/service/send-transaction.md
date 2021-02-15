---
title: Tutorial de criar, compilar e implantar contratos inteligentes – Azure Blockchain Service
description: Tutorial sobre como usar a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code para criar e implantar um contrato inteligente no Azure Blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: f7605a0c118a40e52210582d2411569795fb25ee
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763682"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Tutorial: Criar, compilar e implantar contratos inteligentes no Azure Blockchain Service

Neste tutorial, use a extensão Azure Blockchain Development Kit para Ethereum no Visual Studio Code para criar e implantar um contrato inteligente no Azure Blockchain Service. Use também o kit de desenvolvimento para executar uma função de contrato inteligente por meio de uma transação.

Você usa o Azure Blockchain Development Kit for Ethereum para:

> [!div class="checklist"]
> * Criar um contrato inteligente
> * Implantar um contrato inteligente
> * Executar uma função de contrato inteligente por meio de uma transação

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Concluir [Início Rápido: usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Extensão Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x ou superior](https://nodejs.org/download)
* [Git 2.10.x ou superior](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [CLI do Ganache 6.0.0](https://github.com/trufflesuite/ganache-cli)

No Windows, um compilador C++ instalado é necessário para o módulo node-gyp. Você pode usar as ferramentas do MSBuild:

* Se o Visual Studio 2017 estiver instalado, configure o npm para usar as ferramentas do MSBuild com o comando `npm config set msvs_version 2017 -g`
* Se o Visual Studio 2019 estiver instalado, defina o caminho das ferramentas de MSBuild para o npm. Por exemplo, `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Caso contrário, instale as ferramentas de build autônomas do VS usando `npm install --global windows-build-tools` em um shell de comando *Executar como administrador* com privilégios elevados.

Para obter mais informações sobre o node-gyp, confira o [repositório node-gyp no GitHub](https://github.com/nodejs/node-gyp).

## <a name="create-a-smart-contract"></a>Criar um contrato inteligente

O Azure Blockchain Development Kit for Ethereum usa modelos de projeto e ferramentas do Truffle para ajudar a realizar scaffold, compilar e implantar contratos. Antes de começar, conclua o pré-requisito [Início Rápido: usar o Visual Studio Code para se conectar a uma rede de consórcio do Azure Blockchain Service](connect-vscode.md). O início rápido percorre a instalação e a configuração do Azure Blockchain Development Kit para Ethereum.

1. Na paleta de comandos do VS Code, escolha **Blockchain: Novo projeto do Solidity**.
1. Escolha **Criar projeto básico**.
1. Crie uma pasta chamada `HelloBlockchain` e escolha **Selecionar novo caminho de projeto**.

O Azure Blockchain Development Kit cria e inicializa um novo projeto do Solidity para você. O projeto básico inclui um contrato inteligente **HelloBlockchain** de exemplo e todos os arquivos necessários para compilação e implantação no membro do consórcio no Azure Blockchain Service. Pode demorar alguns minutos para que o projeto seja criado. Você pode monitorar o progresso no painel do terminal do VS Code selecionando a saída para o Azure Blockchain.

A estrutura do projeto é semelhante ao exemplo abaixo:

   ![Projeto do Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Compilar um contrato inteligente

Os contratos inteligentes estão localizados no diretório **contracts** do projeto. Você compila contratos inteligentes antes de implantá-los em um blockchain. Use o comando **Compilar Contratos** para compilar todos os contratos inteligentes no projeto.

1. Na barra lateral do explorer do VS Code, expanda a pasta **contracts** no projeto.
1. Clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Compilar Contratos** no menu.

    ![Escolha o menu Compilar contratos ](./media/send-transaction/build-contracts.png)

O Azure Blockchain Development Kit usa o Truffle para compilar os contratos inteligentes.

![Saída do compilador do Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Implantar um contrato inteligente

O Truffle usa scripts de migração para implantar seus contratos em uma rede Ethereum. As migrações são arquivos JavaScript localizados no diretório **migrations** do projeto.

1. Para implantar o contrato inteligente, clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Implantar Contratos** no menu.
1. Escolha a rede de consórcio do Azure Blockchain na paleta de comandos. A rede de blockchain de consórcio foi adicionada ao arquivo de configuração do Truffle do projeto quando você criou o projeto.
1. Escolha **Gerar mnemônico**. Escolha um nome de arquivo e salve o arquivo mnemônico na pasta do projeto. Por exemplo, `myblockchainmember.env`. O arquivo mnemônico é usado para gerar uma chave privada Ethereum para o membro do blockchain.

O Azure Blockchain Development Kit usa o Truffle para executar o script de migração para implantar os contratos no blockchain.

![Contrato implantado com êxito](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chamar uma função de contrato

A função **SendRequest** do contrato **HelloBlockchain** altera a variável de estado **RequestMessage**. A alteração do estado de uma rede de blockchain é feita por meio de uma transação. Você pode usar a página de interação do contrato inteligente do Azure Blockchain Development Kit para chamar a função **SendRequest** por meio de uma transação.

1. Para interagir com o contrato inteligente, clique com o botão direito do mouse em **HelloBlockchain.sol** e escolha **Mostrar Página de Interação do Contrato Inteligente** no menu.

    ![Escolha Mostrar Página de Interação do Contrato Inteligente no menu](./media/send-transaction/contract-interaction.png)

1. A página de interação permite que você escolha uma versão de contrato implantada, chame funções, exiba o estado atual e exiba os metadados.

    ![Exemplo da Página de Interação do Contrato Inteligente](./media/send-transaction/interaction-page.png)

1. Para chamar a função de contrato inteligente, selecione a ação de contrato e passe seus argumentos. Escolha ação de contrato **SendRequest** e insira **Olá, Blockchain!** no parâmetro **requestMessage**. Selecione **Executar** para chamar a função **SendRequest** por meio de uma transação.

    ![Executar ação SendRequest](./media/send-transaction/sendrequest-action.png)

Depois que a transação for processada, a seção de interação refletirá as alterações de estado.

![Alterações no estado do contrato](./media/send-transaction/contract-state.png)

A função SendRequest define os campos **RequestMessage** e **Estado**. O estado atual para **RequestMessage** é o argumento para o qual você passou **Olá, Blockchain**. O valor do campo **Estado** permanece **Solicitação**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado no pré-requisito Início Rápido: *Criar um membro do blockchain*.

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir.
1. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um projeto do Solidity de exemplo usando o Azure Blockchain Development Kit. Você criou e implantou um contrato inteligente e, em seguida, chamou uma função por meio de uma transação em uma rede de blockchain de consórcio hospedada no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Como desenvolver aplicativos de blockchain usando o serviço Azure Blockchain](develop.md)
