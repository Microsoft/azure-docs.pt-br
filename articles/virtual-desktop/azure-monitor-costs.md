---
title: Monitorar estimativas de preços de custo de área de trabalho virtual do Windows – Azure
description: Como estimar custos e preços para usar o Azure Monitor para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 099def5a29c8a9b46733ba435befed7210756012
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709972"
---
# <a name="estimate-azure-monitor-costs"></a>Estimar custos Azure Monitor

Os logs de Azure Monitor são um serviço que coleta, indexa e armazena dados gerados pelo seu ambiente. Por isso, o modelo de preços Azure Monitor se baseia na quantidade de dados que são trazidos e processados (ou "ingeridos") pelo seu espaço de trabalho de Log Analytics em gigabytes por dia. O custo de um espaço de trabalho de Log Analytics não é apenas baseado no volume de dados coletados, mas também no plano de pagamento do Azure que você selecionou e por quanto tempo você opta por armazenar os dados que seu ambiente gera.

Este artigo explicará as seguintes informações para ajudá-lo a entender como os preços no Azure Monitor funcionam:

- Como estimar os custos de ingestão e armazenamento de dados antes de habilitar esse recurso
- Como medir e controlar sua ingestão e armazenamento para reduzir custos ao usar esse recurso

>[!NOTE]
> Todos os tamanhos e preços listados neste artigo são apenas exemplos para demonstrar como a estimativa funciona. Para obter uma avaliação mais precisa com base em seu Azure Monitor Log Analytics modelo de preços e na região do Azure, consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="estimate-data-ingestion-and-storage-costs"></a>Estimar os custos de ingestão e armazenamento de dados

Recomendamos que você use um conjunto predefinido de dados gravados como logs em seu espaço de trabalho do Log Analytics. No exemplo a seguir estimado, veremos os dados faturáveis na configuração padrão

Os conjuntos de valores predefinidos para Azure Monitor para área de trabalho virtual do Windows incluem:

- Contadores de desempenho dos hosts de sessão
- Logs de eventos do Windows dos hosts de sessão
- Diagnóstico de área de trabalho virtual do Windows da infraestrutura de serviço

A ingestão de dados e os custos de armazenamento dependem do tamanho do ambiente, da integridade e do uso. As estimativas de exemplo que usaremos neste artigo para calcular os intervalos de custo que você pode esperar são baseadas em máquinas virtuais íntegras que executam leve ao uso de energia, com base em nossas [diretrizes de dimensionamento de máquina virtual](/remote/remote-desktop-services/virtual-machine-recs), para calcular uma variedade de ingestão de dados e custos de armazenamento que você pode esperar.

A VM de uso claro que usaremos em nosso exemplo inclui os seguintes componentes:

- 4 vCPUs, 1 disco
- 16 sessões por dia
- Uma duração média da sessão de 2 horas (120 minutos)
- 100 processos por sessão

A VM de uso de energia que usaremos em nosso exemplo inclui os seguintes componentes:

- 6 vCPUs, 1 disco
- 6 sessões por dia
- Duração média da sessão de 4 horas (240 minutos)
- 200 processos por sessão

## <a name="estimating-performance-counter-ingestion"></a>Estimando a ingestão do contador de desempenho

Os contadores de desempenho mostram como os recursos do sistema estão sendo executados. A ingestão de dados do contador de desempenho depende do tamanho e do uso do ambiente. Na maioria dos casos, os contadores de desempenho devem ter de 80 a 99% da ingestão de dados para Azure Monitor para área de trabalho virtual do Windows.

Antes de começar a estimativa, é importante entender que cada contador de desempenho envia dados em uma frequência específica. Definimos uma taxa de amostragem padrão por minuto (você também pode editar essa taxa em suas configurações), mas essa taxa será aplicada em diferentes fatores de multiplicação, dependendo do contador. Os seguintes fatores afetam a taxa:

- Para o fator por VM (máquina virtual), cada contador envia dados por VM em seu ambiente na taxa de amostragem padrão por minuto enquanto a VM está em execução. Você pode estimar o número de registros que esses contadores enviam por dia multiplicando a taxa de amostragem padrão por minuto pelo número de VMs em seu ambiente e multiplicando esse número pelo tempo médio de execução da VM por dia.

   Para resumir:

   Taxa de amostragem padrão por minuto × número de núcleos de CPU no SKU da VM × número de VMs × tempo médio de execução da VM por dia = número de registros enviados por dia

- Para o fator por CPU, cada contador envia na taxa de amostragem padrão por minuto por vCPU em cada VM em seu ambiente enquanto a VM está em execução. Você pode estimar o número de registros que os contadores enviarão por dia multiplicando a taxa de amostragem padrão por minuto pelo número de núcleos de CPU no SKU da VM e multiplicando esse número pelo número de minutos que a VM executa e o número de VMs em seu ambiente.

   Para resumir:
   
   Taxa de amostragem padrão por minuto × número de núcleos de CPU no SKU da VM × número de minutos em que a VM é executada × número de VMs = número de registros enviados por dia

- Para o fator por disco, cada contador envia dados na taxa de amostragem padrão para cada disco em cada VM em seu ambiente. O número de registros que esses contadores enviará por dia é igual à taxa de amostragem padrão por minuto multiplicada pelo número de discos na SKU da VM, multiplicado por 60 minutos por hora e, por fim, multiplicado pela média de horas ativas para uma VM.

   Para resumir:

   Taxa de amostragem padrão por minuto × número de discos no SKU da VM × 60 minutos por hora × número de VMs × tempo médio de execução da VM por dia = número de registros enviados por dia

- Para o fator por sessão, cada contador envia dados na taxa de amostragem padrão para cada sessão em seu ambiente enquanto a sessão está conectada. Você pode estimar o número de registros que esses contadores enviarão por dia pode multiplicar a taxa de amostragem padrão por minuto pelo número médio de sessões por dia e a duração média da sessão.

   Para resumir:

   Taxa de amostragem padrão por minuto × sessões por dia × duração média da sessão = número de registros enviados por dia

- Para o fator por processo, cada contador envia dados na taxa padrão para cada processo em cada sessão em seu ambiente. Você pode estimar o número de registros que esses contadores enviarão por dia multiplicando a taxa de amostragem padrão por minuto pelo número médio de sessões por dia e multiplicando isso pela duração média da sessão e o número médio de processos por sessão.
  
   Para resumir:

   Taxa de amostragem padrão por minuto × sessões por dia × duração média da sessão × número médio de processos por sessão = número de registros enviados por dia

A tabela a seguir lista os 20 contadores de desempenho Azure Monitor para o Windows Virtual Desktop coleta e suas tarifas padrão:

| Nome do contador | Taxa de amostragem padrão | Fator de frequência |
|--------------|---------------------|------------------|
| Disco lógico (C:) \\ % de espaço livre | 60 segundos  | Por disco             |
| Disco lógico (C:) \\ comprimento médio da fila de disco   | 30 segundos    | Por disco             |
| Disco lógico (C:) \\ média de disco s/transferência  | 60 segundos       | Por disco             |
| Disco lógico (C:) \\ comprimento da fila de disco atual  | 30 segundos      | Por disco             |
| Memória ( \* ) \\ MBytes disponíveis | 30 segundos    | Por VM  |
| Memória ( \* ) \\ falhas de página/s | 30 segundos   | Por VM  |
| Memória ( \* ) \\ páginas/s | 30 segundos   | Por VM  |
| Memória ( \* ) \\ % bytes confirmados em uso | 30 segundos    | Por VM  |
| PhysicalDisk ( \* ) \\ comprimento médio da fila de disco | 30 segundos      | Por disco             |
| PhysicalDisk ( \* ) \\ média de disco s/leitura | 30 segundos  | Por disco             |
| PhysicalDisk ( \* ) \\ média de disco s/transferência | 30 segundos  | Por disco             |
| PhysicalDisk ( \* ) \\ média de disco s/gravação | 30 segundos | Por disco             |
| Informações do processador (_Total) \\ % tempo do processador | 30 segundos | Por núcleo/CPU         |
| \*Sessões ativas dos serviços de terminal () \\          | 60 segundos | Por VM  |
| \*Sessões inativas dos serviços de terminal () \\        | 60 segundos | Por VM  |
| Total de sessões dos serviços de terminal ( \* ) \\ | 60 segundos | Por VM  |
| Atraso de entrada do usuário por processo ( \* ) \\ atraso máximo de entrada         | 30 segundos | Por processo          |
| Atraso de entrada do usuário por sessão ( \* ) \\ atraso máximo de entrada        | 30 segundos | Por sessão          |
| \* \\ RTT de TCP atual () de rede do RemoteFX | 30 segundos | Por VM  |
| Rede do RemoteFX ( \* ) \\ largura de banda UDP atual     | 30 segundos | Por VM  |

Se estimarmos que cada tamanho de registro seja 200 bytes, uma VM de exemplo executando uma carga de trabalho leve na taxa de exemplo padrão enviaria aproximadamente 90 megabytes de dados do contador de desempenho por dia por VM. Enquanto isso, um exemplo de VM executando uma carga de trabalho de energia enviaria aproximadamente 130 megabytes de dados do contador de desempenho por dia por VM. No entanto, o tamanho do registro e o uso do ambiente podem variar, portanto, os megabytes por dia que sua implantação usa podem ser diferentes.

Para saber mais sobre contadores de desempenho de atraso de entrada, confira [contadores de desempenho de atraso de entrada do usuário](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="estimating-windows-event-log-ingestion"></a>Estimando a ingestão do log de eventos do Windows

Os logs de eventos do Windows são fontes de dados coletadas por agentes de Log Analytics em máquinas virtuais do Windows. Você pode coletar eventos de logs padrão, como sistema e aplicativo, bem como logs personalizados criados por aplicativos que você precisa monitorar.

Estes são os eventos padrão do Windows para o Azure Monitor para área de trabalho virtual do Windows:

- Aplicativo
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/operacional
- Sistema
- Microsoft-FSLogix-apps/operacional
- Microsoft-FSLogix-apps/admin

Os eventos do Windows são enviados sempre que os termos do evento são atendidos no ambiente. Os computadores em Estados íntegros enviarão menos eventos do que computadores em Estados não íntegros. Como a contagem de eventos é imprevisível, usamos um intervalo de 1.000 a 10.000 eventos por VM por dia, com base em exemplos de ambientes íntegros para essa estimativa. Por exemplo, se estimarmos que cada tamanho de registro de evento neste exemplo seja de 1.500 bytes, isso chegará a aproximadamente 2 a 15 megabytes de dados de evento por dia para o ambiente especificado.

Para saber mais sobre eventos do Windows, consulte [Propriedades de registros de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="estimating-diagnostics-ingestion"></a>Estimando a ingestão de diagnósticos

O serviço de diagnóstico cria logs de atividade para ações administrativas e de usuário.

Estes são os nomes dos logs de atividade que o contador de diagnóstico rastreia:

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

O serviço envia informações de diagnóstico sempre que o ambiente atende aos termos necessários para fazer um registro. Como a contagem de registros de diagnóstico é imprevisível, usamos um intervalo de 500 a 1000 eventos por VM por dia, com base em exemplos de ambientes íntegros para essa estimativa.

Por exemplo, se estimarmos que cada tamanho de registro de diagnóstico neste exemplo seja de 200 bytes, o total de dados ingeridos será menor que 1 MB por VM por dia.

Para saber mais sobre as categorias de log de atividades, consulte [diagnóstico de área de trabalho virtual do Windows](diagnostics-log-analytics.md).

## <a name="estimating-total-costs"></a>Estimando custos totais

Por fim, vamos estimar o custo total. Neste exemplo, digamos que venhamos com os seguintes resultados com base nos valores de exemplo nas seções anteriores:

| Fonte de dados        | Estimativa de tamanho por dia (em megabytes)   |
|-------------------------------------|------------------------------------------|
| Contadores de desempenho   | 90-130 |
| Eventos    | 2-15 |
| Diagnóstico de área de trabalho virtual do Windows | \< uma |

Neste exemplo, o total de dados ingeridos para Azure Monitor para área de trabalho virtual do Windows é entre 92 e 145 megabytes por VM por dia. Em outras palavras, a cada 31 dias, cada VM ingeri aproximadamente de 3 a 5 gigabytes de dados.

Usando o modelo pago conforme o uso padrão para [preços de log Analytics](https://azure.microsoft.com/pricing/details/monitor/), você pode estimar a coleta de dados de Azure monitor e o custo de armazenamento por mês. Dependendo da ingestão de dados, você também pode considerar o modelo de reserva de capacidade para preços de Log Analytics.

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>Gerencie sua ingestão de dados para reduzir custos

Esta seção explicará como medir e gerenciar a ingestão de dados para reduzir os custos.

Para saber mais sobre como gerenciar direitos e permissões para a pasta de trabalho, consulte [controle de acesso](../azure-monitor/visualize/workbooks-access-control.md).

>[!NOTE]
>A remoção de pontos de dados afetará seus visuais correspondentes em Azure Monitor para área de trabalho virtual do Windows.

### <a name="log-analytics-settings"></a>Configurações de Log Analytics

Aqui estão algumas sugestões para otimizar suas configurações de Log Analytics para gerenciar a ingestão de dados:

- Use um espaço de trabalho Log Analytics designado para seus recursos de área de trabalho virtual do Windows para garantir que Log Analytics só colete contadores de desempenho e eventos para as máquinas virtuais em sua implantação de área de trabalho virtual do Windows.
- Ajuste as configurações de armazenamento de Log Analytics para gerenciar os custos. Você pode reduzir o período de retenção, avaliar se um tipo de preço de armazenamento fixo seria mais econômico ou definir limites sobre a quantidade de dados que você pode ingerir para limitar o impacto de uma implantação não íntegra. Para saber mais, confira [gerenciar o uso e os custos para logs de Azure monitor](../azure-monitor/platform/manage-cost-storage.md).

### <a name="remove-excess-data"></a>Remover dados em excesso

Nossa configuração padrão é o único conjunto de dados que recomendamos para Azure Monitor para área de trabalho virtual do Windows. Você sempre tem a opção de adicionar pontos de dados adicionais e exibi-los no diagnóstico do host: o navegador de host ou criar gráficos personalizados para eles, mas os dados adicionados aumentarão o custo de Log Analytics. Elas podem ser removidas para economia de custos.

### <a name="measure-and-manage-your-performance-counter-data"></a>Meça e gerencie seus dados do contador de desempenho

Os custos de monitoramento reais dependerão do tamanho do ambiente, do uso e da integridade. Para entender como medir a ingestão de dados em seu espaço de trabalho Log Analytics, consulte [noções básicas sobre volume de dados de log ingerido](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume).

Os contadores de desempenho que os hosts de sessão usam provavelmente serão a sua maior fonte de dados ingeridos para Azure Monitor para área de trabalho virtual do Windows. O modelo de consulta personalizada a seguir para um espaço de trabalho Log Analytics pode controlar a frequência e os megabytes ingeridos por contador de desempenho no último dia:

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>Certifique-se de substituir os valores de espaço reservado do modelo pelos valores usados pelo seu ambiente, caso contrário, a consulta não funcionará.

Essa consulta mostrará todos os contadores de desempenho que você habilitou no ambiente, não apenas os padrões para Azure Monitor para área de trabalho virtual do Windows. Essas informações podem ajudá-lo a entender quais áreas visar reduzir custos, como reduzir a frequência de um contador ou removê-lo completamente.

Você também pode reduzir os custos removendo os contadores de desempenho. Para saber como remover contadores de desempenho ou editar contadores existentes para reduzir sua frequência, consulte [Configurando contadores de desempenho](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters).

### <a name="manage-windows-event-logs"></a>Gerenciar logs de eventos do Windows

Os eventos do Windows provavelmente causarão um pico na ingestão de dados quando todos os hosts estiverem íntegros. Um host não íntegro pode aumentar o número de eventos enviados ao log, mas as informações podem ser essenciais para corrigir os problemas do host. Recomendamos mantê-los. Para saber mais sobre como gerenciar logs de eventos do Windows, consulte [Configurando logs de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

### <a name="manage-diagnostics"></a>Gerenciar diagnósticos

O diagnóstico de área de trabalho virtual do Windows deve reduzir menos de 1% dos custos de armazenamento de dados, portanto, não recomendamos removê-los. Para gerenciar o diagnóstico de área de trabalho virtual do Windows, [Use log Analytics para o recurso de diagnóstico](diagnostics-log-analytics.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Azure Monitor para área de trabalho virtual do Windows nestes artigos:

- [Use Azure monitor para área de trabalho virtual do Windows para monitorar sua implantação](azure-monitor.md).
- Use o [Glossário](azure-monitor-glossary.md) para saber mais sobre os termos e conceitos.
- Se você encontrar um problema, confira nosso [Guia de solução de problemas](troubleshoot-azure-monitor.md) para obter ajuda.
- Confira [monitoramento de uso e custos estimados em Azure monitor](../azure-monitor/usage-estimated-costs.md) para saber mais sobre como gerenciar seus custos de monitoramento.
