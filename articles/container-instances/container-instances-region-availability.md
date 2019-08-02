---
title: Disponibilidade de recursos das Instâncias de Contêiner do Azure
description: Disponibilidade de recursos de computação e de memória para o serviço de Instâncias de Contêiner do Azure em diferentes regiões do Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 14e7b9a3ea11e59aabeb901c4039e69208ea0cfd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325715"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilidade de recursos para Instâncias de Contêiner do Azure nas regiões do Azure

Este artigo fornece detalhes sobre a disponibilidade de recursos de computação e de memória de Instâncias de Contêiner do Azure em regiões do Azure. 

Os valores apresentados são o máximo de recursos disponíveis para a implantação de um [grupo de contêineres](container-instances-container-groups.md). Os valores estavam atualizados no momento da publicação. 

> [!NOTE]
> Os grupos de contêiner criados dentro desses limites de recursos estão sujeitas à disponibilidade dentro da região de implantação. Quando uma região está sob carga pesada, você pode enfrentar uma falha durante a implantação de instâncias. Para atenuar essa falha de implantação, tente implantar instâncias com configurações de recursos inferiores ou tente implantar em um momento posterior.

Para obter informações sobre cotas e outros limites em suas implantações, confira [cotas e limites para as Instâncias de Contêiner do Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilidade – geral

As regiões e os recursos a seguir estão disponíveis para grupos de contêineres com o Linux e contêineres baseados em Windows Server 2016 com [suporte](container-instances-faq.md#what-windows-base-os-images-are-supported).

| Location | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Ásia Oriental, Canadá Central, Centro-Sul dos EUA, EUA Central, Europa Setentrional, Índia Central, Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Sudeste Asiático, Sul do Reino Unido | Linux | 4 | 16 |
| Oeste da Europa, Oeste dos EUA 2 | Linux | 4 | 14 |
| Leste da Austrália, Leste do Japão | Linux | 2 | 8 |
| Centro-Norte dos EUA, Sul da Índia | Linux | 2 | 3,5 |
| Europa Ocidental | Windows | 4 | 16 |
| Leste dos EUA, Oeste dos EUA | Windows | 4 | 14 |
| Ásia Oriental, Canadá Central, Centro-Norte dos EUA, Centro-Sul dos EUA, EUA Central, Europa Setentrional, Índia Central, Leste da Austrália, Leste do Japão, Leste dos EUA 2, Oeste dos EUA 2, Sudeste Asiático, Sul da Índia, Sul do Reino Unido | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilidade – Implantações do Windows Server 2019 LTSC, 1809 (versão prévia)

As regiões e os recursos a seguir estão disponíveis para grupos de contêineres baseados em Windows Server 2019 (versão prévia).

| Location | SO | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Centro dos EUA, Europa Setentrional, Leste dos EUA, Oeste da Europa, Oeste dos EUA, Oeste dos EUA 2, Sudeste Asiático | Windows | 4 | 16 |
| Leste dos EUA 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Disponibilidade – implantação da rede virtual (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantado em uma [rede virtual do Azure](container-instances-vnet.md) (versão prévia).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilidade – recursos de GPU (versão prévia)

As seguintes regiões e recursos estão disponíveis para um grupo de contêineres implantados com [recursos de GPU](container-instances-gpu.md) (versão prévia).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Próximas etapas

Informe à equipe se você gostaria de ver outras regiões ou uma maior disponibilidade de recursos em [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Para obter informações sobre como solucionar problemas de implantação de instâncias de contêiner, confira [Solucionar problemas de implantação com Instâncias de Contêiner do Azure](container-instances-troubleshooting.md).
