---
title: Configurar alertas de serviço para a Área de Trabalho Virtual do Windows - Azure
description: Como configurar a Integridade do Serviço do Azure para receber notificações de serviço para a Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ca5f225265b42ffc67a41a5252b2e789f7bcf500
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445628"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md).

Use a Integridade do Serviço do Azure para monitorar problemas de serviço e consultorias de integridade para a Área de Trabalho Virtual do Windows. A Integridade do Serviço do Azure pode notificá-lo com diferentes tipos de alertas (por exemplo, email ou SMS), ajudá-lo a entender o efeito de um problema e mantê-lo atualizado durante a resolução do problema. A Integridade do Serviço do Azure também ajuda a minimizar o tempo de inatividade e preparar para a manutenção planejada e para alterações que possam afetar a disponibilidade dos recursos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e configurar os alertas de serviço.

Para saber mais, veja a [documentação de Integridade do Serviço do Azure](../service-health/index.yml).

## <a name="create-service-alerts"></a>Criar alertas de serviço

Esta seção mostra como configurar a Integridade do Serviço do Azure e como configurar notificações, que você pode acessar no portal do Azure. Você pode configurar diferentes tipos de alertas e agendá-los para notificá-lo de maneira oportuna.

### <a name="recommended-service-alerts"></a>Alertas de serviço recomendados

É recomendável que você crie alertas de serviço para os seguintes tipos de evento de integridade:

- **Problema no serviço:** receba notificações sobre os principais problemas que afetam a conectividade dos seus usuários com o serviço ou com a capacidade de gerenciar seu locatário de Área de Trabalho Virtual do Windows.
- **Consultoria de integridade:** receba notificações que exigem sua atenção. Veja a seguir alguns exemplos desse tipo de notificação:
    - As Máquinas Virtuais (VMs) não estão configuradas de modo seguro com a porta aberta 3389
    - Substituição da funcionalidade

### <a name="configure-service-alerts"></a>Configurar os alertas de serviço

Para configurar os alertas de serviço:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Integridade do Serviço do Azure**.
3. Siga as instruções em [Criar alertas do log de atividades em notificações de serviço](../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) para configurar os alertas e as notificações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar e usar a Integridade do Serviço do Azure para monitorar problemas de serviço e consultorias de integridade da Área de Trabalho Virtual do Windows. Para saber mais sobre como entrar na Área de Trabalho Virtual do Windows, continue com as instruções de Conectar-se à Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Conectar-se ao cliente da Área de Trabalho Remota no Windows 7 e no Windows 10](./connect-windows-7-10.md)