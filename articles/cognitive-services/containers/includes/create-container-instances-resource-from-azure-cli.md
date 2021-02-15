---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure no CLI do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 87007d3df3fe44ab04a330b09b8e495ec4b47e54
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865997"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Criar um recurso de instância de contêiner do Azure no CLI do Azure

O YAML abaixo define o recurso de instância de contêiner do Azure. Copie e cole o conteúdo em um novo arquivo, chamado `my-aci.yaml` e substitua os valores comentados pelos seus próprios. Consulte o [formato de modelo][template-format] para YAML válido. Consulte os [repositórios e as imagens do contêiner][repositories-and-images] para obter os nomes de imagem disponíveis e seu repositório correspondente. Para obter mais informações sobre a referência de YAML para instâncias de contêiner, consulte [referência do YAML: instâncias de contêiner do Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
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
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
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
> Nem todos os locais têm a mesma disponibilidade de CPU e memória. Consulte a tabela [local e recursos][location-to-resource] para obter a lista de recursos disponíveis para contêineres por local e so.

Vamos contar com o arquivo YAML que criamos para o [`az container create`][azure-container-create] comando. No CLI do Azure, execute o `az container create` comando substituindo o `<resource-group>` pelo seu próprio. Além disso, para proteger valores em uma implantação do YAML, consulte [valores seguros][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A saída do comando é `Running...` se válida, após algum tempo, a saída é alterada para uma cadeia de caracteres JSON que representa o recurso ACI recém-criado. A imagem de contêiner está mais do que provavelmente não está disponível por um tempo, mas o recurso agora está implantado.

> [!TIP]
> Preste muita atenção aos locais de ofertas de serviço cognitiva do Azure de visualização pública, pois o YAML precisará ser ajustado adequadamente para corresponder ao local.

[azure-container-create]: /cli/azure/container#az-container-create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
