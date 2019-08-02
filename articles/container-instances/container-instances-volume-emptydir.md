---
title: Montar um volume emptyDir em Instâncias de Contêiner do Azure
description: Saiba como montar um volume emptyDir para compartilhar dados entre os contêineres em um grupo de contêineres em Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 0dbe26ff1e00e1912cfd63e8383695ca794dd037
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325458"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montar um volume emptyDir em Instâncias de Contêiner do Azure

Saiba como montar um volume *emptyDir* para compartilhar dados entre os contêineres em um grupo de contêineres em Instâncias de Contêiner do Azure.

> [!NOTE]
> A montagem de um volume *emptyDir* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>Volume emptyDir

O volume *emptyDir* fornece um diretório gravável acessível para cada contêiner em um grupo de contêineres. Os contêineres no grupo podem ler e gravar os mesmos arquivos no volume, e ele pode ser montado usando-se os mesmos caminhos ou caminhos diferentes em cada contêiner.

Alguns exemplos usam para um volume *emptyDir*:

* Espaço transitório
* Ponto de verificação durante tarefas de longa execução
* Armazenar dados recuperados por um contêiner secundário e fornecidos por um contêiner de aplicativos

Os dados em um volume *emptyDir* são mantidos mesmo após falhas de contêiner. Os contêineres reiniciados, entretanto, não têm a garantia de manter os dados em um volume *emptyDir*.

## <a name="mount-an-emptydir-volume"></a>Montar um volume emptyDir

Para montar um volume emptyDir em uma instância de contêiner, você deve implantar usando um [Modelo do Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, popule a matriz `volumes` na seção `properties` do grupo de contêineres do modelo. Em seguida, para cada contêiner do grupo de contêineres no qual você deseja montar o volume *emptyDir*, popule a matriz `volumeMounts` na seção `properties` da definição de contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em dois contêineres, cada um montando o volume *emptyDir*:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver um exemplo de implantação de instância de contêiner com um modelo do Azure Resource Manager, consulte [Implantar grupos com vários contêineres em Instâncias de Contêiner do Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como montar outros tipos de volume em Instâncias de Contêiner do Azure:

* [Montar um compartilhamento de arquivos do Azure em Instâncias de Contêiner do Azure](container-instances-volume-azure-files.md)
* [Montar um volume gitRepo em Instâncias de Contêiner do Azure](container-instances-volume-gitrepo.md)
* [Montar um volume secreto em Instâncias de Contêiner do Azure](container-instances-volume-secret.md)