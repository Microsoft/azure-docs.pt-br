---
title: Entender o roteamento de mensagens do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – como usar o roteamento de mensagens para enviar mensagens de dispositivo para nuvem. Inclui informações de como enviar dados telemétricos e dados que não são de telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: 07bbd50dbc415b86aa0c511d46ead9f0612df107
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642501"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usar o roteamento de mensagens do Hub IoT para enviar mensagens de dispositivo para nuvem para diferentes pontos de extremidade

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Roteamento de mensagens permite que você envie mensagens de dispositivos para serviços de nuvem de maneira automatizada, escalonável e confiável. O roteamento de mensagens pode ser usado para: 

* **Enviar mensagens de telemetria do dispositivo, bem como eventos** , eventos de ciclo de vida do dispositivo, eventos de alteração do dispositivo de troca e eventos de alteração de troca digital para os pontos de extremidade internos e personalizados. Saiba mais sobre [pontos de extremidade de roteamento](#routing-endpoints). Para saber mais sobre os eventos enviados de dispositivos de Plug and Play IoT, confira [entender o iot plug and Play Digital gêmeos](../iot-pnp/concepts-digital-twin.md).

* **Filtrar dados antes de roteá-los para vários pontos de extremidade** aplicando consultas avançadas. O roteamento de mensagens permite consultar as propriedades da mensagem e o corpo da mensagem, bem como as marcas do dispositivo gêmeo e as propriedades do dispositivo gêmeo. Saiba mais sobre como usar [consultas no roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O Hub IoT precisa de acesso de gravação a esses pontos de extremidade para que o direcionamento de mensagens funcione. Se você configurar os pontos de extremidade por meio do Portal do Azure, as permissões necessárias serão adicionadas para você. Certifique-se de configurar os serviços para dar suporte à taxa de transferência esperada. Por exemplo, se você estiver usando hubs de eventos como um ponto de extremidade personalizado, deverá configurar as **unidades de produtividade** para esse Hub de eventos para que ele possa manipular a entrada de eventos que você planeja enviar por meio do roteamento de mensagens do Hub IOT. Da mesma forma, ao usar uma fila do barramento de serviço como um ponto de extremidade, você deve configurar o **tamanho máximo** para garantir que a fila possa conter todos os dados inseridos, até que eles sejam reinseridos pelos consumidores. Quando você configurar pela primeira vez sua solução IoT, talvez seja necessário monitorar seus pontos de extremidade adicionais ao configurar e fazer eventuais ajustes necessários para a carga real.

O Hub IoT define um [formato comum](iot-hub-devguide-messages-construct.md) para todas as mensagens de dispositivo para nuvem que permite a interoperabilidade entre protocolos. Se uma mensagem corresponder a várias rotas e todas apontarem para o mesmo ponto de extremidade, o Hub IoT entregará mensagens a esse ponto apenas uma vez. Portanto, você não precisa configurar a eliminação de duplicação nem no tópico nem na fila do Barramento de Serviço. Em filas particionados, a afinidade de partição garante a ordenação das mensagens. Use este tutorial para aprender a [configurar o roteamento de mensagem](tutorial-routing.md).

## <a name="routing-endpoints"></a>Pontos de extremidade de roteamento

Um Hub IoT tem um padrão de ponto de extremidade interno (**mensagens/eventos**) que é compatível com Hubs de Eventos. Você pode criar [pontos de extremidade personalizados](iot-hub-devguide-endpoints.md#custom-endpoints) para encaminhar mensagens vinculando outros serviços em sua assinatura ao Hub IoT. 

Cada mensagem é roteada para todos os pontos de extremidade cujas consultas de roteamento ele corresponde. Em outras palavras, uma mensagem pode ser roteada para vários pontos de extremidade.

Se seu ponto de extremidade personalizado tiver configurações de firewall, considere o uso da exceção de terceira parte confiável da Microsoft, para dar acesso ao Hub IoT ao ponto de extremidade específico- [armazenamento do Azure](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing), [hubs de eventos do](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) Azure e [barramento de serviço do Azure](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing). Isso está disponível em regiões selecionadas para hubs IoT com [identidade de serviço gerenciada](./virtual-network-support.md).

O Hub IoT atualmente dá suporte aos seguintes pontos de extremidade:

 - Ponto de extremidade interno
 - Armazenamento do Azure
 - Filas do Barramento de Serviço e Tópicos do Barramento de Serviço
 - Hubs de Eventos

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Ponto de extremidade interno como um ponto de extremidade de roteamento

Você pode usar [SDKs e integração padrão dos Hubs de Eventos](iot-hub-devguide-messages-read-builtin.md) para receber mensagens de dispositivo para nuvem do ponto de extremidade interno (**mensagens/eventos**). Depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade.

## <a name="azure-storage-as-a-routing-endpoint"></a>Armazenamento do Azure como um ponto de extremidade de roteamento

Há dois serviços de armazenamento que o Hub IoT pode rotear mensagens para as contas-- [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md) e [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). Contas de Azure Data Lake Storage são contas de armazenamento [hierárquicas habilitadas para namespaces](../storage/blobs/data-lake-storage-namespace.md)criadas com base no armazenamento de BLOBs. Ambos usam BLOBs para seu armazenamento.

O Hub IoT dá suporte à gravação de dados no armazenamento do Azure no formato [Apache Avro](https://avro.apache.org/) , bem como no formato JSON. O padrão é AVRO. Ao usar a codificação JSON, você deve definir o contentType como **Application/JSON** e ContentEncoding como **UTF-8** nas [Propriedades do sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)de mensagens. Esses dois valores não diferenciam maiúsculas de minúsculas. Se a codificação de conteúdo não estiver definida, o Hub IoT gravará as mensagens no formato codificado 64 base.

O formato de codificação só pode ser definido quando o ponto de extremidade do armazenamento de BLOBs é configurado; Ele não pode ser editado para um ponto de extremidade existente. Para alternar os formatos de codificação para um ponto de extremidade existente, você precisará excluir e recriar o ponto de extremidade personalizado com o formato desejado. Uma estratégia útil pode ser criar um novo ponto de extremidade personalizado com o formato de codificação desejado e adicionar uma rota paralela ao ponto de extremidade. Dessa forma, você pode verificar seus dados antes de excluir o ponto de extremidade existente.

Você pode selecionar o formato de codificação usando o Hub IoT criar ou atualizar a API REST, especificamente o [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), o portal do Azure, o [CLI do Azure](/cli/azure/iot/hub/routing-endpoint)ou o [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint). A imagem a seguir mostra como selecionar o formato de codificação no portal do Azure.

![Codificação de ponto de extremidade de armazenamento de BLOB](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

O Hub IoT armazena em lotes mensagens e grava dados no armazenamento sempre que o lote atinge um determinado tamanho ou um determinado período de tempo decorrido. O Hub IoT segue a seguinte convenção de nomenclatura de arquivo padrão:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Você pode usar qualquer convenção de nomenclatura de arquivo, no entanto, é necessário usar todos os tokens listados. O Hub IoT gravará em um blob vazio se não houver nenhum dado para gravação.

É recomendável listar os BLOBs ou arquivos e iterar sobre eles, para garantir que todos os BLOBs ou arquivos sejam lidos sem fazer nenhuma suposição de partição. O intervalo de partição potencialmente pode ser alterado durante um [failover iniciado pela Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) ou [failover manual](iot-hub-ha-dr.md#manual-failover) do Hub IoT. Você pode usar a [API listar BLOBs](/rest/api/storageservices/list-blobs) para enumerar a lista de BLOBs ou [listar ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/path) para a lista de arquivos. Consulte o exemplo a seguir como orientação.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Para criar um Azure Data Lake conta de armazenamento compatível com Gen2, crie uma nova conta de armazenamento V2 e selecione *habilitado* no campo *namespace hierárquico* na guia **avançado** , conforme mostrado na imagem a seguir:

![Selecione Azure data Lake armazenamento Gen2](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Filas do barramento de serviço e tópicos do barramento de serviço como um ponto de extremidade de roteamento

As filas e os tópicos do Barramento de Serviço utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

## <a name="event-hubs-as-a-routing-endpoint"></a>Hubs de eventos como um ponto de extremidade de roteamento

Além do ponto de extremidade compatível com os Hubs de Eventos internos, você também pode encaminhar dados para pontos de extremidade personalizados do tipo Hubs de Eventos. 

## <a name="reading-data-that-has-been-routed"></a>Lendo dados que foram encaminhados

Configure uma rota seguindo este [tutorial](tutorial-routing.md).

Use os tutoriais a seguir para saber como ler a mensagem de um ponto de extremidade.

* Lendo do [ponto de extremidade interno](quickstart-send-telemetry-node.md)

* Lendo do [Armazenamento de Blobs](../storage/blobs/storage-blob-event-quickstart.md)

* Lendo dos [hubs de eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Leitura de [filas do barramento de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Ler dos [Tópicos do Barramento de Serviço](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)


## <a name="fallback-route"></a>Rota de fallback

A rota de fallback envia todas as mensagens que não atendem às condições de consulta em nenhuma das rotas existentes para os Hubs de Eventos internos em (**mensagens/eventos**), compatíveis com [Hubs de Eventos](../event-hubs/index.yml). Se o roteamento de mensagens estiver habilitado, você poderá habilitar a funcionalidade de rota de fallback. Depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade. Se não houver nenhuma rota para o ponto de extremidade interno e houver uma rota de fallback habilitada, somente as mensagens que não corresponderem a nenhuma condição de consulta nas rotas serão enviadas ao ponto de extremidade interno. Além disso, se todas as rotas existentes forem excluídas, a rota de fallback precisará ser habilitada para receber todos os dados no ponto de extremidade interno.

Você pode habilitar/desabilitar a rota de fallback na folha de roteamento de mensagens portal do Azure >. Você também pode usar o Azure Resource Manager para [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para usar um ponto de extremidade personalizado para a rota de fallback.

## <a name="non-telemetry-events"></a>Eventos que não são de telemetria

Além da telemetria do dispositivo, o roteamento de mensagens também permite o envio de eventos de alteração de dispositivos de troca, eventos de ciclo de vida de dispositivo e eventos de alteração de troca digital Por exemplo, se uma rota é criada com a fonte de dados definida como **eventos de alteração de dispositivo gêmeo**, o Hub IoT envia mensagens para o ponto de extremidade que contém a alteração no dispositivo gêmeo. Da mesma forma, se uma rota for criada com a fonte de dados definida como **eventos de ciclo de vida do dispositivo**, o Hub IOT enviará uma mensagem indicando se o dispositivo foi excluído ou criado. Por fim, como parte do [plug and Play de IOT do Azure](../iot-pnp/overview-iot-plug-and-play.md), um desenvolvedor pode criar rotas com a fonte de dados definida como **eventos de alteração de troca digital** e o Hub IOT envia mensagens sempre que uma propriedade de troca de alterações digitais é definida ou alterada, uma troca de dados digitais é substituída ou quando ocorre um evento de alteração para o dispositivo subjacente.

O [Hub IOT também se integra à grade de eventos do Azure](iot-hub-event-grid.md) para publicar eventos de dispositivo para dar suporte a integrações em tempo real e automação de fluxos de trabalho com base nesses eventos. Confira as principais [diferenças entre o roteamento de mensagens e Grade de Eventos](iot-hub-event-grid-routing-comparison.md) para saber o que funciona melhor para seu cenário.

## <a name="testing-routes"></a>Testando rotas

Ao criar uma rota ou editar uma rota existente, você deve testar a consulta de rota com uma mensagem de exemplo. Você pode testar rotas individuais ou todas as rotas de uma vez. Não será roteada nenhuma mensagem aos pontos de extremidade durante o teste. Portal do Azure, Azure Resource Manager, Azure PowerShell e CLI do Azure podem ser usados para teste. Os resultados ajudam a identificar se a mensagem de exemplo correspondeu à consulta, se a mensagem não correspondeu à consulta ou não foi possível executar o teste porque a sintaxe da mensagem de exemplo ou da consulta está incorreta. Para saber mais, confira [Testar Rota](/rest/api/iothub/iothubresource/testroute) e [Testar todas as rotas](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Ordenando garantias com pelo menos uma entrega

O roteamento de mensagens do Hub IoT garante pedidos e, pelo menos, uma entrega de mensagens para os pontos de extremidade. Isso significa que pode haver mensagens duplicadas e uma série de mensagens pode ser retransmitida respeitando a ordenação original da mensagem. Por exemplo, se a ordem de mensagem original for [1, 2, 3, 4], você poderá receber uma sequência de mensagens como [1, 2, 1, 2, 3, 1, 2, 3, 4]. A garantia de ordenação é que, se você já receber a mensagem [1], ela sempre será seguida de [2, 3, 4].

Para lidar com duplicatas de mensagens, é recomendável carimbar um identificador exclusivo nas propriedades do aplicativo da mensagem no ponto de origem, que geralmente é um dispositivo ou um módulo. O serviço que consome as mensagens pode lidar com mensagens duplicadas usando esse identificador.

## <a name="latency"></a>Latency

Ao rotear mensagens de telemetria do dispositivo para nuvem usando pontos de extremidade internos, haverá um pequeno aumento na latência de ponta a ponta após a criação da primeira rota.

Na maioria dos casos, o aumento médio na latência é menor que 500 ms. Você pode monitorar a latência usando a métrica do Hub IoT **Roteamento: latência de mensagem para mensagens/eventos** ou **d2c.endpoints.latency.builtIn.events**. Criar ou excluir qualquer rota após a primeira não afeta a latência de ponta a ponta.

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas

O Hub IoT fornece várias métricas relacionadas ao roteamento e aos pontos de extremidade para fornecer uma visão geral da integridade do seu hub e das mensagens enviadas. Para obter uma lista de todas as métricas do Hub IoT divididas por categoria funcional, consulte [métricas na referência de dados de monitoramento](monitor-iot-hub-reference.md#metrics). Você pode rastrear os erros que ocorrem durante a avaliação de uma consulta de roteamento e integridade do ponto de extremidade, conforme percebido pelo Hub IoT, com a [categoria **rotas** nos logs de recursos do Hub IOT](monitor-iot-hub-reference.md#routes). Para saber mais sobre como usar os logs de recursos e métricas com o Hub IoT, confira [monitorar o Hub IOT](monitor-iot-hub.md).

Você pode usar a integridade do [ponto de extremidade Get](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) da API REST para obter o [status de integridade](iot-hub-devguide-endpoints.md#custom-endpoints) dos pontos de extremidade.

Use o [Guia de solução de problemas para roteamento](troubleshoot-message-routing.md) para obter mais detalhes e suporte para a solução de problemas de roteamento.

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar rotas de mensagens, consulte [Processo de mensagens dispositivo para nuvem de Hub IoT usando as rotas](tutorial-routing.md).

* [Como enviar mensagens do dispositivo para nuvem](quickstart-send-telemetry-node.md)

* Para saber mais sobre os SDKs que você pode usar para enviar mensagens de dispositivo para a nuvem, confira [SDKs do Azure IoT](iot-hub-devguide-sdks.md).
