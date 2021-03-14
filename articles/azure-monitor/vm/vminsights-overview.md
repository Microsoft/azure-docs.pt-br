---
title: O que é o virtual insights?
description: Visão geral do VM insights, que monitora a integridade e o desempenho das VMs do Azure e descobre e mapeia automaticamente os componentes do aplicativo e suas dependências.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046407"
---
# <a name="overview-of-vm-insights"></a>Visão geral de informações de VM

As informações da VM monitoram o desempenho e a integridade de suas máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais, incluindo seus processos e dependências em execução em outros recursos. Ele pode ajudar a fornecer desempenho previsível e disponibilidade de aplicativos vitais identificando gargalos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado a outras dependências.

As informações de VM oferecem suporte aos sistemas operacionais Windows e Linux nos seguintes computadores:

- Máquinas virtuais do Azure
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas conectadas com o arco do Azure
- Máquinas virtuais locais
- Máquinas virtuais hospedadas em outro ambiente de nuvem
  

As informações de VM armazenam seus dados em logs de Azure Monitor, o que permite que ele forneça agregação e filtragem poderosas e analise as tendências de dados ao longo do tempo. Você pode exibir esses dados em uma única VM da máquina virtual diretamente ou pode usar Azure Monitor para fornecer uma exibição agregada de várias VMs.

![Perspectiva de insights de máquina virtual no portal do Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Preços
Não há custo direto para o virtual insights, mas você é cobrado por sua atividade no espaço de trabalho Log Analytics. Com base no preço que é publicado na [página de preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), o VM insights é cobrado por:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Dados de estado de integridade coletados da integridade do convidado (visualização)
- Regras de alerta com base em dados de log e de integridade.
- Notificações enviadas de regras de alerta.

O tamanho do log varia de acordo com os comprimentos de cadeia de caracteres de contadores de desempenho e pode aumentar com o número de discos lógicos e adaptadores de rede alocados para a VM. Se você já estiver usando Mapa do Serviço, a única alteração que você verá é os dados de desempenho extras que são enviados para o `InsightsMetrics` tipo de dados Azure monitor.


## <a name="configuring-vm-insights"></a>Configurando o VM insights
As etapas para configurar o VM insights são as seguintes. Siga cada link para obter orientações detalhadas sobre cada etapa:

- [Criar Log Analytics espaço de trabalho.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Adicione a solução VMInsights ao espaço de trabalho.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Instale agentes na máquina virtual e no conjunto de dimensionamento de máquinas virtuais a ser monitorado.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Próximas etapas

- Consulte [implantar informações de VM](./vminsights-enable-overview.md) para requisitos e métodos que permitem o monitoramento de suas máquinas virtuais.
