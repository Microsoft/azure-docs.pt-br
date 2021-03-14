---
title: Exibir métricas com Azure Monitor
titleSuffix: Azure Digital Twins
description: Consulte como exibir as métricas de gêmeos digitais do Azure no Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434059"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Solução de problemas do Azure digital gêmeos: métricas

As métricas descritas neste artigo fornecem informações sobre o estado dos recursos de gêmeos digital do Azure em sua assinatura do Azure. As métricas do Azure digital gêmeos ajudam a avaliar a integridade geral do serviço gêmeos do Azure digital e os recursos conectados a ele. Essas estatísticas voltadas para o usuário ajudam a ver o que está acontecendo com o gêmeos digital do Azure e ajudam a executar a análise da causa raiz sobre problemas sem a necessidade de contatar o suporte do Azure.

As métricas são habilitadas por padrão. Você pode exibir as métricas do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Como exibir as métricas do gêmeos digital do Azure

1. Crie uma instância de gêmeos digital do Azure. Você pode encontrar instruções sobre como configurar uma instância do gêmeos digital do Azure em [*como: configurar uma instância e uma autenticação*](how-to-set-up-instance-portal.md).

2. Localize sua instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com) (você pode abrir a página para ela digitando seu nome na barra de pesquisa do Portal). 

    No menu da instância, selecione **métricas**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Captura de tela mostrando a página de métricas do Azure digital gêmeos":::

    Esta página exibe as métricas para sua instância do gêmeos digital do Azure. Você também pode criar exibições personalizadas de suas métricas selecionando aquelas que deseja ver na lista.
    
3. Você pode optar por enviar seus dados de métricas para um ponto de extremidade de hubs de eventos ou uma conta de armazenamento do Azure selecionando **configurações de diagnóstico** no menu e, em seguida, **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

    Para obter mais informações sobre esse processo, consulte [*solução de problemas: configurar o diagnóstico*](troubleshoot-diagnostics.md).

4. Você pode optar por configurar alertas para seus dados de métricas selecionando **alertas** no menu e **+ nova regra de alerta**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Captura de tela mostrando a página alertas e o botão para adicionar":::

    Para obter mais informações sobre esse processo, consulte [*solução de problemas: configurar alertas*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Métricas do gêmeos digital do Azure e como usá-las

O Azure digital gêmeos fornece várias métricas para fornecer uma visão geral da integridade da sua instância e de seus recursos associados. Você também pode combinar informações de várias métricas para pintar uma imagem maior do estado da sua instância. 

As tabelas a seguir descrevem as métricas controladas por cada instância do gêmeos digital do Azure e como cada métrica se relaciona com o status geral da instância.

#### <a name="metrics-for-tracking-service-limits"></a>Métricas para rastrear limites de serviço

Você pode configurar essas métricas para controlar quando está se aproximando de um [limite de serviço publicado](reference-service-limits.md#functional-limits) para algum aspecto da sua solução. 

Para configurar isso, use o recurso [alertas](troubleshoot-alerts.md) no Azure monitor. Você pode definir limites para essas métricas para que receba um alerta quando uma métrica atingir uma determinada porcentagem de seu limite publicado.

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Contagem de entrelaçamentos (versão prévia) | Contagem | Total | Número total de gêmeos na instância do gêmeos digital do Azure. Use essa métrica para determinar se você está se aproximando do [limite de serviço](reference-service-limits.md#functional-limits) para o número máximo de gêmeos permitidas por instância. |  Nenhum |
| ModelCount | Contagem de modelos (visualização) | Contagem | Total | Número total de modelos na instância do gêmeos digital do Azure. Use essa métrica para determinar se você está se aproximando do [limite de serviço](reference-service-limits.md#functional-limits) para o número máximo de modelos permitidos por instância. | Nenhum |

#### <a name="api-request-metrics"></a>Métricas de solicitação de API

Métricas que têm a ver com solicitações de API:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Solicitações da API | Contagem | Total | O número de solicitações de API feitas para operações de leitura, gravação, exclusão e consulta gêmeos digitais. |  Authentication <br>Operacional <br>Protocolo <br>Código de status, <br>Classe de código de status, <br>Texto de status |
| ApiRequestsFailureRate | Taxa de falha de solicitações de API | Porcentagem | Média | A porcentagem de solicitações de API que o serviço recebe para sua instância que fornece um código de resposta de erro interno (500) para operações de leitura, gravação, exclusão e consulta de gêmeos digital. | Authentication <br>Operacional <br>Protocolo <br>Código de status, <br>Classe de código de status, <br>Texto de status
| ApiRequestsLatency | Latência de solicitações de API | Milissegundos | Média | O tempo de resposta para solicitações de API. Isso se refere ao tempo de quando a solicitação é recebida pelo Azure digital gêmeos até que o serviço envie um resultado de êxito/falha para operações de leitura, gravação, exclusão e consulta de gêmeos digital. | Authentication <br>Operacional <br>Protocolo |

#### <a name="billing-metrics"></a>Métricas de cobrança

Métricas que têm a ver com a cobrança:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operações de API de cobrança | Contagem | Total | Métrica de cobrança para a contagem de todas as solicitações de API feitas no serviço de Gêmeosção digital do Azure. | ID de Medidor |
| BillingMessagesProcessed | Mensagens de cobrança processadas | Contagem | Total | Métrica de cobrança para o número de mensagens enviadas do Azure digital gêmeos para pontos de extremidade externos.<br><br>Para ser considerado uma única mensagem para fins de cobrança, uma carga não deve ter mais de 1 KB. Cargas maiores que isso serão contadas como mensagens adicionais em incrementos de 1 KB (portanto, uma mensagem entre 1 e 2 KB será contada como duas mensagens, entre 2 e 3 KB serão 3 mensagens e assim por diante).<br>Essa restrição também se aplica a respostas – portanto, uma chamada que retorna 1,5 KB no corpo da resposta, por exemplo, será cobrada como duas operações. | ID de Medidor |
| BillingQueryUnits | Unidades de consulta de cobrança | Contagem | Total | O número de unidades de consulta, uma medida interna computada de uso de recursos de serviço, consumida para executar consultas. Também há uma API auxiliar disponível para medir as unidades de consulta: [QueryChargeHelper Class](/dotnet/api/azure.digitaltwins.core.querychargehelper) | ID de Medidor |

Para obter mais detalhes sobre como o Azure digital gêmeos é cobrado, consulte [*preços do Azure digital gêmeos*](https://azure.microsoft.com/pricing/details/digital-twins/).

#### <a name="ingress-metrics"></a>Métricas de entrada

Métricas que têm a ver com a entrada de dados:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Eventos de entrada | Contagem | Total | O número de eventos de telemetria de entrada no Azure digital gêmeos. | Resultado |
| IngressEventsFailureRate | Taxa de falha de eventos de entrada | Porcentagem | Média | A porcentagem de eventos de telemetria de entrada para os quais o serviço retorna um código de resposta de erro interno (500). | Resultado |
| IngressEventsLatency | Latência de eventos de entrada | Milissegundos | Média | A hora de quando um evento chega quando está pronto para ser enviado pelo gêmeos digital do Azure, em que ponto o serviço envia um resultado de êxito/falha. | Resultado |

#### <a name="routing-metrics"></a>Métricas de roteamento

Métricas que têm a ver com o roteamento:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Mensagens roteadas | Contagem | Total | O número de mensagens roteadas para um serviço do Azure de ponto de extremidade, como o Hub de eventos, o barramento de serviço ou a grade de eventos. | Tipo de ponto de extremidade, <br>Resultado |
| RoutingFailureRate | Taxa de falha de roteamento | Porcentagem | Média | A porcentagem de eventos que resultam em um erro à medida que são roteados do Azure digital gêmeos para um serviço do Azure de ponto de extremidade, como o Hub de eventos, o barramento de serviço ou a grade de eventos. | Tipo de ponto de extremidade, <br>Resultado |
| RoutingLatency | Latência de roteamento | Milissegundos | Média | Tempo decorrido entre um evento sendo roteado do Azure digital gêmeos para quando ele é Postado para o serviço do Azure do ponto de extremidade, como o Hub de eventos, o barramento de serviço ou a grade de eventos. | Tipo de ponto de extremidade, <br>Resultado |

## <a name="dimensions"></a>Dimensões

As dimensões ajudam a identificar mais detalhes sobre as métricas. Algumas das métricas de roteamento fornecem informações por ponto de extremidade. A tabela a seguir lista os valores possíveis para essas dimensões.

| Dimensão | Valores |
| --- | --- |
| Autenticação | OAuth |
| Operação (para solicitações de API) | Microsoft. DigitalTwins/DigitalTwins/Delete, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/Read, <br>Microsoft. DigitalTwins/eventroutes/Read, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/Delete, <br>Microsoft. DigitalTwins/Models/Read, <br>Microsoft. DigitalTwins/Models/Write, <br>Microsoft. DigitalTwins/Models/Delete, <br>Microsoft. DigitalTwins/consulta/ação |
| Tipo de Ponto de Extremidade | Grade de eventos, <br>Hub de eventos, <br>Barramento de Serviço |
| Protocolo | HTTPS |
| Resultado | Êxito <br>Falha |
| Código de status | 200, 404, 500 e assim por diante. |
| Classe de código de status | 2xx, 4xx, 5xx e assim por diante. |
| Texto de status | Erro interno do servidor, não encontrado e assim por diante. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar as métricas gravadas para o Azure digital gêmeos, consulte [*solução de problemas: configurar o diagnóstico*](troubleshoot-diagnostics.md).
