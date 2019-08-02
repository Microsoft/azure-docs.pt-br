---
title: Configurar alertas de serviço para a Área de Trabalho Virtual do Windows - Azure
description: Como configurar a Integridade do Serviço do Azure para receber notificações de serviço para a Área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: v-chjenk
ms.openlocfilehash: cae75f16da2cad453c74b7e6e9fb62dd789fe5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081136"
---
# <a name="tutorial-set-up-service-alerts"></a>Tutorial: Configurar alertas de serviço

Use a Integridade do Serviço do Azure para monitorar problemas de serviço e consultorias de integridade para a Área de Trabalho Virtual do Windows. A Integridade do Serviço do Azure pode notificá-lo com diferentes tipos de alertas (por exemplo, email ou SMS), ajudá-lo a entender o efeito de um problema e mantê-lo atualizado durante a resolução do problema. A Integridade do Serviço do Azure também ajuda a minimizar o tempo de inatividade e preparar para a manutenção planejada e para alterações que possam afetar a disponibilidade dos recursos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e configurar os alertas de serviço.

Para saber mais, veja a [documentação de Integridade do Serviço do Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Pré-requisitos

- [Tutorial: Criar um locatário na Versão prévia da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Tutorial: Criar entidades de serviço e atribuições de função com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Tutorial: Criar um pool de host com o Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

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

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Integridade do Serviço do Azure**.
3. Use as instruções em [Criar alertas do log de atividades em notificações de serviço](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) para configurar os alertas e as notificações.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar e usar a Integridade do Serviço do Azure para monitorar problemas de serviço e consultorias de integridade da Área de Trabalho Virtual do Windows. Para saber mais sobre como entrar na Área de Trabalho Virtual do Windows, continue com as instruções de Conectar-se à Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Conectar-se ao cliente da Área de Trabalho Remota no Windows 7 e no Windows 10](./connect-windows-7-and-10.md)
