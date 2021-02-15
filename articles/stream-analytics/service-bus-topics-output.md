---
title: Resultados da saída dos tópicos do barramento de serviço de Azure Stream Analytics
description: Este artigo descreve os tópicos do barramento de serviço como saída para Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 584d73acf36d22f59fbbcb6dff8b2f53cbc7437d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014171"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Resultados da saída dos tópicos do barramento de serviço de Azure Stream Analytics

As filas do Barramento de Serviço fornecem um método de comunicação um-para-um do remetente para o destinatário. [Os tópicos do Barramento de Serviço](/previous-versions/azure/hh367516(v=azure.100)) fornecem uma forma de comunicação de um-para-muitos.

A tabela a seguir lista os nomes de propriedade e suas descrições para a criação de uma saída de tópico do Barramento de Serviço.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse tópico de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um contêiner para um conjunto de entidades de mensagens. Ao criar um novo hub de eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de dispositivos e serviços. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estão disponíveis a menos que uma assinatura seja criada, portanto, verifique se há uma ou mais assinaturas sob o tópico. |
| Nome da política de tópico |Ao criar um tópico do Barramento de Serviço, você também pode criar políticas de acesso compartilhado na guia **Configurar** do tópico. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço. |
| Formato de serialização do evento |O formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação |Se você estiver usando o formato CSV ou JSON, uma codificação deverá ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Colunas da propriedade | Opcional. Colunas separadas por vírgula que precisam ser anexadas como propriedades de usuário da mensagem de saída em vez do conteúdo. Há mais informações sobre esse recurso na seção [Propriedades de metadados personalizadas para saída](#custom-metadata-properties-for-output). |
| Colunas da Propriedade do Sistema | Opcional. Pares de valor-chave das Propriedades do Sistema e nomes de colunas correspondentes que precisam ser anexados à mensagem de saída em vez do conteúdo. |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="partitioning"></a>Particionamento

O particionamento é escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). A chave de partição é um valor inteiro exclusivo para cada partição. O número de gravadores de saída é o mesmo que o número de partições no tópico de saída.

## <a name="output-batch-size"></a>Tamanho do lote de saída

O tamanho máximo da mensagem é 256 KB por mensagem para a camada Standard e 1MB para a camada Premium. Para obter mais informações, consulte [limites do barramento de serviço](../service-bus-messaging/service-bus-quotas.md). Para otimizar, use um único evento por mensagem.

## <a name="custom-metadata-properties-for-output"></a>Personalizar propriedades de metadados para a saída

Você pode anexar colunas de consulta às suas mensagens de saída como propriedades de usuário. Essas colunas não entram no conteúdo. As propriedades estão presentes no formato de um dicionário na mensagem de saída. *Chave* é o nome da coluna e *valor* é o valor da coluna no dicionário de propriedades. Todos os tipos de dados do Stream Analytics têm suporte, exceto Registro e Matriz.

No exemplo a seguir, os campos `DeviceId` e `DeviceStatus` são adicionados aos metadados.

1. Use a seguinte consulta:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configure `DeviceId,DeviceStatus` como colunas de propriedade na saída.

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="Colunas da propriedade":::

A imagem a seguir é das propriedades de mensagem de saída esperadas inspecionadas no EventHub usando o [Gerenciador do barramento de serviço](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="Propriedades personalizadas do evento":::

## <a name="system-properties"></a>Propriedades do sistema

Você pode anexar colunas de consulta como [propriedades do sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) às suas mensagens da fila ou do tópico do Barramento de Serviço de saída. Essas colunas não entram no conteúdo, em vez disso, a [propriedade do sistema](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true#properties) BrokeredMessage correspondente é preenchida com os valores da coluna de consulta.
Estas propriedades do sistema têm suporte: `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc`.

Os valores de cadeia de caracteres dessas colunas são analisados como o tipo de valor de propriedade do sistema correspondente, e quaisquer falhas de análise são tratadas como erros de dados.
Esse campo é fornecido como um formato de objeto JSON. Os detalhes sobre esse formato são os seguintes:

* Entre chaves {}.
* Escritos em pares chave/valor.
* Chaves e valores devem ser cadeias de caracteres.
* A chave é o nome da propriedade do sistema, e o valor é o nome da coluna de consulta.
* Chaves e valores são separados por dois pontos.
* Cada par chave/valor é separado por uma vírgula.

Eis como usar essa propriedade:

* Consulta: `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Colunas da Propriedade do Sistema: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Isso define a `MessageId` nas mensagens de fila do barramento de serviço com os valores de `column1`, e a PartitionKey é definida com os valores de `column2`.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando a CLI do Azure](quick-create-azure-cli.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando um modelo do Resource Manager](quick-create-azure-resource-manager.md)
* [Início rápido: criar um trabalho de Stream Analytics usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Azure Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code](quick-create-visual-studio-code.md)