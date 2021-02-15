---
title: Usar marcas de serviço
titleSuffix: Azure SignalR Service
description: Usar marcas de serviço para permitir o tráfego de saída para o serviço de Signaler do Azure
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152317"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Usar marcas de serviço para o serviço de Signaler do Azure

Você pode usar [marcas de serviço](../virtual-network/network-security-groups-overview.md#service-tags) para o serviço de Signaler do Azure ao configurar o [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md#network-security-groups). Ele permite que você defina a regra de segurança de rede de saída para pontos de extremidade do serviço de Signaler do Azure sem precisar codificar endereços IP.

O serviço de Signaler do Azure gerencia essas marcas de serviço. Você não pode criar sua própria marca de serviço nem modificar uma existente. A Microsoft gerencia esses prefixos de endereço que correspondem à marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

## <a name="use-service-tag-on-portal"></a>Usar a marca de serviço no portal

Você pode permitir o tráfego de saída para o serviço de Signaler do Azure adicionando uma nova regra de segurança de rede de saída:

1. Vá para o grupo de segurança de rede.

1. Clique no menu configurações chamado **regras de segurança de saída**.

1. Clique no botão **+ Adicionar** na parte superior.

1. Escolha a **marca de serviço** em **destino**.

1. Escolha **AzureSignalR** em **marca de serviço de destino**.

1. Preencha **443** nos **intervalos de portas de destino**.

    ![Criar uma regra de segurança de saída](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Ajuste outros campos de acordo com suas necessidades.

1. Clique em **Adicionar**.


## <a name="next-steps"></a>Próximas etapas

- [Grupos de segurança de rede: marcas de serviço](../virtual-network/network-security-groups-overview.md#security-rules)