---
title: Adicionar métricas estendidas para máquinas virtuais do Azure | Microsoft Docs
description: Este artigo ajuda você a habilitar e configurar métricas de diagnóstico estendido para suas VMs do Azure.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
ms.custom: seodec18
ms.openlocfilehash: 6a4f7f5671562679a245d97ad8491764657cbb34
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "66242459"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Adicionar métricas estendidas para máquinas virtuais do Azure

O Cloudyn usa dados de métrica do Azure de suas VMs do Azure para mostrar informações detalhadas sobre seus recursos. Dados da métrica, também chamados de contadores de desempenho, são usados pelo Cloudyn para gerar relatórios. No entanto, o Cloudyn não coleta automaticamente todos os dados de métrica do Azure de VMs convidadas — você precisa habilitar a coleta de métricas. Este artigo ajuda você a habilitar e configurar métricas de diagnóstico adicionais para suas VMs do Azure.

Depois que você habilita a coleta de métricas, você pode:

- Saber quando suas VMs são atingir seus limites de CPU, memória e disco.
- Detectar problemas e tendências de uso.
- Controlar os custos de dimensionamento de acordo com o uso.
- Obter recomendações de otimização de dimensionamento com bom custo-benefício do Cloudyn.

Por exemplo, convém monitorar o percentual de CPU e memória das suas VMs do Azure. As métricas de VM do Azure correspondem ao _percentual de CPU_ e _\Memory\% de bytes confirmados em uso_.

> [!NOTE]
> A coleta de dados de métrica estendida só é compatível com o monitoramento no nível do convidado do Azure. Cloudyn não é compatível com a extensão de VM de logs de Azure Monitor.

## <a name="determine-whether-extended-metrics-are-enabled"></a>Determinar se as métricas estendidas estão habilitadas

1. Entre no Portal do Azure em https://portal.azure.com.
2. Em **Máquinas virtuais**, selecione uma máquina virtual e, em seguida, em **Monitoramento**, selecione **Métricas**. É mostrada uma lista das métricas disponíveis.
3. Selecione algumas métricas e é mostrado um gráfico de dados para elas.  
    ![Métrica de exemplo – porcentagem de CPU de host](./media/azure-vm-extended-metrics/metric01.png)

No exemplo anterior, um conjunto limitado de métricas padrão estão disponíveis para seus hosts, mas as métricas de memória não estão. Métricas da memória fazem parte das métricas estendidas. Nesse caso, as métricas estendidas não estão habilitadas para a VM. Você deve executar algumas etapas adicionais para habilitar a métrica estendida. As informações a seguir o orientará para habilitá-las.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Habilitar métricas estendidas no portal do Azure

Métricas padrão são as métricas do computador host. A métrica _percentual de CPU_ é um exemplo. Também há métricas básicas para VMs convidadas e também são chamadas métricas estendidas. Exemplos de métricas estendidas _incluem\% \Memory bytes confirmados em uso_ e _\ Mbytes bytes_.

Habilitar métricas estendidas é simples. Para cada VM, habilite o monitoramento em nível de convidado. Quando você habilita o monitoramento de nível de convidado, o agente de diagnóstico do Azure é instalado na VM. Por padrão, um conjunto básico de métricas estendidas é adicionado. O processo a seguir é o mesmo para VMs clássicas e regulares e o mesmo para VMs do Windows e do Linux.

Tenha em mente que o monitoramento de nível de convidado do Azure e do Linux exige uma conta de armazenamento. Quando habilita o monitoramento de nível de convidado, se você não escolher uma conta de armazenamento existente, ela será criada para você.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Habilitar o monitoramento em nível de convidado em VMs existentes

1. Em **Máquinas Virtuais**, exiba a lista das suas VMs e, em seguida, selecione uma VM.
2. Em **Monitoramento**, selecione **Configurações de diagnóstico**.
3. Na página de Configurações de Diagnóstico, clique em **Habilitar o monitoramento de nível de convidado**.  
    ![Habilitar o monitoramento no nível do convidado na página Visão geral](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Depois de alguns minutos, o agente de diagnóstico do Azure está instalado na VM. Um conjunto básico de métricas é adicionado. Atualize a página. Os contadores de desempenho adicionados são exibidos na guia Visão geral.
5. Em Monitoramento, selecione **Métricas**.
6. No gráfico de métricas em **Namespace da Métrica**, selecione **Convidado (Clássico)** .
7. Na lista Métrica, você pode exibir todos os contadores de desempenho disponíveis para a VM convidada.  
    ![lista de métricas estendidas de exemplo](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Habilitar o monitoramento em nível de convidado em VMs novas

Quando você cria novas VMs, na guia Gerenciamento, selecione **Ativado** para **Diagnóstico de SO convidado**.

![definir o diagnóstico do SO convidado como Ativado](./media/azure-vm-extended-metrics/new-enable-diag.png)

Para obter mais informações sobre como habilitar métricas estendidas para máquinas virtuais do Azure, consulte [Entendendo e usando o agente Linux do Azure](../virtual-machines/extensions/agent-linux.md) e [visão geral do agente de máquina Virtual do Azure](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Credenciais de Gerenciador de Recursos

Depois de habilitar métricas estendidas, certifique-se de que o Cloudyn tem acesso às suas [credenciais do Resource Manager](activate-subs-accounts.md). Suas credenciais são necessárias para o Cloudyn coletar e exibir dados de desempenho para suas VMs. Eles também são usados para criar recomendações de otimização de custo. O Cloudyn precisa de pelo menos três dias de dados de desempenho de uma instância para determinar se é um candidato para uma recomendação de downsizing.

## <a name="enable-vm-metrics-with-a-script"></a>Habilitar as métricas VM com um script

Você pode habilitar as métricas VM com scripts do Azura PowerShell. Quando você tem muitas VMs que você deseja habilitar métricas, você pode usar um script para automatizar o processo. Scripts de exemplo estão no GitHub em [Habilitar o diagnóstico do Azure](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Exibir métricas de desempenho do Azure

Para exibir as métricas de desempenho em suas instâncias do Azure no portal de Cloudyn, navegue até **Assets** > **Compute** > **Instance Explorer**. Na lista de instâncias de VM, expanda uma instância e um recurso para exibir os detalhes.

![informações de exemplo mostradas no Gerenciador de Instâncias](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não tiver habilitado o acesso à API do Azure Resource Manager para suas contas, vá para [Ativar contas e assinaturas do Azure](activate-subs-accounts.md).
