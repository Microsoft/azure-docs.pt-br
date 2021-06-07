---
title: Criar um membro do Azure Blockchain Service – CLI do Azure
description: Crie um membro do Azure Blockchain Service para um consórcio Blockchain usando a CLI do Azure.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 2d94640e1c6a037893fb1ad9198b82195c46dd24
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768117"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Início Rápido: Criar um membro do blockchain do serviço Azure Blockchain usando a CLI do Azure

Neste início rápido, você implanta um novo membro do blockchain e o consórcio no Azure Blockchain Service usando a CLI do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este início rápido exigirá a CLI do Azure versão 2.0.51 ou posterior. Execute `az --version` para encontrar a versão. Caso precise instalá-la ou atualizá-la, confira [instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. Entrar.

    Entre usando o comando [az login](/cli/azure/reference-index#az_login) se estiver usando uma instalação local da CLI.

    ```azurecli
    az login
    ```

    Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

1. Instale a extensão de CLI do Azure.

    Ao trabalhar com referências de extensão para a CLI do Azure, você deve primeiro instalar a extensão.  As extensões da CLI do Azure fornecem acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte da CLI principal.  Para saber mais sobre extensões, incluindo atualização e desinstalação, confira [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

    Instale a [extensão para o Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain) executando o seguinte comando:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Crie um grupos de recursos.

    O Azure Blockchain Service, assim como todos os recursos do Azure, precisa ser implantado em um grupo de recursos. Os grupos de recursos facilitam organizar e gerenciar os recursos relacionados ao Azure.

    Para este início rápido, crie um grupo de recursos denominado _myResourceGroup_ no local _eastus_ com o seguinte comando [az group create](/cli/azure/group#az_group_create):

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Criar um membro do blockchain

Um membro do Azure Blockchain Service é um nó de blockchain em uma rede privada de blockchain de consórcio. Ao provisionar um membro, você pode criar uma rede de consórcio ou ingressar nela. Você precisará de, pelo menos, um membro para uma rede de consórcio. O número de membros de blockchain necessários para os participantes depende do cenário. Os participantes do consórcio podem ter um ou mais membros de blockchain ou podem compartilhar membros com outros participantes. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md).

Há vários parâmetros e propriedades que você precisará passar. Substitua os parâmetros de exemplo por seus valores.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parâmetro | Descrição |
|---------|-------------|
| **resource-group** | Nome do grupo de recursos no qual os recursos do serviço Azure Blockchain são criados. Use o grupo de recursos criado na seção anterior.
| **name** | Um nome exclusivo que identifica o membro do blockchain do serviço Azure Blockchain. O nome é usado para o endereço do ponto de extremidade público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **local** | Região do Azure em que o membro do blockchain é criado. Por exemplo, `westus2`. Escolha o local mais próximo para os usuários ou para outros aplicativos do Azure. Os recursos podem não estar disponíveis em algumas regiões. O Gerenciador de Dados do Azure Blockchain está disponível atualmente nas seguintes regiões do Azure: Leste dos EUA e Europa Ocidental.
| **password** | A senha do nó de transação padrão do membro. Use a senha para a autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.
| **protocol** | Protocolo do Blockchain. No momento, há suporte para o protocolo *Quorum*.
| **consortium** | Nome do consórcio a ser ingressado ou criado. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md).
| **consortium-management-account-password** | A senha da conta do consórcio também é conhecida como a senha da conta do membro. A senha da conta do membro é usada para criptografar a chave privada para a conta do Ethereum criada para o membro. Use a conta do membro e a senha da conta do membro para o gerenciamento do consórcio.
| **sku** | Tipo de camada. Selecione *Standard* ou *Basic*. Use a camada *Basic* para desenvolvimento, teste e prova de conceitos. Use a camada *Standard* para implantações de nível de produção. Use também a camada *Standard* se estiver usando o Gerenciador de Dados do Blockchain ou enviando um alto volume de transações particulares. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.

São necessários cerca de 10 minutos para criar o membro do blockchain e os recursos de suporte.

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode usar o membro do blockchain criado para o próximo Início Rápido ou tutorial. Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado para o início rápido.

Execute o comando a seguir para remover o grupo de recursos e todos os recursos relacionados.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo início rápido para usar o Azure Blockchain Development Kit for Ethereum para fazer a anexação a um membro do Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Usar o Visual Studio Code para se conectar ao Azure Blockchain Service](connect-vscode.md)
