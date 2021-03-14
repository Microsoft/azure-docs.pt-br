---
title: Acessar métricas internas-Azure IoT Edge
description: Acesso remoto a métricas internas do IoT Edge de componentes de tempo de execução
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1a78db821c0fab01ad5d6752216a8f7682fb2c46
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200490"
---
# <a name="access-built-in-metrics"></a>Acessar métricas internas

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Os componentes de tempo de execução IoT Edge, IoT Edge Hub e agente de IoT Edge, produzem métricas internas no [formato Prometheus exposição](https://prometheus.io/docs/instrumenting/exposition_formats/). Acesse essas métricas remotamente para monitorar e entender a integridade de um dispositivo IoT Edge.

A partir da versão 1.0.10, as métricas são automaticamente expostas por padrão na **porta 9600** dos módulos **edgeHub** e **edgeAgent** ( `http://edgeHub:9600/metrics` e `http://edgeAgent:9600/metrics` ). Eles não são mapeados de porta para o host por padrão.

Acesse as métricas do host expondo e mapeando a porta de métricas do módulo `createOptions` . O exemplo a seguir mapeia a porta de métricas padrão para a porta 9601 no host:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Escolha números de porta de host diferentes e exclusivos se você estiver mapeando os pontos de extremidade de métricas de edgeHub e edgeAgent.

> [!NOTE]
> A variável de ambiente `httpSettings__enabled` não deve ser definida como para que as `false` métricas internas estejam disponíveis para coleta.
>
> As variáveis de ambiente que podem ser usadas para desabilitar as métricas são listadas no [documento do repositório do Azure/iotedge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).

## <a name="available-metrics"></a>Métricas disponíveis

As métricas contêm marcas para ajudar a identificar a natureza da métrica que está sendo coletada. Todas as métricas contêm as seguintes marcas:

| Marca | Descrição |
|-|-|
| iothub | O Hub ao qual o dispositivo está se comunicando |
| edge_device | A ID do dispositivo atual |
| instance_number | Um GUID que representa o tempo de execução atual. Ao reiniciar, todas as métricas serão redefinidas. Esse GUID torna mais fácil reconciliar as reinicializações. |

No formato Prometheus exposição, há quatro tipos principais de métrica: contador, medidor, histograma e resumo. Para obter mais informações sobre os diferentes tipos de métrica, consulte a [documentação tipos de métrica Prometheus](https://prometheus.io/docs/concepts/metric_types/).

As quantis fornecidas para as métricas internas de histograma e resumo são 0,1, 0,5, 0,9 e 0,99.

O módulo **edgeHub** produz as seguintes métricas:

| Nome | Dimensões | Descrição |
|-|-|-|
| `edgehub_gettwin_total` | `source` (origem da operação)<br> `id` (ID do módulo) | Tipo: contador<br> Número total de chamadas getentrelaçadas |
| `edgehub_messages_received_total` | `route_output` (saída que enviou a mensagem)<br> `id` | Tipo: contador<br> Número total de mensagens recebidas de clientes |
| `edgehub_messages_sent_total` | `from` (origem da mensagem)<br> `to` (destino da mensagem)<br>`from_route_output`<br> `to_route_input` (entrada de destino da mensagem)<br> `priority` (prioridade da mensagem para o destino) | Tipo: contador<br> Número total de mensagens enviadas aos clientes ou upstream<br> `to_route_input` está vazio quando o `to` está $upstream |
| `edgehub_reported_properties_total` | `target`(destino da atualização)<br> `id` | Tipo: contador<br> Total de chamadas de atualizações de propriedade relatadas |
| `edgehub_message_size_bytes` | `id`<br> | Tipo: Resumo<br> Tamanho da mensagem de clientes<br> Os valores podem ser relatados como `NaN` se nenhuma nova medida for relatada por um determinado período de tempo (atualmente, 10 minutos); para `summary` tipo, correspondendo `_count` e os `_sum` contadores serão emitidos. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Tipo: Resumo<br> Tempo gasto para operações de obtenção de entrelaçamento |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Tipo: Resumo<br> Tempo necessário para enviar uma mensagem |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Tipo: Resumo<br> Tempo necessário para processar uma mensagem da fila |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Tipo: Resumo<br> Tempo necessário para atualizar as propriedades relatadas |
| `edgehub_direct_method_duration_seconds` | `from` chamado<br> `to` distância | Tipo: Resumo<br> Tempo necessário para resolver uma mensagem direta |
| `edgehub_direct_methods_total` | `from`<br> `to` | Tipo: contador<br> Número total de mensagens diretas enviadas |
| `edgehub_queue_length` | `endpoint` (origem da mensagem)<br> `priority` (prioridade da fila) | Tipo: medidor<br> Comprimento atual da fila de edgeHub para uma determinada prioridade |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Tipo: contador<br> Número total de mensagens removidas por causa do motivo |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Tipo: contador<br> Número total de mensagens não confirmadas devido à falha de armazenamento |
| `edgehub_offline_count_total` | `id` | Tipo: contador<br> Número total de vezes que o edgeHub ficou offline |
| `edgehub_offline_duration_seconds`| `id` | Tipo: Resumo<br> O Hub de borda temporal estava offline |
| `edgehub_operation_retry_total` | `id`<br> `operation` (nome da operação) | Tipo: contador<br> Número total de vezes que as operações edgeHub foram repetidas |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (não autenticado)<br> | Tipo: contador<br> Número total de vezes que os clientes falharam ao se conectar ao edgeHub |

O módulo **edgeAgent** produz as seguintes métricas:

| Nome | Dimensões | Descrição |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Tipo: medidor<br> A quantidade de tempo que o módulo foi especificado na implantação e estava em estado de execução |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Tipo: medidor<br> A quantidade de tempo que o módulo foi especificado na implantação |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Tipo: contador<br> Número de vezes que o Docker edgeAgent pediu para iniciar o módulo |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Tipo: contador<br> Número de vezes que o Docker edgeAgent pediu para parar o módulo |
| `edgeAgent_command_latency_seconds` | `command` | Tipo: medidor<br> Quanto tempo levou o Docker para executar o comando fornecido. Os comandos possíveis são: criar, atualizar, remover, iniciar, parar, reiniciar |
| `edgeAgent_iothub_syncs_total` | | Tipo: contador<br> Número de vezes que o edgeAgent tentou sincronizar seu entrelaçado com iotHub, com êxito e sem êxito. Esse número inclui o agente que solicita uma notificação de entrelaçamento e de Hub de uma atualização de n º |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Tipo: contador<br> Número de vezes que o edgeAgent falhou ao sincronizar seu número de iotHub. |
| `edgeAgent_deployment_time_seconds` | | Tipo: contador<br> A quantidade de tempo que leva para concluir uma nova implantação depois de receber uma alteração. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Tipo: contador<br> Número de vezes que um método direto edgeAgent interno é chamado, como ping ou reinicialização. |
| `edgeAgent_host_uptime_seconds` | | Tipo: medidor<br> Por quanto tempo o host esteve ativado |
| `edgeAgent_iotedged_uptime_seconds` | | Tipo: medidor<br> Quanto tempo iotedged está em execução |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Tipo: medidor<br> Quantidade de espaço restante no disco |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Tipo: medidor<br> Tamanho do disco |
| `edgeAgent_used_memory_bytes` | `module_name` | Tipo: medidor<br> Quantidade de RAM usada por todos os processos |
| `edgeAgent_total_memory_bytes` | `module_name` | Tipo: medidor<br> RAM disponível |
| `edgeAgent_used_cpu_percent` | `module_name` | Tipo: histograma<br> Porcentagem de CPU usada por todos os processos |
| `edgeAgent_created_pids_total` | `module_name` | Tipo: medidor<br> O número de processos ou threads que o contêiner criou |
| `edgeAgent_total_network_in_bytes` | `module_name` | Tipo: medidor<br> O número de bytes recebidos da rede |
| `edgeAgent_total_network_out_bytes` | `module_name` | Tipo: medidor<br> O número de bytes enviados à rede |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Tipo: medidor<br> O número de bytes lidos do disco |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Tipo: medidor<br> O número de bytes gravados no disco |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Tipo: medidor<br> Metadados gerais sobre o dispositivo. O valor é sempre 0, as informações são codificadas nas marcas. Observe `experimental_features` e `host_information` são objetos JSON. `host_information` é semelhante a ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` . Observe `ServerVersion` que a versão do Docker é `Version` a IOT Edge versão do daemon de segurança. |

## <a name="next-steps"></a>Próximas etapas

* [Entenda o runtime do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md)
* [Propriedades do agente do IoT Edge e dos gêmeos de módulo do hub do IoT Edge](module-edgeagent-edgehub.md)
