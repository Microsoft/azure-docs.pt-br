---
title: Copiar e mover projetos de Visão Personalizada
titleSuffix: Azure Cognitive Services
description: Saiba como usar as APIs ExportProject e ImportProject para copiar e mover seus projetos de Visão Personalizada.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 7d58a8239c728f70efe3584c2649e196dffd791f
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501083"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Copiar e mover seus projetos de Visão Personalizada

Depois de criar e treinar um projeto Visão Personalizada, talvez você queira copiar o projeto para outro recurso. Por exemplo, talvez você queira mover um projeto de um ambiente de desenvolvimento para produção ou fazer backup de um projeto em uma conta em uma região diferente do Azure para aumentar a segurança dos dados.

As APIs **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** e **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** habilitam esse cenário permitindo que você copie projetos de uma conta visão personalizada para outras pessoas. Este guia mostra como usar essas APIs REST com a rotação. Você também pode usar um serviço de solicitação HTTP como o postmaster para emitir as solicitações.

## <a name="business-scenarios"></a>Cenários de negócios

Se seu aplicativo ou negócios depende do uso de um projeto Visão Personalizada, recomendamos que você copie seu modelo para outra conta de Visão Personalizada em outra região. Em seguida, se ocorrer uma interrupção regional, você poderá acessar seu projeto na região onde ele foi copiado.

##  <a name="prerequisites"></a>Pré-requisitos

- Dois recursos do Azure Visão Personalizada. Se você não os tiver, vá para a portal do Azure e [crie um novo recurso de visão personalizada](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- As chaves de treinamento e as URLs de ponto de extremidade do seu Visão Personalizada recursos. Você pode encontrar esses valores na guia **visão geral** do recurso na portal do Azure.
- Um projeto Visão Personalizada criado. Consulte [criar um classificador](./getting-started-build-a-classifier.md) para obter instruções sobre como fazer isso.
* [PowerShell versão 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)ou um utilitário de linha de comando semelhante.

## <a name="process-overview"></a>Visão geral do processo

O processo para copiar um projeto consiste nas seguintes etapas:

1. Primeiro, você obtém a ID do projeto em sua conta de origem que deseja copiar.
1. Em seguida, chame a API **ExportProject** usando a ID do projeto e a chave de treinamento da sua conta de origem. Você obterá uma cadeia de caracteres de token temporária.
1. Em seguida, chame a API **ImportProject** usando a cadeia de caracteres de token e a chave de treinamento da sua conta de destino. O projeto será então listado em sua conta de destino.

## <a name="get-the-project-id"></a>Obter a ID do projeto

Primeiro, chame **[Getprojects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** para ver uma lista de seus projetos de visão personalizada existentes e suas IDs. Use a chave de treinamento e o ponto de extremidade de sua conta de origem.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

Você receberá uma `200\OK` resposta com uma lista de projetos e seus metadados no corpo. O `"id"` valor é a cadeia de caracteres a ser copiada para as próximas etapas.

```json
[
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
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>Exportar o projeto

Chame **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** usando a ID do projeto e sua chave de treinamento de origem e o ponto de extremidade.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

Você receberá uma `200/OK` resposta com metadados sobre o projeto exportado e uma cadeia de caracteres de referência `"token"` . Copie o valor do token.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Importar o projeto

Chame **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** usando a chave de treinamento de destino e o ponto de extremidade, juntamente com o token de referência. Você também pode dar um nome ao seu projeto em sua nova conta.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

Você receberá uma `200/OK` resposta com metadados sobre seu projeto importado recentemente.

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
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a copiar e mover um projeto entre Visão Personalizada recursos. Em seguida, explore os documentos de referência da API para ver o que mais você pode fazer com Visão Personalizada.
* [Documentação de referência da API REST](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)