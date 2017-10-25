---
title: "Gerenciar logs de fluxo de Grupo de Segurança de Rede com o Observador de Rede do Azure | Microsoft Docs"
description: "Esta página explica como gerenciar logs de fluxo de Grupo de Segurança de Rede no Observador de Rede do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 633543aba99f5c09b14a9e4b11adf59ca04d0fe5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gerenciar logs do fluxo do Grupo de Segurança de Rede no Portal do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Logs de fluxo do Grupo de Segurança de Rede são um recurso do Observador de Rede que permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede. Esses logs de fluxo são gravados no formato JSON e fornecem informações importantes, incluindo: 

- Fluxos de entrada e saída por regra.
- A NIC à qual o fluxo se aplica.
- Informações de cinco tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, protocolo).
- Informação sobre o tráfego ter sido permitido ou negado.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já seguiu as etapas em [Criar uma instância de Observador de Rede](network-watcher-create.md). O cenário também pressupõe que você tem um grupo de recursos com uma máquina virtual válida.

## <a name="register-insights-provider"></a>Provedor de informações de registro

Para o registro de fluxo em log funcionar, o provedor **Microsoft.Insights** deve ser registrado. Para registrar o provedor, siga as seguintes etapas: 

1. Va até **Assinaturas** e selecione a assinatura para a qual deseja habilitar os logs de fluxo. 
2. Na folha **Assinatura**, selecione **Provedores de Recursos**. 
3. Confira a lista de provedores e verifique se o provedor **microsoft.insights** está registrado. Se não estiver, selecione **Registrar**.

![Exibir provedores][providers]

## <a name="enable-flow-logs"></a>Habilitar logs de fluxo

Estas etapas guiarão você pelo processo de habilitação dos logs de fluxo em um Grupo de Segurança de Rede.

### <a name="step-1"></a>Etapa 1

Vá até uma instância do Observador de Rede e selecione **Logs de fluxo de NSG**.

![Visão geral dos logs de fluxo][1]

### <a name="step-2"></a>Etapa 2

Selecione um Grupo de Segurança de Rede na lista.

![Visão geral dos logs de fluxo][2]

### <a name="step-3"></a>Etapa 3 

Na folha **Configurações dos logs do fluxo**, defina o status como **Ligado** e configure uma conta de armazenamento.  Quando terminar, selecione **OK**. Em seguida, selecione **Salvar**.

![Visão geral dos logs de fluxo][3]

## <a name="download-flow-logs"></a>Baixar logs de fluxo

Os Logs de fluxo são salvos em uma conta de armazenamento. Baixe os logs de fluxo para exibi-los.

### <a name="step-1"></a>Etapa 1

Para baixar os logs de fluxo, selecione **É possível baixar logs de fluxo nas contas de armazenamento configuradas**. Esta etapa leva você até uma exibição da conta de armazenamento em que você pode escolher quais logs baixar.

![Configurações dos logs do fluxo][4]

### <a name="step-2"></a>Etapa 2

Vá para a conta de armazenamento correta. Em seguida, selecione **Contêineres** > **insights-log-networksecuritygroupflowevent**.

![Configurações dos logs do fluxo][5]

### <a name="step-3"></a>Etapa 3

Vá para o local do log de fluxo, selecione-o e, em seguida, selecione **Baixar**.

![Configurações dos logs do fluxo][6]

Para saber mais sobre a estrutura do log, visite a [Visão geral do log do fluxo de Grupo de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Próximas etapas

Saiba como [visualizar seus logs de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
