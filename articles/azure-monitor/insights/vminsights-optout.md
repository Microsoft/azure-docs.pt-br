---
title: Como desabilitar o monitoramento com o Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento de suas máquinas virtuais com o Azure Monitor para VMs.
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
ms.openlocfilehash: c6a8322998b391b9021292a3a7213ded56b67feb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386437"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Como desabilitar o monitoramento de suas máquinas virtuais com o Azure Monitor para VMs (versão prévia)

Se, depois de habilitar o monitoramento de suas máquinas virtuais, você decidir que não deseja mais monitorá-las com o Azure Monitor para VMs, poderá desabilitar o monitoramento. Este artigo mostra como fazer isso para uma única ou várias VMs.  

Atualmente, o Azure Monitor para VMs não oferece suporte a desabilitar seletivamente o monitoramento de suas VMs. Se seu espaço de trabalho do Log Analytics estiver configurado para suportar essa solução e outras soluções, bem como coletar outros dados de monitoramento, é importante que você reconheça o impacto e os métodos descritos abaixo antes de continuar.

O Azure Monitor para VMs depende dos seguintes componentes para fornecer sua experiência:

* Um espaço de trabalho do Log Analytics que armazena dados de monitoramento coletados de VMs e outras fontes.
* Coleção de contadores de desempenho configurados no espaço de trabalho, que atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* Duas soluções de monitoramento configuradas no espaço de trabalho - **InfrastructureInsights** e **ServiceMap**, que atualizam a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* Duas extensões de máquina virtual do Azure, **MicrosoftMonitoringAgent** e **DependencyAgent**, que coletam e enviam dados para o workspace.

Considere o seguinte ao se preparar para desabilitar o monitoramento de suas máquinas virtuais com o Azure Monitor para VMs:

* Se você estiver avaliando com uma única VM e aceitou o espaço de trabalho do Log Analytics padrão pré-selecionado, poderá desativar o monitoramento desinstalando o agente de Dependência da VM e desconectando o agente do Log Analytics deste espaço de trabalho. Essa abordagem é apropriada se você pretende usar a VM para outras finalidades e decidir posteriormente reconectá-la a uma área de trabalho diferente.
* Se você estiver usando o espaço de trabalho do Log Analytics para oferecer suporte a outras soluções de monitoramento e coleta de dados de outras fontes, poderá remover componentes da solução do Azure Monitor para VMs do espaço de trabalho sem interrupção ou impacto em seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do seu espaço de trabalho, você poderá continuar a ver o estado de integridade de suas VMs do Azure; especificamente dados de desempenho e mapa quando você navega para qualquer visualização no portal. Os dados acabarão parando de aparecer na visualização Desempenho e Mapa depois de algum tempo; no entanto, a visualização de integridade continuará mostrando o status de integridade de suas VMs. A opção **Experimente agora** estará disponível na VM do Azure selecionada para permitir que você reative o monitoramento no futuro.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Remoção completa do Azure Monitor para VMs

As etapas a seguir descrevem como remover completamente o Azure Monitor para VMs, se você ainda precisar do espaço de trabalho do Log Analytics. Você removerá as soluções **InfrastructureInsights** e **ServiceMap** do workspace.  

>[!NOTE]
>Se você estava usando a solução de monitoramento do Mapa de Serviço antes de ativar o Azure Monitor para VMs e ainda assim confiar nela, não remova essa solução conforme descrito na etapa 6 abaixo.  
>

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite o Log Analytics. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. Na sua lista de espaços de trabalho do Log Analytics, selecione a área de trabalho escolhida ao integrar o Azure Monitor para VMs.
4. No painel esquerdo, selecione **soluções**.  
5. Na lista de soluções, selecione **InfrastructureInsights (nome da área de trabalho)** e, em seguida, na página **Visão geral** da solução, clique em **Excluir**.  Quando solicitado a confirmar, clique em **Sim**.  
6. Na lista de soluções, selecione **ServiceMap (nome da área de trabalho)** e, em seguida, na página **Visão geral** da solução, clique em **Excluir**.  Quando solicitado a confirmar, clique em **Sim**.  

Se antes de integrar o Azure Monitor para VMs, você não estava [coletando os contadores de desempenho habilitados](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled) para as VMs baseadas em Windows ou Linux em sua área de trabalho, precisa desabilitar essas regras seguindo as etapas descritas [aqui](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters) para Windows e para Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Desativar o monitoramento de uma VM do Azure e manter o espaço de trabalho  

As etapas a seguir descrevem como desabilitar o monitoramento de uma máquina virtual habilitada para avaliar o Azure Monitor para VMs, mas ainda é necessário o espaço de trabalho do Log Analytics para dar suporte ao monitoramento de outras fontes. Se essa for uma VM do Azure, você removerá a extensão de VM do agente de dependência e a extensão de VM do agente do Log Analytics para Windows / Linux diretamente da VM. 

>[!NOTE]
>Se a máquina virtual for gerenciada pelo Azure Automation para orquestrar processos, gerenciar a configuração ou gerenciar atualizações, ou gerenciada pela Central de Segurança do Azure para gerenciamento de segurança e detecção de ameaças, o agente do Log Analytics não deve ser removido. Caso contrário, você impedirá que esses serviços e soluções de gerenciamento proativo da sua VM. 

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No Portal do Azure, selecione **Máquinas Virtuais**. 
3. Na lista, selecione uma VM. 
4. No painel esquerdo, selecione **Extensões** e, na página **Extensões**, selecione **DependencyAgent**.
5. Na página de propriedades de extensão, clique em **desinstalação**.
6. Na página **Extensões**, selecione **MicrosoftMonitoringAgent** e, na página de propriedades da extensão, clique em **Desinstalar**.  
