---
title: Gerenciamento entre locatários na central de segurança do Azure | Microsoft Docs
description: Saiba como configurar o gerenciamento entre locatários para gerenciar a postura de segurança de vários locatários na central de segurança usando o gerenciamento de recursos delegado do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: d76ecc55c2e156199b2db789721946fd1f36de27
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922914"
---
# <a name="cross-tenant-management-in-security-center"></a>Gerenciamento entre locatários na central de segurança

O gerenciamento entre locatários permite exibir e gerenciar a postura de segurança de vários locatários na central de segurança, aproveitando o [Gerenciamento de recursos delegados do Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Gerencie vários locatários com eficiência, de uma única exibição, sem precisar entrar no diretório de cada locatário.

- Os provedores de serviços podem gerenciar a postura de segurança dos recursos, para vários clientes, de dentro de seu próprio locatário.

- As equipes de segurança de organizações com vários locatários podem exibir e gerenciar sua postura de segurança em um único local.

## <a name="set-up-cross-tenant-management"></a>Configurar o gerenciamento entre locatários

O gerenciamento de recursos delegados do Azure é um dos principais componentes do Azure Lighthouse. Configure o gerenciamento de locatários cruzados delegando acesso a recursos de locatários gerenciados para seu próprio locatário usando estas instruções da documentação do Azure Lighthouse: [Gerenciamento de recursos delegado do Azure](../lighthouse/concepts/azure-delegated-resource-management.md).


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Como o gerenciamento entre locatários funciona na central de segurança

Você pode revisar e gerenciar assinaturas em vários locatários da mesma maneira que gerencia várias assinaturas em um único locatário.

Na barra de menus superior, clique no ícone de filtro e selecione as assinaturas, no diretório de cada locatário, que você gostaria de exibir.

  ![Filtrar locatários](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

As exibições e ações são basicamente as mesmas. Estes são alguns exemplos:

- **Gerenciar políticas de segurança**: em uma exibição, gerencie a postura de segurança de muitos recursos com [políticas](tutorial-security-policy.md), execute ações com recomendações de segurança e colete e gerencie dados relacionados à segurança.
- **Aprimore a pontuação segura e a postura de conformidade**: a visibilidade entre locatários permite que você veja a postura geral de segurança de todos os seus locatários e onde e como melhorar melhor a [Pontuação segura](secure-score-security-controls.md) e a [postura de conformidade](security-center-compliance-dashboard.md) para cada um deles.
- **Corrigir recomendações**: monitore e corrija uma [recomendação](security-center-recommendations.md) para muitos recursos de vários locatários ao mesmo tempo. Você pode então lidar imediatamente com as vulnerabilidades que apresentam o maior risco em todos os locatários.
- **Gerenciar alertas**: detectar [alertas](security-center-alerts-overview.md) em vários locatários diferentes. Execute ações em recursos que estão fora de conformidade com etapas de [correção](security-center-managing-and-responding-alerts.md)acionáveis.

- **Gerencie recursos avançados de defesa de nuvem e muito mais**: Gerencie os vários serviços de proteção contra ameaças, como [acesso à VM just-in-time (JIT)](security-center-just-in-time.md), [proteção de rede adaptável](security-center-adaptive-network-hardening.md), [controles de aplicativos adaptáveis](security-center-adaptive-application.md)e muito mais.
 
## <a name="next-steps"></a>Próximas etapas
Este artigo explica como o gerenciamento entre locatários funciona na central de segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Fortaleça sua postura de segurança com a central de segurança do Azure](security-center-monitoring.md) -saiba como monitorar a integridade dos recursos do Azure.
* [Perguntas frequentes da central de segurança do Azure](faq-general.md) -encontre perguntas frequentes sobre como usar o serviço.
* [Saiba mais sobre o Azure Lighthouse em cenários empresariais](../lighthouse/concepts/enterprise.md) – Descubra como o Azure Lighthouse pode simplificar o gerenciamento entre locatários em uma empresa que usa vários locatários do Azure AD.