---
title: Conceitos-gerenciamento de API
description: Saiba como o gerenciamento de API protege as APIs em execução nas VMs (máquinas virtuais) da solução VMware do Azure
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541956"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Gerenciamento de API para publicar e proteger APIs em execução em VMs baseadas em soluções VMware do Azure

Microsoft Azure [Gerenciamento de API](https://azure.microsoft.com/services/api-management/) permite que você publique com segurança em consumidores internos ou externos.  Somente os SKUs do desenvolvedor e Premium permitem a integração da rede virtual do Azure para publicar APIs em execução em cargas de trabalho da solução VMware do Azure.  Ambos os SKUs habilitam com segurança a conectividade entre o serviço de gerenciamento de API e o back-end. 

>[!NOTE]
>A SKU do desenvolvedor destina-se ao desenvolvimento e ao teste enquanto o SKU Premium é para implantações de produção.

A configuração de gerenciamento de API é a mesma para serviços de back-end que são executados sobre VMs (máquinas virtuais) da solução do Azure VMware e locais. Para ambas as implantações, o gerenciamento de API configura o VIP (IP virtual) no balanceador de carga como o ponto de extremidade de back-end quando o servidor back-end é colocado atrás de um Load Balancer NSX na solução Azure VMware. 


## <a name="external-deployment"></a>Implantação externa

Uma implantação externa publica APIs consumidas por usuários externos usando um ponto de extremidade público. Desenvolvedores e engenheiros de DevOps podem gerenciar APIs por meio do portal do Azure ou do PowerShell e do portal do desenvolvedor de gerenciamento de API.

O diagrama de implantação externa mostra todo o processo e os atores envolvidos (mostrados na parte superior). Os atores são:

- **Administrador (es):** Representa a equipe administrador ou DevOps, que gerencia a solução Azure VMware por meio dos mecanismos de portal do Azure e automação, como PowerShell ou Azure DevOps.

- **Usuários:**  Representa os consumidores das APIs expostas e representa os usuários e os serviços que consomem as APIs.

O fluxo de tráfego passa pela instância de gerenciamento de API, que abstrai os serviços de back-end, conectados à rede virtual do Hub. O gateway de ExpressRoute roteia o tráfego para o canal de Alcance Global do ExpressRoute e atinge um NSX Load Balancer distribuindo o tráfego de entrada para as diferentes instâncias de serviços de back-end.

O gerenciamento de API tem uma API pública do Azure e é recomendável ativar o serviço de proteção contra DDOS do Azure. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Implantação externa – gerenciamento de API para a solução do Azure VMware":::


## <a name="internal-deployment"></a>Implantação interna

Uma implantação interna publica APIs consumidas por usuários ou sistemas internos. A equipe DevOps e os desenvolvedores de API usam as mesmas ferramentas de gerenciamento e portal do desenvolvedor que a implantação externa.

As implantações internas podem ser feitas [com aplicativo Azure gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) para criar um ponto de extremidade público e seguro para a API.  Os recursos do gateway são usados para criar uma implantação híbrida que permite cenários diferentes.  

* Use o mesmo recurso de gerenciamento de API para consumo por consumidores internos e externos.

* Ter um único recurso de gerenciamento de API com um subconjunto de APIs definido e disponível para consumidores externos.

* Forneça uma maneira fácil de ativar e desativar o acesso ao gerenciamento de API a partir da Internet pública.

O diagrama de implantação abaixo mostra os consumidores que podem ser internos ou externos, com cada tipo acessando as mesmas ou diferentes APIs.

Em uma implantação interna, as APIs são expostas para a mesma instância de gerenciamento de API. Na frente do gerenciamento de API, o gateway de aplicativo é implantado com o recurso de firewall do aplicativo Web do Azure (WAF) ativado. Também implantado, um conjunto de ouvintes HTTP e regras para filtrar o tráfego, expondo apenas um subconjunto dos serviços de back-end em execução na solução VMware do Azure.


* O tráfego interno roteia o gateway de ExpressRoute para o Firewall do Azure e, em seguida, para o gerenciamento de API, diretamente ou por meio de regras de tráfego   

* O tráfego externo entra no Azure por meio do gateway de aplicativo, que usa a camada de proteção externa para o gerenciamento de API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Implantação interna – gerenciamento de API para a solução do Azure VMware" lightbox="media/api-management/internal-deployment.png":::