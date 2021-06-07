---
title: Ver métricas com o Azure Monitor
titleSuffix: Azure Digital Twins
description: Saiba como ver as métricas dos Gêmeos Digitais do Azure no Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434059"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Solução de problemas dos Gêmeos Digitais do Azure: métricas

As métricas descritas neste artigo mostram informações sobre o estado dos recursos dos Gêmeos Digitais do Azure em sua assinatura do Azure. As métricas dos Gêmeos Digitais do Azure ajudam a avaliar a integridade geral do serviço e dos recursos conectados a ele. Com essas estatísticas voltadas para usuários, você vê o que está acontecendo com os Gêmeos Digitais do Azure e análise a causa raiz de problemas sem entrar em contato com o Suporte do Azure.

As métricas são habilitadas por padrão. Você pode ver as métricas dos Gêmeos Digitais do Azure no [portal do Azure](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Como ver as métricas dos Gêmeos Digitais do Azure

1. Crie uma instância dos Gêmeos Digitais do Azure. Veja as instruções de configuração de instâncias dos Gêmeos Digitais do Azure em [*Instruções: configurar uma instância e autenticação*](how-to-set-up-instance-portal.md).

2. Localize a instância dos Gêmeos Digitais do Azure no [portal do Azure](https://portal.azure.com) (para abrir a página, digite o nome dela na barra de pesquisa do portal). 

    No menu da instância, selecione **Métricas**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Captura de tela que mostra a página de métricas dos Gêmeos Digitais do Azure":::

    Esta página exibe as métricas da instância dos Gêmeos Digitais do Azure. Você também pode selecionar algumas métricas na lista para criar exibições personalizadas.
    
3. Você pode enviar os dados de métricas a um ponto de extremidade dos Hubs de Eventos ou uma conta do Armazenamento do Microsoft Azure. Para isso, selecione **Configurações de diagnóstico** no menu e **Adicionar configuração de diagnóstico**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Captura de tela mostrando a página de configurações de diagnóstico e o botão para adicionar":::

    Veja mais informações sobre esse processo em [*Solução de problemas: configurar o diagnóstico*](troubleshoot-diagnostics.md).

4. Para configurar alertas para os dados de métricas, selecione **Alertas** no menu e **+ Nova regra de alerta**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Captura de tela que mostra a página Alertas e o botão para adicionar":::

    Veja mais informações sobre esse processo em [*Solução de problemas: configurar alertas*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Métricas dos Gêmeos Digitais do Azure e como usá-las

Os Gêmeos Digitais do Azure oferecem várias métricas que apresentam uma visão geral da integridade de uma instância e dos recursos associados. Você também pode combinar informações de várias métricas para ter uma ideia mais ampla do estado da instância. 

As tabelas a seguir contêm descrições das métricas controladas pelas instâncias dos Gêmeos Digitais do Azure e da relação de cada métrica com o status geral da instância.

#### <a name="metrics-for-tracking-service-limits"></a>Métricas para acompanhar limites de serviço

Você pode configurar essas métricas para saber quando está prestes a atingir um [limite de serviço publicado](reference-service-limits.md#functional-limits) de algum aspecto da solução. 

Para configurar isso, use o recurso [alertas](troubleshoot-alerts.md) no Azure Monitor. Você pode definir limites para essas métricas para receber um alerta quando uma métrica atingir um percentual do limite publicado.

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Contagem de gêmeos (versão prévia) | Contagem | Total | Número total de gêmeos na instância dos Gêmeos Digitais do Azure. Use essa métrica para saber se você está se aproximando do [limite de serviço](reference-service-limits.md#functional-limits) do número máximo de gêmeos permitidos por instância. |  Nenhum |
| ModelCount | Contagem de modelos (versão prévia) | Contagem | Total | Número total de modelos na instância dos Gêmeos Digitais do Azure. Use essa métrica para saber se você está se aproximando do [limite de serviço](reference-service-limits.md#functional-limits) do número máximo de modelos permitidos por instância. | Nenhum |

#### <a name="api-request-metrics"></a>Métricas de solicitação de API

Métricas que têm a ver com solicitações de API:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Solicitações da API | Contagem | Total | O número de solicitações de API feitas para operações de leitura, gravação, exclusão e consulta dos Gêmeos Digitais. |  Autenticação, <br>Operação, <br>Protocolo, <br>Código de status, <br>Classe do código de status, <br>Texto de status |
| ApiRequestsFailureRate | Taxa de falhas das solicitações de API | Porcentagem | Média | O percentual de solicitações de API da instância recebidas pelo serviço que resultam no código de resposta de erro interno (500), referentes a operações de leitura, gravação, exclusão e consulta dos Gêmeos Digitais. | Autenticação, <br>Operação, <br>Protocolo, <br>Código de status, <br>Classe do código de status, <br>Texto de status
| ApiRequestsLatency | Latência das solicitações a API | Milissegundos | Média | O tempo de resposta das solicitações de API. Esse é o tempo desde o momento em que os Gêmeos Digitais do Azure recebem a solicitação de leitura, gravação, exclusão ou consulta, até o momento em que o serviço envia a mensagem de êxito/falha. | Autenticação, <br>Operação, <br>Protocolo |

#### <a name="billing-metrics"></a>Métricas de cobrança

Métricas que têm a ver com a cobrança:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operações da API de cobrança | Contagem | Total | Métrica de cobrança da contagem de todas as solicitações de API feitas no serviço de Gêmeos Digitais do Azure. | ID de Medidor |
| BillingMessagesProcessed | Mensagens de cobrança processadas | Contagem | Total | Métrica de cobrança do número de mensagens enviadas dos Gêmeos Digitais do Azure a pontos de extremidade externos.<br><br>Para ser considerado uma mensagem para fins de cobrança, o conteúdo deve ter menos de 1 KB. Conteúdos maiores que isso serão contados como mensagens adicionais em incrementos de 1 KB (portanto, uma mensagem entre 1 e 2 KB será contada como duas mensagens, entre 2 e 3 KB serão três mensagens e assim por diante).<br>Essa restrição também se aplica a respostas, portanto, uma chamada que retorna 1,5 KB no corpo da resposta, por exemplo, será cobrada como duas operações. | ID de Medidor |
| BillingQueryUnits | Unidades de consulta de cobrança | Contagem | Total | O número de unidades de consulta, uma medida de uso de recursos de serviço calculada internamente, consumidas para executar consultas. Também há uma API auxiliar disponível para medir as unidades de consulta: [classe QueryChargeHelper](/dotnet/api/azure.digitaltwins.core.querychargehelper) | ID de Medidor |

Veja mais detalhes sobre a cobrança dos Gêmeos Digitais do Azure em [*Preços dos Gêmeos Digitais do Azure*](https://azure.microsoft.com/pricing/details/digital-twins/).

#### <a name="ingress-metrics"></a>Métricas de entrada

Métricas que têm a ver com a entrada de dados:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Eventos de entrada | Contagem | Total | O número de eventos de telemetria de entrada nos Gêmeos Digitais do Azure. | Resultado |
| IngressEventsFailureRate | Taxa de falha de eventos de entrada | Porcentagem | Média | O percentual de eventos de telemetria de entrada para os quais o serviço retornou um código de resposta de erro interno (500). | Resultado |
| IngressEventsLatency | Latência de eventos de entrada | Milissegundos | Média | O tempo entre a chegada do evento e o momento quando ele está pronto para sair dos Gêmeos Digitais do Azure, momento no qual o serviço envia o resultado êxito/falha. | Resultado |

#### <a name="routing-metrics"></a>Métricas de roteamento

Métricas que têm a ver com o roteamento:

| Métrica | Nome de exibição da métrica | Unidade | Tipo de agregação| Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Mensagens roteadas | Contagem | Total | O número de mensagens roteadas a um serviço do Azure de ponto de extremidade, como o Hub de Eventos, o Barramento de Serviço ou a Grade de Eventos. | Tipo de ponto de extremidade, <br>Resultado |
| RoutingFailureRate | Taxa de falha de roteamento | Porcentagem | Média | O percentual de eventos que resultam em erro durante o roteamento dos Gêmeos Digitais do Azure até um serviço do Azure de ponto de extremidade, como o Hub de Eventos, o Barramento de Serviço ou a Grade de Eventos. | Tipo de ponto de extremidade, <br>Resultado |
| RoutingLatency | Latência de roteamento | Milissegundos | Média | O tempo entre o roteamento do evento nos Gêmeos Digitais do Azure até a publicação dele no serviço do Azure de ponto de extremidade, como o Hub de Eventos, o Barramento de Serviço ou a Grade de Eventos. | Tipo de ponto de extremidade, <br>Resultado |

## <a name="dimensions"></a>Dimensões

As dimensões ajudam a identificar mais detalhes sobre as métricas. Algumas das métricas de roteamento apresentam informações por ponto de extremidade. A tabela a seguir contém os valores possíveis dessas dimensões.

| Dimensão | Valores |
| --- | --- |
| Autenticação | OAuth |
| Operação (para solicitações de API) | Microsoft.DigitalTwins/digitaltwins/delete, <br>Microsoft.DigitalTwins/digitaltwins/write, <br>Microsoft.DigitalTwins/digitaltwins/read, <br>Microsoft.DigitalTwins/eventroutes/read, <br>Microsoft.DigitalTwins/eventroutes/write, <br>Microsoft.DigitalTwins/eventroutes/delete, <br>Microsoft.DigitalTwins/models/read, <br>Microsoft.DigitalTwins/models/write, <br>Microsoft.DigitalTwins/models/delete, <br>Microsoft.DigitalTwins/query/action |
| Tipo de Ponto de Extremidade | Grade de Eventos, <br>Hub de Evento, <br>Barramento de Serviço |
| Protocolo | HTTPS |
| Resultado | Êxito, <br>Falha |
| Código de status | 200, 404, 500 e assim por diante. |
| Classe do código de status | 2xx, 4xx, 5xx e assim por diante. |
| Texto de status | Erro interno do servidor, não encontrado e assim por diante. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o gerenciamento das métricas gravadas dos Gêmeos Digitais do Azure, confira [*Solução de problemas: configurar o diagnóstico*](troubleshoot-diagnostics.md).
