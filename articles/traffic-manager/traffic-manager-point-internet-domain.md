---
title: Apontar um domínio de Internet para o Gerenciador de tráfego-Gerenciador de tráfego do Azure
description: Este artigo ajudará a indicar o nome de domínio de sua empresa para um nome de domínio do Gerenciador de Tráfego.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: e0e2acfb0ec0068dcd08ae660e397f65e039a665
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183737"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Apontar um domínio de Internet da empresa para um domínio do Gerenciador de Tráfego do Azure

Quando você cria um perfil do Gerenciador de tráfego, o Azure atribui automaticamente um nome DNS ao perfil. Para usar um nome de sua zona DNS, crie um registro DNS CNAME que mapeia para o nome de domínio de seu perfil do Gerenciador de Tráfego. Você pode encontrar o nome do domínio do Gerenciador de Tráfego na seção **Geral** da página Configuração do perfil do Gerenciador de Tráfego.

Por exemplo, para o nome do ponto `www.contoso.com` para o nome DNS do Gerenciador de Tráfego `contoso.trafficmanager.net`, você cria o seguinte registro de recurso DNS:

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

Todas as solicitações de tráfego para *www \. contoso.com* são direcionadas para *contoso.trafficmanager.net*.

> [!IMPORTANT]
> Não é possível indicar um domínio de segundo nível, como *contoso.com*, para o domínio do Gerenciador de Tráfego. Os padrões de protocolo DNS não permitem registros CNAME para nomes de domínio de segundo nível.

## <a name="next-steps"></a>Próximas etapas

* [Métodos de roteamento do Gerenciador de Tráfego](traffic-manager-routing-methods.md)
* [Gerenciador de Tráfego - Desabilitar, habilitar ou excluir um perfil](./traffic-manager-manage-profiles.md)
* [Gerenciador de Tráfego - Desabilitar ou habilitar um ponto de extremidade](./traffic-manager-manage-endpoints.md)