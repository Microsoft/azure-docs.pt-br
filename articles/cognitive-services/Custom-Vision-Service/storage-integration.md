---
title: Integrar o armazenamento do Azure para notificações e backup de modelo
titleSuffix: Azure Cognitive Services
description: Saiba como integrar o armazenamento do Azure para receber notificações por push ao treinar ou exportar modelos de Visão Personalizada. Você também pode salvar um backup de modelos exportados.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: 2afac0e25c394c9bf265daf1fe3ded3d763a7c61
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508496"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Integrar o armazenamento do Azure para notificações e backup

Você pode integrar seu projeto de Visão Personalizada a uma fila de armazenamento de blobs do Azure para receber notificações por push de uma atividade de treinamento/exportação de projetos e cópias de backup de modelos publicados. Esse recurso é útil para evitar a sondagem contínua do serviço em busca de resultados quando operações longas estão em execução. Em vez disso, você pode integrar as notificações de fila de armazenamento ao seu fluxo de trabalho.

Este guia mostra como usar essas APIs REST com a rotação. Você também pode usar um serviço de solicitação HTTP como o postmaster para emitir as solicitações.

> [!NOTE]
> As notificações por push dependem do parâmetro opcional _notificationQueueUri_ na API **CreateProject** , e os backups de modelo exigem que você também use o parâmetro _exportModelContainerUri_ opcional. Este guia usará ambos para o conjunto completo de recursos.

## <a name="prerequisites"></a>Pré-requisitos

- Um recurso Visão Personalizada no Azure. Se você não tiver uma, vá para a portal do Azure e [crie um novo recurso de visão personalizada](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). No momento, esse recurso não dá suporte ao recurso de serviço cognitiva (tudo em uma única chave).
- Uma conta de armazenamento do Azure com um contêiner de BLOB. Siga [os exercícios 1 do laboratório de armazenamento do Azure](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) se precisar de ajuda com esta etapa.
* [PowerShell versão 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)ou um aplicativo de linha de comando semelhante.

## <a name="set-up-azure-storage-integration"></a>Configurar a integração do armazenamento do Azure

Vá para o recurso de treinamento de Visão Personalizada no portal do Azure, selecione a página **identidade** e habilite a identidade gerenciada atribuída pelo sistema.

Em seguida, vá para o recurso de armazenamento no portal do Azure. Vá para a página **controle de acesso (iam)** e adicione uma atribuição de função para cada recurso de integração:
* Selecione o recurso de treinamento de Visão Personalizada e atribua a função de **colaborador de dados de blob de armazenamento** se você planeja usar o recurso de backup de modelo. 
* Em seguida, selecione o recurso de treinamento Visão Personalizada e atribua o **colaborador de dados da fila de armazenamento** se você planeja usar o recurso fila de notificação.

> [!div class="mx-imgBorder"]
> ![Página de atribuição de função de adição de conta de armazenamento](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Obter URLs de integração

Em seguida, você obterá as URLs que permitem que o recurso de Visão Personalizada Acesse esses pontos de extremidade.

Para a URL de integração da fila de notificação, vá para a página **filas** da sua conta de armazenamento, adicione uma nova fila e salve sua URL em um local temporário.

> [!div class="mx-imgBorder"]
> ![Página fila do armazenamento do Azure](./media/storage-integration/queue-url.png) 

Para a URL de integração de backup do modelo, vá para a página **contêineres** da sua conta de armazenamento e crie um novo contêiner. Em seguida, selecione-o e vá para a página **Propriedades** . Copie a URL para um local temporário.
 
> [!div class="mx-imgBorder"]
> ![Página de propriedades do contêiner de armazenamento do Azure](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Integrar Visão Personalizada projeto

Agora que você tem as URLs de integração, você pode criar um novo projeto de Visão Personalizada que integre os recursos de armazenamento do Azure. Você também pode atualizar um projeto existente para adicionar os recursos.

### <a name="create-new-project"></a>Criar um novo projeto

Ao chamar a API [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) , adicione os parâmetros opcionais _exportModelContainerUri_ e _notificationQueueUri_. Atribua os valores de URL que você obteve na seção anterior. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Se você receber uma `200/OK` resposta, isso significará que as URLs foram configuradas com êxito. Você também deve ver os valores de URL na resposta JSON:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Atualizar projeto existente

Para atualizar um projeto existente com a integração de recursos do armazenamento do Azure, chame a API [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) , usando a ID do projeto que você deseja atualizar. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Defina o corpo da solicitação ( `body` ) para o seguinte formato JSON, preenchendo os valores apropriados para _ExportModelContainerUri_ e _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Se você receber uma `200/OK` resposta, isso significará que as URLs foram configuradas com êxito.

## <a name="verify-the-connection"></a>Verificar conexão 

Sua chamada à API na seção anterior já deve ter disparado novas informações em sua conta de armazenamento do Azure. 

No seu contêiner designado, deve haver um blob de teste dentro de uma pasta **CustomVision-TestPermission** . Esse blob só existirá temporariamente.

Na fila de notificação, você deve ver uma notificação de teste no seguinte formato:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Obter notificações de eventos

Quando estiver pronto, chame a API do [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) em seu projeto para fazer uma operação de treinamento comum.

Em sua fila de notificação de armazenamento, você receberá uma notificação após a conclusão do treinamento:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

O `"trainingStatus"` campo pode ser `"TrainingCompleted"` ou `"TrainingFailed"` . O `"iterationId"` campo é a ID do modelo treinado.

## <a name="get-model-export-backups"></a>Obter backups de exportação de modelo

Quando estiver pronto, chame a API [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) para exportar um modelo treinado para uma plataforma especificada.

No seu contêiner de armazenamento designado, uma cópia de backup do modelo exportado será exibida. O nome do blob terá o formato:

```
{projectId} - {iterationId}.{platformType}
```

Além disso, você receberá uma notificação em sua fila quando a exportação for concluída. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

O `"exportStatus"` campo pode ser `"ExportCompleted"` ou `"ExportFailed"` . O `"modelUri"` campo conterá a URL do modelo de backup armazenado em seu contêiner, supondo que você tenha integrado notificações de fila no início. Se você não tiver, o `"modelUri"` campo mostrará a URL SAS para seu blob de modelo de visão personalizada.

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a copiar e mover um projeto entre Visão Personalizada recursos. Em seguida, explore os documentos de referência da API para ver o que mais você pode fazer com Visão Personalizada.
* [Documentação de referência da API REST (treinamento)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [Documentação de referência da API REST (previsão)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)
