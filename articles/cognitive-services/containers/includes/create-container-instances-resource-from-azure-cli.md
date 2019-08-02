---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure da CLI do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717108"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Criar um recurso de instância de contêiner do Azure da CLI do Azure

O YAML a seguir define o recurso de instância de contêiner do Azure. Copie e cole o conteúdo em um novo arquivo, chamado `my-aci.yaml` e substitua os valores comentados com seus próprios. Consulte o [formato de modelo] [modelo Formatar] para YAML válido. Consulte a [contêiner repositórios e imagens][repositories-and-images] para os nomes de imagem disponível e seu repositório correspondente.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nem todos os locais têm a mesma disponibilidade de CPU e memória. Consulte a [local e recursos][location-to-resource] tabela para obter uma lista de recursos disponíveis para contêineres por local e o sistema operacional.

Vamos utilizar o arquivo YAML que criamos para o [ `az container create` ][azure-container-create] comando. Na CLI do Azure, execute as `az container create` comando substituindo o `<resource-group>` pelos seus próprios. Além disso, para proteger os valores dentro de um YAML implantação consultem [proteger valores][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A saída do comando é `Running...` se válido, após algum tempo a saída será alterado para uma cadeia de caracteres JSON que representa o recurso ACI recém-criado. A imagem de contêiner é mais provável que não de estar disponível por algum tempo, mas o recurso está implantado.

> [!TIP]
> Preste muita atenção aos locais das ofertas de serviços Cognitivos do Azure de visualização pública, como o YAML precisava ser ajustados adequadamente para corresponder ao local.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values