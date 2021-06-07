---
title: Criar um Azure Data Factory usando um modelo do ARM (Azure Resource Manager)
description: Crie um exemplo de pipeline do Azure Data Factory usando um modelo do ARM (Azure Resource Manager).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: c579b6d723533e751e08a80a578195c03e945607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783329"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Início Rápido: criar um Azure Data Factory usando modelo do ARM

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versão atual](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este início rápido descreve como usar um modelo do ARM (Azure Resource Manager) para criar um data factory do Azure. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, confira [Tutorial: Transformar dados usando o Spark](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Assinatura do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="create-a-file"></a>Criar um arquivo

Abra um editor de texto como o **Bloco de notas** e crie um arquivo chamado **emp.txt** com o seguinte conteúdo:

```emp.txt
John, Doe
Jane, Doe
```

Salve o arquivo na pasta **C:\ADFv2QuickStartPSH**. (Se a pasta ainda não existir, crie-a.)

## <a name="review-template"></a>Examinar modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Há recursos do Azure definidos no modelo:

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): define uma conta de armazenamento.
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories): cria um Azure Data Factory.
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): cria um serviço vinculado do Azure Data Factory.
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets): cria um conjunto de dados do Azure Data Factory.
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines): configura um pipeline do Azure Data Factory.

Mais exemplos de modelos do Azure Data Factory podem ser encontrados na [galeria de modelos de início rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir um modelo. O modelo cria uma conta do Azure Data Factory, uma conta de armazenamento e um contêiner de blob.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Implantar modelo do ARM para ADF":::

    A menos que seja especificado, use os valores padrão para criar os recursos do Azure Data Factory:

    - **Assinatura**: Selecione uma assinatura do Azure.
    - **Grupo de recursos**: selecione **Criar**, insira um nome exclusivo para o grupo de recursos e selecione **OK**.
    - **Região**: Selecione um local.  Por exemplo, *Leste dos EUA*.
    - **Nome do Data Factory**: use o valor padrão.
    - **Localização**: use o valor padrão.
    - **Nome da Conta de Armazenamento**: use o valor padrão.
    - **Contêiner de blob**: use o valor padrão.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Selecione **Ir para grupo de recursos**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Grupo de recursos":::

2.  Verifique se o Azure Data Factory é criado.
    1. O nome do Azure Data Factory está no formato – datafactory\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Exemplo de Data Factory":::

2. Verifique se a conta de armazenamento foi criada.
    1. O nome da conta de armazenamento está no formato – storage\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Conta de armazenamento":::

3. Escolha a conta de armazenamento criada e selecione **Contêineres**.
    1. Na página **Contêineres**, selecione o contêiner de blob que você criou.
        1. O nome do contêiner de blob está no formato – blob\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Contêiner de blob":::

### <a name="upload-a-file"></a>Carregar um arquivo

1. Na página **Contêineres**, selecione **Carregar**.

2. No painel direito, selecione a caixa **Arquivos**, navegue até o arquivo **emp.txt** que você criou anteriormente e selecione-o.

3. Expanda o título **Avançado**.

4. Na caixa **Carregar para a pasta**, insira *entrada*.

5. Selecione o botão **Carregar**. O arquivo **emp.txt** e o status do carregamento devem estar na lista.

6. Selecione o ícone **Fechar** (um **X**) para fechar a página **Carregar blob**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Carregar arquivo na pasta de entrada":::

Mantenha a página de contêiner aberta, pois você poderá utilizá-la para verificar a saída no final deste guia de início rápido.

### <a name="start-trigger"></a>Iniciar gatilho

1. Navegue até a página **Data factories** e selecione o data factory que você criou. 

2. Selecione o bloco **Criar e Monitorar**. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Criar e Monitorar":::

2. Selecione a guia **Criar** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false":::.

3. Selecione o pipeline criado – ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="Pipeline de modelo do ARM":::

4. Selecione **Adicionar Gatilho** > **Disparar Agora**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Gatilho":::

5. No painel direito, em **Execução do pipeline**, selecione **OK**.

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline

1. Selecione a guia **Monitorar** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::.

2. Você vê as execuções de atividade associadas à execução do pipeline. Neste início rápido, o pipeline tem apenas uma atividade do tipo: Cópia. Assim, você vê uma execução para essa atividade.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Execução bem-sucedida":::

### <a name="verify-the-output-file"></a>Verificar o arquivo de saída

O pipeline cria automaticamente a pasta de saída no contêiner de blob. Em seguida, ele copia o arquivo emp.txt da pasta de entrada para a pasta de saída. 

1. No portal do Azure, na página **Contêineres**, selecione **Atualizar** para ver a pasta de saída. 

2. Selecione **saída** na lista de pastas.

3. Confirme que **emp.txt** tenha sido copiado para a pasta de saída. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Saída":::

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos criados no Guia de início rápido de duas maneiras. Você pode [excluir o grupo de recursos do Azure](../azure-resource-manager/management/delete-resource-group.md), o que inclui todos os recursos do grupo. Se desejar manter os outros recursos intactos, exclua apenas o data factory que você criou neste tutorial.

Ao excluir um grupo de recursos, todos os recursos são excluídos, incluindo os data factories nele. Execute o comando a seguir para excluir o grupo de recursos inteiro: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Se quiser excluir apenas o data factory, e não o grupo de recursos inteiro, execute o seguinte comando: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Azure Data Factory usando um modelo do ARM e validou a implantação. Para saber mais sobre o Azure Data Factory e o Azure Resource Manager, continue com os artigos abaixo.

- [Documentação do Azure Data Factory](index.yml)
- Saiba mais sobre o [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Obter outros [modelos do ARM para Azure Data Factory](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)