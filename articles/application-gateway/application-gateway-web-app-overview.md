---
title: "Visão geral de back-ends com vários locatários com o Gateway de Aplicativo do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral do suporte a vários sites do Gateway de Aplicativo para back-ends com vários locatários."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: c29ff60a50e68c75b4e8f62713d6d1fffd2123d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>O Gateway de Aplicativo dá suporte a back-ends com vários locatários

O Gateway de Aplicativo do Azure dá suporte a conjuntos de dimensionamento de máquina virtual, adaptadores de rede, IPs públicos/privados ou FQDN (nomes de domínio totalmente qualificados) como parte de seus pools de back-end. Por padrão, o gateway de aplicativo não altera o cabeçalho de host HTTP recebido do cliente e envia o cabeçalho inalterado para o back-end. Há muitos serviços, como [Aplicativos Web do Azure](../app-service/app-service-web-overview.md) e [Gerenciamento de API](../api-management/api-management-key-concepts.md), que tem vários locatários por natureza e contam com um cabeçalho de host ou extensão SNI específica para resolver para o ponto de extremidade correto. O Gateway de Aplicativo agora dá suporte aos usuários para que possam substituir o cabeçalho de host HTTP de entrada com base nas configurações HTTP de back-end. Esse recurso habilita o suporte a Aplicativos Web do Azure e ao Gerenciamento de APIs com back-ends com vários locatários. Esse recurso está disponível para as SKUs padrão e WAF. O suporte ao back-end com vários locatários funciona com terminação SSL e cenários do SSL de ponta a ponta.

![cenário de aplicativo Web](./media/application-gateway-web-app-overview/scenario.png)

A capacidade de especificar uma substituição de host é definida com as configurações de HTTP e pode ser aplicada a qualquer pool de back-end durante a criação da regra. Os back-ends com vários locatários dão suporte a estas duas maneiras de substituição de cabeçalho de host e extensão SNI.

1. A capacidade de definir o nome de host como um valor fixo nas configurações de HTTP. Esse recurso faz com que o cabeçalho de host seja substituído por esse valor para todo o tráfego em direção ao pool de back-end em que as configurações de HTTP serão aplicadas. Ao usar SSL de ponta a ponta, esse nome de host substituído é usado na extensão de SNI. Esse recurso habilita cenários onde um farm de pool de back-end espera um cabeçalho de host que seja diferente do cabeçalho de host de entrada do cliente.

2. A capacidade de derivar o nome do host do IP ou FQDN dos membros do pool de back-end. As configurações HTTP também fornecem uma opção para escolher o nome de host do FQDN de um membro do pool de back-end se configurado com a opção para derivar o nome de host de um membro do pool de back-end individual. Ao usar SSL de ponta a ponta, esse nome de host é derivado do FQDN e é usado na extensão de SNI. Esse recurso habilita cenários em que um pool de back-end pode ter dois ou mais serviços PaaS com vários locatários, como aplicativos Web do Azure, e o cabeçalho de host da solicitação para cada membro pode conter o nome de host derivado do seu FQDN.

> [!NOTE]
> Em ambos os casos anteriores, as configurações afetam somente o comportamento de tráfego ativo, não o comportamento de investigação de integridade. Os testes personalizados já dão suporte à capacidade de especificar um cabeçalho de host na configuração da investigação. Os testes personalizados agora também dão suporte à capacidade de derivar o comportamento do cabeçalho de host das configurações de HTTP configuradas no momento. Essa configuração pode ser especificada usando o parâmetro `PickHostNameFromback endAddress` na configuração da investigação. Para a funcionalidade de ponta a ponta funcionar, a investigação e as configurações de HTTP devem ser modificadas para refletir a configuração correta.

Com essa funcionalidade, os clientes especificam as opções de configuração apropriadas das investigações personalizadas e das configurações de HTTP. Essa configuração é, em seguida, vinculada a um ouvinte e a um pool de back-end por meio de uma regra.

## <a name="next-steps"></a>Próximas etapas

Saiba como configurar um gateway de aplicativo com um aplicativo Web como um membro do pool de back-end visitando: [Configurar aplicativos Web do Serviço de Aplicativos com o Gateway de Aplicativo](application-gateway-web-app-powershell.md)
