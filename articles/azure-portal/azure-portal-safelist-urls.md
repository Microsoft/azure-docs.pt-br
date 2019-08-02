---
title: O portal do Azure de lista segura URLs | Microsoft Docs
description: Adicione essas URLs para o desvio do servidor proxy para se comunicar com o portal do Azure e seus serviços
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304967"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>O portal do Azure de lista segura URLs em seu servidor proxy ou firewall

Para o bom desempenho e conectividade entre sua rede local ou remota e a nuvem do Azure, configurar os dispositivos de segurança local para ignorar as restrições de segurança para o portal do Azure URLs. Geralmente, os administradores de rede implantam servidores proxy, firewalls ou outros dispositivos para ajudar a proteger e oferecer controle sobre como os usuários acessam a internet. No entanto, regras projetadas para proteger os usuários às vezes, podem bloquear ou reduzir a velocidade legítimos tráfego de internet relacionados aos negócios, incluindo comunicações entre você e o Azure. Para otimizar a conectividade entre sua rede e o portal do Azure e seus serviços, é recomendável que você adicione o portal do Azure URLs para sua lista segura.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Bypass de URLs para o proxy do portal do Azure

Adicione a seguinte lista de URLs para o seu servidor proxy ou firewall para permitir o tráfego de rede para esses pontos de extremidade para ignorar restrições:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *. portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.wpc.azureedge.net

> [!NOTE]
> O tráfego para esses pontos de extremidade usa portas TCP padrão para HTTP (80) e HTTPS (443).
>
>
## <a name="next-steps"></a>Próximas etapas

* Precisa de endereços IP de lista segura? Baixe a lista de [intervalos de IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* Outro serviços uso Microsoft URLs adicionais e endereços IP para conectividade. Para otimizar a conectividade de rede para serviços do Microsoft 365, consulte [configurar sua rede para o Office 365](/office365/enterprise/set-up-network-for-office-365).
