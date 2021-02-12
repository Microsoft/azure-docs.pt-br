---
title: Monitorar a integridade do espaço de trabalho de Log Analytics no Azure Monitor
description: Descreve como monitorar a integridade do seu espaço de trabalho do Log Analytics usando dados na tabela de operações.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 3cb01a8f1c06bad618ae5c7930920ee0f067038c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184528"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Monitorar a integridade do espaço de trabalho de Log Analytics no Azure Monitor
Para manter o desempenho e a disponibilidade do seu espaço de trabalho do Log Analytics no Azure Monitor, você precisa ser capaz de detectar proativamente quaisquer problemas que surjam. Este artigo descreve como monitorar a integridade do seu espaço de trabalho do Log Analytics usando dados na tabela de [operações](/azure/azure-monitor/reference/tables/operation) . Essa tabela está incluída em todos os Log Analytics espaço de trabalho e contém erros e avisos que ocorrem em seu espaço de trabalho. Você deve examinar esses dados regularmente e criar alertas para que sejam notificados proativamente quando houver incidentes importantes em seu espaço de trabalho.

## <a name="_logoperation-function"></a>Função _LogOperation

Os logs de Azure Monitor enviam detalhes sobre quaisquer problemas na tabela de [operações](/azure/azure-monitor/reference/tables/operation) no espaço de trabalho em que o problema ocorreu. A função de sistema **_LogOperation** é baseada na tabela de **operações** e fornece um conjunto simplificado de informações para análise e alertas.

## <a name="columns"></a>Colunas

A função **_LogOperation** retorna as colunas na tabela a seguir.

| Coluna | Descrição |
|:---|:---|
| TimeGenerated | Hora em que o incidente ocorreu em UTC. |
| Categoria  | Grupo de categorias de operação. Pode ser usado para filtrar tipos de operações e ajudar a criar alertas e auditoria de sistema mais precisos. Consulte a seção abaixo para obter uma lista de categorias. |
| Operação  | Descrição do tipo de operação. Isso pode indicar um dos limites de Log Analytics, o tipo de operação ou parte de um processo. |
| Nível | Nível de severidade do problema:<br>-Informações: nenhuma atenção específica necessária.<br>-Aviso: o processo não foi concluído conforme o esperado e a atenção é necessária.<br>-Erro: o processo de falha e a atenção urgente é necessário. 
| Detalhe | A descrição detalhada da operação incluirá uma mensagem de erro específica se existir. |
| _ResourceId | ID de recurso do recurso do Azure relacionado à operação.  |
| Computador | Nome do computador se a operação estiver relacionada a um agente de Azure Monitor. |
| CorrelationId | Usado para agrupar operações relacionadas consecutivas. |


## <a name="categories"></a>Categorias

A tabela a seguir descreve as categorias da função _LogOperation. 

| Categoria | Descrição |
|:---|:---|
| Ingestão           | Operações que fazem parte do processo de ingestão de dados. Consulte abaixo para obter mais detalhes. |
| Agente               | Indica um problema com a instalação do agente. |
| Coleta de dados     | Operações relacionadas a processos de coleta de dados. |
| Direcionamento de solução  | A operação do tipo *ConfigurationScope* foi processada. |
| Solução de avaliação | Um processo de avaliação foi executado. |


### <a name="ingestion"></a>Ingestão
As operações de ingestão são problemas ocorridos durante a ingestão de dados, incluindo notificações sobre o alcance dos limites do espaço de trabalho do Azure Log Analytics. As condições de erro nessa categoria podem sugerir perda de dados, para que sejam particularmente importantes para monitorar. A tabela a seguir fornece detalhes sobre essas operações. Confira [Azure monitor limites de serviço](../service-limits.md#log-analytics-workspaces) para os limites de serviço de log Analytics espaços de trabalho.


| Operação | Nível | Detalhe | Artigo relacionado |
|:---|:---|:---|:---|
| Log personalizado | Erro do   | Limite de colunas de campos personalizados atingido. | [Limites do serviço Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Log personalizado | Erro do   | Falha na ingestão de logs personalizados. | |
| Metadata. | Erro do | Erro de configuração detectado. | |
| Coleta de dados | Erro do   | Os dados foram descartados porque a solicitação foi criada antes do número de dias definidos. | [Gerenciar o uso e os custos com logs do Azure Monitor](manage-cost-storage.md#alert-when-daily-cap-reached)
| Coleta de dados | Info    | A configuração da máquina de coleta foi detectada.| |
| Coleta de dados | Info    | Coleta de dados iniciada devido ao novo dia. | [Gerenciar o uso e os custos com logs do Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Coleta de dados | Aviso | A coleta de dados foi interrompida devido a um limite diário atingido.| [Gerenciar o uso e os custos com logs do Azure Monitor](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Processamento de dados | Erro do   | Formato JSON inválido. | [Enviar dados de log para o Azure Monitor com a API do Coletor de Dados HTTP (visualização pública)](data-collector-api.md#request-body) | 
| Processamento de dados | Aviso | O valor foi cortado para o tamanho máximo permitido. | [Limites do serviço Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Processamento de dados | Aviso | Valor de campo cortado como limite de tamanho atingido. | [Limites do serviço Azure Monitor](../service-limits.md#log-analytics-workspaces) | 
| Taxa de ingestão | Info | O limite de taxa de ingestão está se aproximando de 70%. | [Limites do serviço Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Taxa de ingestão | Aviso | Limite de taxa de ingestão que está se aproximando do limite. | [Limites do serviço Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Taxa de ingestão | Erro do   | Limite de taxa atingido. | [Limites do serviço Azure Monitor](../service-limits.md#log-analytics-workspaces) |
| Armazenamento | Erro do   | Não é possível acessar a conta de armazenamento porque as credenciais usadas são inválidas.  |



   

## <a name="alert-rules"></a>Regras de alerta
Use [alertas de consulta de log](../platform/alerts-log-query.md) em Azure monitor para ser notificado proativamente quando um problema for detectado em seu espaço de trabalho do log Analytics. Você deve usar uma estratégia que permita que você responda em tempo hábil aos problemas, minimizando os custos. Sua assinatura é cobrada por cada regra de alerta com um custo, dependendo da frequência avaliada.

Uma estratégia recomendada é iniciar com duas regras de alerta com base no nível do problema. Use uma frequência curta, como a cada 5 minutos, para erros e uma frequência maior, como 24 horas para avisos. Como os erros indicam potencial perda de dados, você deseja respondê-los rapidamente para minimizar qualquer perda. Normalmente, os avisos indicam um problema que não requer atenção imediata, para que você possa examiná-los diariamente.

Use o processo em [criar, exibir e gerenciar alertas de log usando Azure monitor](../platform/alerts-log.md) para criar as regras de alerta de log. As seções a seguir descrevem os detalhes de cada regra.


| Consulta | Valor limite | Período | Frequência |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Essas regras de alerta responderão as mesmas a todas as operações com erro ou aviso. Ao se familiarizar mais com as operações que estão gerando alertas, talvez você queira responder de forma diferente para operações específicas. Por exemplo, talvez você queira enviar notificações para pessoas diferentes para operações específicas. 

Para criar uma regra de alerta para uma operação específica, use uma consulta que inclua as colunas **categoria** e **operação** . 

O exemplo a seguir cria um alerta de aviso quando a taxa de volume de ingestão atingiu 80% do limite.

- Destino: Selecione seu espaço de trabalho Log Analytics
- Critérios:
  - Nome do sinal: Pesquisa de logs personalizada
  - Consulta de pesquisa: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Baseado em: Número de resultados
  - Condição: Maior que
  - Limite: 0
  - Período: 5 (minutos)
  - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite diário de dados atingido
- Gravidade: Aviso (Sev 1)


O exemplo a seguir cria um alerta de aviso quando a coleta de dados atingiu o limite diário. 

- Destino: Selecione seu espaço de trabalho Log Analytics
- Critérios:
  - Nome do sinal: Pesquisa de logs personalizada
  - Consulta de pesquisa: `_LogOperation | where Category == "Ingestion" | where Operation == "Data Collection" | where Level == "Warning"`
  - Baseado em: Número de resultados
  - Condição: Maior que
  - Limite: 0
  - Período: 5 (minutos)
  - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite diário de dados atingido
- Gravidade: Aviso (Sev 1)



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [alertas de log](alerts-log.md).
- [Coletar dados de auditoria de consulta](../log-query/query-audit.md) para seu espaço de trabalho.