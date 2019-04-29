---
title: Montar um volume emptyDir em Instâncias de Contêiner do Azure
description: Saiba como montar um volume emptyDir para compartilhar dados entre os contêineres em um grupo de contêineres em Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 98a72123a05fa7d8dc16be7ddb787f2a2cf7e4d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563113"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montar um volume emptyDir em Instâncias de Contêiner do Azure

Saiba como montar um volume *emptyDir* para compartilhar dados entre os contêineres em um grupo de contêineres em Instâncias de Contêiner do Azure.

> [!NOTE]
> A montagem de um volume *emptyDir* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças atuais de plataforma em [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-quotas.md).

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