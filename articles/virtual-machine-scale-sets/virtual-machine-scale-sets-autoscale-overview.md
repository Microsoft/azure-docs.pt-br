---
title: Visão geral de dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre as diferentes maneiras para dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais do Azure com base no desempenho ou em uma agenda fixa
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 610f3073594f73f04a68865593be6bfb4188d4f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883663"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Visão geral de dimensionamento automático com conjuntos de dimensionamento de máquinas virtuais do Azure
Um conjunto de dimensionamento de máquinas virtuais do Azure pode aumentar ou diminuir automaticamente o número de instâncias de VM que executam o aplicativo. Esse comportamento automatizado e elástico reduz a sobrecarga de gerenciamento para monitorar e otimizar o desempenho do aplicativo. Você cria regras que definem o desempenho aceitável para uma experiência de cliente positiva. Quando esses limites definidos são atendidos, as regras de dimensionamento automático atuam para ajustar a capacidade do seu conjunto de dimensionamento. Você também pode agendar eventos para aumentar ou diminuir a capacidade do conjunto de dimensionamento automaticamente em horas fixas. Este artigo fornece uma visão geral das métricas disponíveis e de quais ações o dimensionamento automático pode executar.


## <a name="benefits-of-autoscale"></a>Benefícios de dimensionamento automático
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento.

Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou memória, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.


## <a name="use-host-based-metrics"></a>Usar métricas baseadas em host
Você pode criar regras de dimensionamento automático que incorporam métricas de host disponíveis nas suas instâncias de VM. As métricas de host dão visibilidade ao desempenho das instâncias de VM em um conjunto de dimensionamento sem a necessidade de instalar ou configurar outros agentes e coletas de dados. As regras de dimensionamento automático que usam essas métricas podem expandir o número de instâncias de VM em resposta ao uso da CPU, à demanda por memória ou ao acesso ao disco.

As regras de dimensionamento automático que usam métricas baseadas em host podem ser tratadas com uma das ferramentas a seguir:

- [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [PowerShell do Azure](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Para criar regras de dimensionamento automático que usam métricas de desempenho mais detalhadas, você pode [instalar e configurar a extensão de diagnóstico do Azure](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) em instâncias de VM ou [configurar seu aplicativo usando o Application Insights](#application-level-metrics-with-app-insights).

As regras de dimensionamento automático que usam métricas baseadas em host, as métricas de VM no convidado com a extensão de diagnóstico do Azure e o Application Insights podem usar as configurações a seguir.

### <a name="metric-sources"></a>Fontes de métricas
As regras de dimensionamento automático podem usar métricas de uma das seguintes fontes:

| Origem da métrica        | Caso de uso                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Conjunto de dimensionamento atual    | Para métricas de host que não exigem a instalação ou configuração de agentes adicionais.                                  |
| Conta de armazenamento      | A extensão de diagnóstico do Azure grava as métricas de desempenho no armazenamento do Azure, que, em seguida, é consumido para disparar regras de dimensionamento automático. |
| Fila do Barramento de Serviço    | O aplicativo ou outros componentes pode transmitir mensagens em uma fila do Barramento de Serviço do Azure para disparar regras.                   |
| Application Insights | Um pacote de instrumentação instalado em seu aplicativo que transmite métricas diretamente do aplicativo.                         |


### <a name="autoscale-rule-criteria"></a>Critérios de regra de dimensionamento automático
As métricas baseadas em host a seguir estão disponíveis para uso quando você cria regras de dimensionamento automático. Se você usar a extensão de diagnóstico do Azure ou o Application Insights, poderá definir quais métricas devem ser monitoradas e usar regras de dimensionamento automático.

| Nome da métrica               |
|---------------------------|
| Porcentagem de CPU            |
| Entrada na rede                |
| Saída da rede               |
| Bytes de leitura de disco           |
| Bytes de gravação de disco          |
| Operações de leitura de disco/Seg  |
| Operações de gravação de disco/Seg |
| Créditos de CPU Restantes     |
| Créditos de CPU Consumidos      |

Quando você cria regras de dimensionamento automático para monitorar determinada métrica, as regras examinam uma das seguintes ações de agregação de métricas:

| Tipo de agregação |
|------------------|
| Média          |
| Mínimo          |
| Máximo          |
| Total            |
| Último             |
| Contagem            |

As regras de dimensionamento automático são acionadas quando as métricas são comparadas com o limite definido com um dos operadores abaixo:

| Operador                 |
|--------------------------|
| Maior que             |
| Maior ou igual a |
| Menor que                |
| Menor ou igual a    |
| Igual a                 |
| Não igual a             |


### <a name="actions-when-rules-trigger"></a>Ações quando as regras são acionadas
Quando uma regra de dimensionamento automático é acionada, seu conjunto de dimensionamento pode reduzir horizontalmente e automaticamente de uma das seguintes maneiras:

| Operação de dimensionamento     | Caso de uso                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Aumentar a contagem em   | Um número fixo de instâncias de VM a serem criadas. Útil para conjuntos de dimensionamento com um número menor de VMs.                                           |
| Aumentar porcentagem em | Um aumento das instâncias VM baseado em porcentagens. Bom para conjuntos de dimensionamento maiores em que um aumento fixo pode não melhorar o desempenho perceptivelmente. |
| Aumentar a contagem para   | Crie quantas instâncias de VM forem necessárias para alcançar a quantidade máxima desejada.                                                            |
| Diminuir contagem por   | Um número fixo de instâncias de VM a serem removidas. Útil para conjuntos de dimensionamento com um número menor de VMs.                                           |
| Reduzir porcentagem em | Uma redução de instâncias de VM baseada em porcentagens. Bom para conjuntos de dimensionamento maiores em que um aumento fixo pode não reduzir o consumo e os custos de recurso perceptivelmente. |
| Reduzir contagem para   | Remova quantas instâncias de VM forem necessárias para alcançar a quantidade mínima desejada.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Métricas de VM no convidado com a extensão de diagnóstico do Azure
A extensão de diagnóstico do Azure é um agente executado dentro de uma instância de VM. O agente monitora e salva as métricas de desempenho no armazenamento do Azure. Essas métricas de desempenho contêm informações mais detalhadas sobre o status da VM, como *AverageReadTime* para discos ou *PercentIdleTime* para CPU. Você pode criar regras de dimensionamento automático com base em uma noção mais detalhada sobre o desempenho de VM, não apenas o percentual de consumo de memória ou o uso de CPU.

Para usar a extensão de diagnóstico do Azure, você deve criar contas de armazenamento do Azure para suas instâncias de VM, instalar o agente de diagnóstico do Azure e configurar as VMs para transmitir contadores de desempenho específicos para a conta de armazenamento.

Para saber mais, veja os artigos sobre como habilitar a extensão de diagnóstico do Azure em uma [VM do Linux](../virtual-machines/extensions/diagnostics-linux.md) ou [VM do Windows](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Métricas de nível de aplicativo com o Application Insights
Para visualizar melhor o desempenho de seus aplicativos, você pode usar o Application Insights. Instale um pacote de instrumentação pequeno em seu aplicativo que monitora o aplicativo e envia a telemetria para o Azure. Você pode monitorar métricas como o tempo de resposta do seu aplicativo, o desempenho de carregamento de página e a sessão de conta. Essas métricas de aplicativo podem ser usadas para criar regras de dimensionamento automático em um nível granular e incorporado à medida que você dispara regras com base em informações acionáveis que podem afetar a experiência do cliente.

Para saber mais sobre o App Insights, confira [O que é o Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Dimensionamento automático agendado
Também é possível criar regras de dimensionamento automático baseadas em agendamentos. Essas regras baseadas em agenda permitem dimensionar automaticamente o número de instâncias de máquina virtual em horas fixas. Com as regras baseadas em desempenho, pode haver um impacto no desempenho do aplicativo antes do acionamento das regras de dimensionamento automático e antes que as novas instâncias VM sejam provisionadas. Se você puder prever tal demanda, as instâncias de VM adicionais serão provisionadas e estarão prontas para as demandas adicionais de uso do cliente e aplicativo.

Os exemplos a seguir são cenários que podem se beneficiar com o uso de regras de dimensionamento automático com base em agendamento:

- Dimensione automaticamente o número de instâncias de VM no início do dia de trabalho, quando a demanda do cliente aumenta. No final do dia de trabalho, dimensione automaticamente o número de instâncias de VM para minimizar os custos de recursos durante a noite, quando o uso do aplicativo é baixo.
- Se um departamento usa um aplicativo com frequência em determinadas épocas do mês ou ciclo fiscal, dimensione automaticamente o número de instâncias de VM para acomodar essas exigências adicionais.
- Quando há um evento de marketing, uma promoção ou vendas em datas comemorativas, é possível dimensionar automaticamente o número de instâncias de VM para antecipar essa demanda. 


## <a name="next-steps"></a>Próximas etapas
Você pode criar regras de dimensionamento automático que usam métricas baseadas em host podem ser tratadas com uma das ferramentas abaixo:

- [PowerShell do Azure](tutorial-autoscale-powershell.md)
- [CLI do Azure](tutorial-autoscale-cli.md)
- [Modelo do Azure](tutorial-autoscale-template.md)

Esta visão geral detalhou como usar as regras de dimensionamento automático para escalar horizontalmente e expandir ou diminuir o *número* de instâncias de VM no seu conjunto de dimensionamento. Também é possível escalar verticalmente para aumentar ou diminuir o *tamanho* da instância VM. Para obter mais informações, consulte [Dimensionamento vertical automático com conjuntos de Dimensionamento de Máquinas Virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerenciar suas instâncias de VM, consulte [Gerenciar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando suas regras de dimensionamento automático forem disparadas, consulte [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor](../azure-monitor/platform/autoscale-webhook-email.md). Também é possível [Usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
