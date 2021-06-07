---
title: Compartilhamento fora da sua organização (modelo do ARM) – Guia de início rápido do Azure Data Share
description: Saiba como compartilhar dados com clientes e parceiros usando o Azure Data Share e um modelo do Azure Resource Manager (modelo do ARM) neste guia de início rápido.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 5abe92120c8b822ac86ced90658869a0858d4ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487680"
---
# <a name="quickstart-share-data-using-azure-data-share-and-arm-template"></a>Início Rápido: Compartilhar dados usando o Azure Data Share e um modelo do ARM

Saiba como configurar um novo Azure Data Share de uma conta de armazenamento do Azure usando um modelo do Azure Resource Manager (modelo do ARM). E comece a compartilhar seus dados com clientes e parceiros fora da sua organização do Azure. Para obter uma lista dos armazenamentos de dados com suporte, confira [Armazenamentos de dados com suporte no Azure Data Share](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Os seguintes recursos do Azure estão definidos no modelo:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.DataShare/accounts](/azure/templates/microsoft.datashare/accounts)
* [Microsoft.DataShare/accounts/shares](/azure/templates/microsoft.datashare/accounts/shares)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts/shares/dataSets](/azure/templates/microsoft.datashare/accounts/shares/datasets)
* [Microsoft.DataShare/accounts/shares/invitations](/azure/templates/microsoft.datashare/accounts/shares/invitations)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/azure/templates/microsoft.datashare/accounts/shares/synchronizationsettings)

O modelo realiza as seguintes tarefas:

* Criar uma conta de armazenamento e um contêiner usado como a fonte de dados do Compartilhamento de dados.
* Criar uma conta do Data Share e um Compartilhamento de dados.
* Crie uma atribuição de função para conceder a função de Leitor de Dados de Blob de Armazenamento ao recurso de compartilhamento de dados de origem. Confira [Funções e requisitos do Azure Data Share](./concepts-roles-permissions.md).
* Adicionar um conjunto de dados ao Compartilhamento de dados.
* Adicionar destinatários ao Compartilhamento de dados.
* Habilitar um agendamento de instantâneo para o Compartilhamento de dados.

Este modelo é criado para fins de aprendizado. Na prática, normalmente você tem alguns dados em uma conta de armazenamento existente. Você precisaria criar a atribuição de função antes de executar um modelo ou um script para criar o conjunto de dados. Às vezes, você pode receber a seguinte mensagem de erro ao implantar o modelo:

```plaintext
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Isso ocorre porque a implantação está tentando criar o conjunto de dados antes que a atribuição de função do Azure seja finalizada. Apesar da mensagem de erro, a implantação pode ser bem-sucedida. Você ainda poderá percorrer [Examinar recursos implantados](#review-deployed-resources).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Selecione ou insira os valores a seguir:

    * **Assinatura**: selecione uma assinatura do Azure usada para criar o compartilhamento de dados e os outros recursos.
    * **Grupo de recursos**: selecione **Criar** para criar um grupo de recursos ou selecione um existente.
    * **Local**: selecione um local para o grupo de recursos.
    * **Nome do projeto**: insira um nome para o projeto.  O nome do projeto é usado para gerar nomes de recursos.  Consulte as definições de variáveis no modelo anterior.
    * **local**: selecione um local para os recursos.  Você pode usar o mesmo local do grupo de recursos.
    * **Email de convite**: insira o endereço de email de logon do Azure do destinatário do compartilhamento de dados.  O alias de email não funciona.

    Use o valor padrão para o restante das configurações.
1. Selecione **Concordo com os termos e condições declarados acima** e selecione **Comprar**.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).
1. Abra a conta do compartilhamento de dados que você criou.
1. No menu esquerdo, selecione **Enviar compartilhamentos**.  Você verá a conta de armazenamento listada.
1. Selecione a conta de armazenamento.  Em **Detalhes**, você verá a configuração de sincronização conforme definida no modelo.

    ![Configurações de sincronização da Conta de Armazenamento do Azure Data Share](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Selecione **Convites** na parte superior. Você verá o endereço de email que especificou ao implantar o modelo. O **Status** deverá ser **Pendente**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos, que excluirá os recursos no grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar um Azure Data Share e a convidar destinatários. Para saber mais sobre como um consumidor de dados pode aceitar e receber um compartilhamento de dados, prossiga para o tutorial [aceitar e receber dados](subscribe-to-data-share.md).
