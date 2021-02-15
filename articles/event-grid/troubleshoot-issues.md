---
title: Solucionar problemas de grade de eventos
description: Este artigo fornece diferentes maneiras de solucionar problemas da grade de eventos do Azure
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c52ba8561c10dd94ec6ef51c78b8534c6c58e96
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416939"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Solucionar problemas da grade de eventos do Azure
Este artigo fornece informações que ajudam a solucionar problemas de grade de eventos do Azure. 

## <a name="diagnostic-logs"></a>Logs de diagnóstico
Habilite as configurações de diagnóstico para os tópicos ou os domínios da grade de eventos para capturar e exibir os logs de falha de publicação e entrega. Para obter mais informações, consulte [logs de diagnóstico](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Métricas
Você pode exibir as métricas para tópicos e assinaturas da grade de eventos e criar alertas nelas. Para obter mais informações, consulte [métricas da grade de eventos](monitor-event-delivery.md).

## <a name="alerts"></a>Alertas
Criar alertas sobre as métricas da grade de eventos do Azure e as operações do log de atividades. Para obter mais informações, consulte [alertas sobre métricas de grade de eventos e logs de atividade](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problemas de validação de assinatura
Durante a criação da assinatura do evento, você pode receber uma mensagem de erro dizendo que a validação do ponto de extremidade fornecido falhou. Para solucionar problemas de validação de assinatura, confira [solucionar problemas de validações de assinatura na grade de eventos](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problemas de conectividade de rede
Há várias razões para os aplicativos cliente não poderem se conectar a um tópico/domínio da grade de eventos. Os problemas de conectividade que você enfrenta podem ser permanentes ou transitórios. Para saber como resolver esses problemas, consulte [solucionar problemas de conectividade](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Códigos do Erro
Se você receber mensagens de erro com códigos de erro como 400, 409 e 403, consulte [solucionar erros de grade de eventos](troubleshoot-errors.md). 

## <a name="distributed-tracing-net"></a>Rastreamento distribuído (.NET)
A biblioteca .NET da grade de eventos dá suporte à distribuição de rastreamento. Para aderir às [diretrizes da especificação do CloudEvents](https://github.com/cloudevents/spec/blob/master/extensions/distributed-tracing.md) sobre a distribuição de rastreamento, a biblioteca define o `traceparent` e `tracestate` na [extensãoattributes](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/src/Customization/CloudEvent.cs#L126) de um `CloudEvent` quando o rastreamento distribuído está habilitado. Para saber mais sobre como habilitar o rastreamento distribuído em seu aplicativo, dê uma olhada na [documentação de rastreamento distribuído](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)do SDK do Azure.

### <a name="sample"></a>Amostra
Consulte o [exemplo de contador de linha](/samples/azure/azure-sdk-for-net/line-counter/). Este aplicativo de exemplo ilustra o uso de armazenamento, hubs de eventos e clientes de grade de eventos junto com ASP.NET Core integração, rastreamento distribuído e serviços hospedados. Ele permite que os usuários carreguem um arquivo em um blob, que dispara um evento de hubs de eventos contendo o nome do arquivo. O processador de hubs de eventos recebe o evento e, em seguida, o aplicativo baixa o blob e conta o número de linhas no arquivo. O aplicativo exibe um link para uma página que contém a contagem de linhas. Quando o link é clicado, um CloudEvent contendo o nome do arquivo é publicado usando a grade de eventos.

## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste seu problema no [fórum do Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou abra um [tíquete de suporte](https://azure.microsoft.com/support/options/). 
