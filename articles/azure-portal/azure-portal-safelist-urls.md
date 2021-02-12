---
title: Faça uma lista segura das URLs do portal Azure em seu firewall ou servidor proxy
description: Adicione essas URLs ao bypass do servidor proxy para se comunicar com o portal do Azure e seus serviços
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745869"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Faça uma lista segura das URLs do portal Azure em seu firewall ou servidor proxy

Você pode configurar dispositivos de segurança locais para ignorar as restrições de segurança para as URLs de portal do Azure. Essa configuração pode melhorar o desempenho e a conectividade entre sua rede local ou de longa distância e a nuvem do Azure.

Os administradores de rede geralmente implantam servidores proxy, firewalls ou outros dispositivos. Esses dispositivos ajudam a proteger e fornecem controle sobre como os usuários acessam a Internet. As regras projetadas para proteger os usuários às vezes podem bloquear ou reduzir o tráfego de Internet legítimo relacionado aos negócios. Esse tráfego inclui comunicações entre você e o Azure. Para otimizar a conectividade entre a rede e o portal do Azure e seus serviços, recomendamos que você adicione portal do Azure URLs à sua assafe.

## <a name="azure-portal-urls-for-proxy-bypass"></a>URLs de portal do Azure para bypass de proxy

Os pontos de extremidade de URL para a lista segura para os portal do Azure são específicos para a nuvem do Azure em que sua organização está implantada. Para permitir que o tráfego de rede para esses pontos de extremidade ignore as restrições, selecione sua nuvem. Em seguida, adicione a lista de URLs ao seu servidor proxy ou firewall.

#### <a name="public-cloud"></a>[Nuvem pública](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[Nuvem do governo dos EUA](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Nuvem do governo da China](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> O tráfego para esses pontos de extremidade usa portas TCP padrão para HTTP (80) e HTTPS (443).
>
>
