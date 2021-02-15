---
title: Desabilitar monitoramento no Azure Monitor para VMs
description: Este artigo descreve como parar de monitorar suas máquinas virtuais no Azure Monitor para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79480514"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Desabilite o monitoramento de suas VMs no Azure Monitor para VMs

Depois de habilitar o monitoramento de suas VMs (máquinas virtuais), você pode optar por desabilitar o monitoramento em Azure Monitor para VMs mais tarde. Este artigo mostra como desabilitar o monitoramento de uma ou mais VMs.  

Atualmente, Azure Monitor para VMs não dá suporte à desabilitação seletiva de monitoramento de VM. Seu espaço de trabalho do Log Analytics pode dar suporte a Azure Monitor para VMs e outras soluções. Ele também pode coletar outros dados de monitoramento. Se seu espaço de trabalho do Log Analytics fornece esses serviços, você precisa entender o efeito e os métodos de desabilitar o monitoramento antes de começar.

O Azure Monitor para VMs depende dos seguintes componentes para fornecer sua experiência:

* Um espaço de trabalho Log Analytics, que armazena dados de monitoramento de VMs e outras fontes.
* Uma coleção de contadores de desempenho configurados no espaço de trabalho. A coleção atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `VMInsights`, que é uma solução de monitoramento configurada no espaço de trabalho. Essa solução atualiza a configuração de monitoramento em todas as VMs conectadas ao espaço de trabalho.
* `MicrosoftMonitoringAgent` e `DependencyAgent` que são extensões de VM do Azure. Essas extensões coletam e enviam dados para o espaço de trabalho.

Ao se preparar para desabilitar o monitoramento de suas VMs, tenha em mente estas considerações:

* Se você avaliou com uma única VM e usou o espaço de trabalho de Log Analytics padrão selecionado, você pode desabilitar o monitoramento desinstalando o agente de dependência da VM e desconectando o agente de Log Analytics desse espaço de trabalho. Essa abordagem é apropriada se você pretende usar a VM para outras finalidades e decidir mais tarde para reconectá-la a um espaço de trabalho diferente.
* Se você selecionou um espaço de trabalho de Log Analytics pré-existente que dá suporte a outras soluções de monitoramento e coleta de dados de outras fontes, você pode remover componentes da solução do espaço de trabalho sem interromper ou afetar seu espaço de trabalho.  

>[!NOTE]
> Depois de remover os componentes da solução do seu espaço de trabalho, você pode continuar a ver o desempenho e mapear dados para suas VMs do Azure. Os dados eventualmente serão interrompidos nas exibições de **desempenho** e de **mapa** . A opção **habilitar** estará disponível na VM do Azure selecionada para que você possa reabilitar o monitoramento no futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Remover Azure Monitor para VMs completamente

Se você ainda precisar do espaço de trabalho Log Analytics, siga estas etapas para remover completamente Azure Monitor para VMs. Você removerá a `VMInsights` solução do espaço de trabalho.  

1. Entre no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Conforme você começa a digitar, a lista filtra as sugestões com base em sua entrada. Selecione o **Log Analytics**.
3. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você escolheu quando habilitou Azure Monitor para VMs.
4. À esquerda, selecione **soluções**.  
5. Na lista de soluções, selecione **VMInsights (nome do espaço de trabalho)**. Na página **visão geral** da solução, selecione **excluir**. Quando for solicitado a confirmar, selecione **Sim**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Desabilitar o monitoramento e manter o espaço de trabalho  

Se seu espaço de trabalho do Log Analytics ainda precisar de suporte ao monitoramento de outras fontes, seguindo estas etapas para desabilitar o monitoramento na VM que você usou para avaliar Azure Monitor para VMs. Para VMs do Azure, você removerá a extensão de VM do agente de dependência e a extensão de VM do agente de Log Analytics para Windows ou Linux diretamente da VM. 

>[!NOTE]
>Não remova o agente de Log Analytics se: 
>
> * A automação do Azure gerencia a VM para orquestrar processos ou para gerenciar a configuração ou as atualizações. 
> * A central de segurança do Azure gerencia a VM para detecção de ameaças e segurança. 
>
> Se você remover o agente de Log Analytics, impedirá que esses serviços e soluções gerenciem proativamente sua VM. 

1. Entre no [portal do Azure](https://portal.azure.com). 
2. No Portal do Azure, selecione **Máquinas Virtuais**. 
3. Na lista, selecione uma VM. 
4. À esquerda, selecione **extensões**. Na página **extensões** , selecione **DependencyAgent**.
5. Na página Propriedades da extensão, selecione **desinstalar**.
6. Na página **extensões** , selecione **extensão microsoftmonitoringagent**. Na página Propriedades da extensão, selecione **desinstalar**.  
