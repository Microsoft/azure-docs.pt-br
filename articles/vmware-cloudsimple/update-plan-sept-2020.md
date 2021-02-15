---
title: Solução Azure VMware de CloudSimple de setembro de 2020 atualização
description: Neste artigo, saiba mais sobre o que esperar durante essa operação de manutenção e as alterações em sua nuvem privada.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: ae9c1ba5259e95ed030d7099e5dafe2d4f7935b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447760"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Solução Azure VMware de CloudSimple de setembro de 2020 atualização

Uma atualização importante para o serviço de solução do Azure VMware será executada em setembro. Uma notificação por email, enviada como parte da manutenção, incluirá a linha do tempo da manutenção. Neste artigo, você aprenderá o que esperar durante essa operação de manutenção e as alterações em sua nuvem privada.

> [!NOTE]
> Esta é uma atualização sem interrupções. Durante a atualização, você pode ver que um dos componentes redundantes fica inativo.

## <a name="vmware-infrastructure-upgrade"></a>Atualização de infraestrutura do VMware

A infraestrutura do VMware de sua nuvem privada será atualizada para uma versão mais recente. Isso inclui atualizações para os componentes vCenter, ESXi, NSX-T e HCX (extensão de nuvem híbrida) (se implantadas) da sua nuvem privada.

Durante a atualização, um novo nó será adicionado à sua nuvem privada antes que um nó seja colocado no modo de manutenção para a operação de atualização. Isso garante que a capacidade da sua nuvem privada e da disponibilidade da sua nuvem privada seja mantida durante o processo de atualização. Durante a atualização dos componentes do VMware, você poderá ver os alarmes exibidos em sua interface do usuário da Web do vCenter. Os alarmes fazem parte das operações de manutenção executadas pela equipe de operações de serviço.

**Versão do componente**

- 6.7 ESXi
- vCenter 6.7 U3
- vSAN 6,7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Atualizações do datacenter

Essa atualização inclui atualizações para a infraestrutura do datacenter. As atualizações sem interrupção serão executadas durante o período de manutenção. Você notará menos redundância durante o processo de atualização. Os alertas podem ser gerados em sua infraestrutura do VMware de nuvem privada, seus circuitos do ExpressRoute, conexões GlobalReach e quaisquer dispositivos VPN site a site relacionados a um dos links Availabilities. Isso é normal durante a atualização, pois os componentes serão reinicializados como parte da atualização.

-   Se uma VPN site a site for implantada como uma única instância (não HA), talvez seja necessário restabelecer a conexão VPN.

-   Se você usar uma conexão VPN ponto a site, precisará restabelecer a conexão VPN.

## <a name="post-update"></a>Pós-atualização

Depois que as atualizações forem concluídas, você deverá ver as versões mais recentes dos componentes do VMware. Se você perceber algum problema ou tiver dúvidas, entre em contato com nossa equipe de suporte abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
