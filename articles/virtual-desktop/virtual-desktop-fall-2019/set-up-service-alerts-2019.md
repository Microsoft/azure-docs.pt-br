---
title: Configurar alertas de serviço para a Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como configurar a Integridade do Serviço do Azure para receber notificações de serviço para a Área de Trabalho Virtual do Windows (clássica).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: aac22dfb95cc099a5d1243e120834415dfa61a4b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444251"
---
# <a name="tutorial-set-up-service-alerts-for-windows-virtual-desktop-classic"></a>Tutorial: Configurar alertas de serviço para a Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../set-up-service-alerts.md).

Use a Integridade do Serviço do Azure para monitorar problemas de serviço e consultorias de integridade para a Área de Trabalho Virtual do Windows. A Integridade do Serviço do Azure pode notificá-lo com diferentes tipos de alertas (por exemplo, email ou SMS), ajudá-lo a entender o efeito de um problema e mantê-lo atualizado durante a resolução do problema. A Integridade do Serviço do Azure também ajuda a minimizar o tempo de inatividade e preparar para a manutenção planejada e para alterações que possam afetar a disponibilidade dos recursos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e configurar os alertas de serviço.

Para saber mais, veja a [documentação de Integridade do Serviço do Azure](../../service-health/index.yml).

## <a name="prerequisites"></a>Pré-requisitos

- [Tutorial: Criar um locatário na Área de Trabalho Virtual do Windows](tenant-setup-azure-active-directory.md)
- [Tutorial: Criar entidades de serviço e atribuições de função com o PowerShell](create-service-principal-role-powershell.md)
- [Tutorial: Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace-2019.md)

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
3. Use as instruções em [Criar alertas do log de atividades em notificações de serviço](../../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) para configurar os alertas e as notificações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar e usar a Integridade do Serviço do Azure para monitorar problemas de serviço e consultorias de integridade da Área de Trabalho Virtual do Windows. Para saber mais sobre como entrar na Área de Trabalho Virtual do Windows, continue com as instruções de Conectar-se à Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Conectar-se ao cliente da Área de Trabalho Remota no Windows 7 e no Windows 10](connect-windows-7-10-2019.md)