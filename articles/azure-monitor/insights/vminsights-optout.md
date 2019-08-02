---
title: Desabilitar o monitoramento no Azure Monitor para máquinas virtuais (versão prévia) | Microsoft Docs
description: Este artigo descreve como interromper o monitoramento de máquinas virtuais no Azure Monitor para VMs.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155698"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Desabilitar o monitoramento de suas VMs no Azure Monitor para máquinas virtuais (versão prévia)

Depois de habilitar o monitoramento de suas máquinas virtuais (VMs), você pode escolher mais tarde desabilitar o monitoramento no Azure Monitor para máquinas virtuais. Este artigo mostra como desabilitar o monitoramento para uma ou mais VMs.  

Atualmente, Monitor do Azure para VMs não dá suporte a desabilitar seletiva de monitoramento da VM. Seu espaço de trabalho do Log Analytics pode oferecer suporte do Azure Monitor para VMs e outras soluções. Ele também poderá coletar outros dados de monitoramento. Se o seu espaço de trabalho do Log Analytics fornece esses serviços, você precisa entender o efeito e os métodos da desabilitação do monitoramento antes de iniciar.

O Azure Monitor para VMs depende dos seguintes componentes para fornecer sua experiência:

* Um espaço do Log Analytics, que armazena dados de monitoramento de máquinas virtuais e outras fontes.
* Uma coleção de contadores de desempenho configurados no espaço de trabalho. A coleção atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `InfrastructureInsights` e `ServiceMap`, que está monitorando soluções configuradas no espaço de trabalho. Essas soluções atualizar a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `MicrosoftMonitoringAgent` e `DependencyAgent`, que são extensões da VM do Azure. Essas extensões de coletam e enviar dados ao espaço de trabalho.

Quando você se prepara para desabilitar o monitoramento de suas VMs, tenha em mente essas considerações:

* Se você avaliou com uma única VM e usado o espaço de trabalho do Log Analytics pré-selecionados padrão, você pode desabilitar o monitoramento, desinstale o agente de dependência da VM e desconectar-se o agente do Log Analytics deste espaço de trabalho. Essa abordagem é apropriada se você pretende usar a VM para outras finalidades e decidir mais tarde reconectar-se para outro espaço de trabalho.
* Se você selecionou um espaço de trabalho do Log Analytics preexistente que dá suporte a outras soluções de monitoramento e coleta de dados de outras fontes, você pode remover os componentes da solução do espaço de trabalho sem interromper ou afetar seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do seu espaço de trabalho, você pode continuar a ver o estado de integridade das VMs do Azure; Especificamente, você ver o desempenho e mapear dados quando você vai para o modo de exibição no portal. Dados eventualmente deixará de aparecer na **desempenho** e **mapa** modos de exibição. Mas o **integridade** exibição continuará mostrar o status de integridade para suas VMs. O **Experimente agora** opção estará disponível na VM do Azure selecionado para que você pode habilitar novamente o monitoramento no futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Remover o Azure Monitor para VMs completamente

Se você ainda precisar de espaço de trabalho do Log Analytics, siga estas etapas para remover completamente o Azure Monitor para VMs. Você removerá o `InfrastructureInsights` e `ServiceMap` soluções do espaço de trabalho.  

>[!NOTE]
>Se você usou o mapa do serviço de solução de monitoramento antes que você habilitou o Azure Monitor para VMs e você ainda depende dela, não remova essa solução, conforme descrito na última etapa do procedimento a seguir.  
>

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Conforme você começa a digitar, a lista é filtrada com base na sua entrada de sugestões. Selecione **Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho você escolheu quando você habilitou o Azure Monitor para VMs.
4. À esquerda, selecione **soluções**.  
5. Na lista de soluções, selecione **InfrastructureInsights (nome do espaço de trabalho)** . Sobre o **visão geral** página de solução, selecione **excluir**. Quando solicitado a confirmar, selecione **Sim**.  
6. Na lista de soluções, selecione **ServiceMap (nome do espaço de trabalho)** . Sobre o **visão geral** página de solução, selecione **excluir**. Quando solicitado a confirmar, selecione **Sim**.  

Antes de você habilitou o Azure Monitor para VMs, se você não fez isso [coletar contadores de desempenho](vminsights-enable-overview.md#performance-counters-enabled) para as VMs com base em Linux ou Windows em seu espaço de trabalho [desabilitar essas regras](../platform/data-sources-performance-counters.md#configuring-performance-counters) para Windows e Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desabilite o monitoramento e manter o espaço de trabalho  

Se seu espaço de trabalho do Log Analytics ainda precisa dar suporte ao monitoramento de outras fontes, siga estas etapas para desabilitar o monitoramento na máquina virtual que você usou para avaliar o Azure Monitor para máquinas virtuais. Para VMs do Azure, você removerá o extensão de VM do agente de dependência e o extensão de VM do agente do Log Analytics para Windows ou Linux diretamente da VM. 

>[!NOTE]
>Não remova o agente do Log Analytics se: 
>
> * A automação do Azure gerencia a VM para orquestrar os processos ou para gerenciar a configuração ou atualizações. 
> * A Central de segurança do Azure gerencia a segurança e detecção de ameaças da VM. 
>
> Se você remover o agente do Log Analytics, você impedirá que esses serviços e soluções de gerenciamento proativo da sua VM. 

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. No Portal do Azure, selecione **Máquinas Virtuais**. 
3. Na lista, selecione uma VM. 
4. À esquerda, selecione **extensões**. Sobre o **extensões** página, selecione **DependencyAgent**.
5. Na página de propriedades de extensão, selecione **desinstalação**.
6. Sobre o **extensões** página, selecione **MicrosoftMonitoringAgent**. Na página de propriedades de extensão, selecione **desinstalação**.  
