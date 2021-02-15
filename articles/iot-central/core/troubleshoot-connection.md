---
title: Solucionar problemas de conexões de dispositivo com o Azure IoT Central | Microsoft Docs
description: Solucionar problemas por que você não está vendo dados de seus dispositivos no IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: d1a7c94152b611ea0dbea249156add617178d7ca
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673227"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Solucionar problemas do motivo pelo qual os dispositivos não estão sendo exibidos no Azure IoT Central

Este documento ajuda os desenvolvedores de dispositivos a descobrir por que os dados que seus dispositivos estão enviando para IoT Central podem não estar aparecendo no aplicativo.

Há duas áreas principais a serem investigadas:

- Problemas de conectividade do dispositivo
  - Problemas de autenticação, como o dispositivo, têm credenciais inválidas
  - Problemas de conectividade de rede
  - O dispositivo não está aprovado ou bloqueado
- Problemas de forma de carga do dispositivo

Este guia de solução de problemas concentra-se nos problemas de conectividade do dispositivo e na forma de carga do dispositivo.

## <a name="device-connectivity-issues"></a>Problemas de conectividade do dispositivo

Esta seção ajuda você a determinar se seus dados estão atingindo IoT Central.

Se você ainda não tiver feito isso, instale a `az cli` ferramenta e a `azure-iot` extensão.

Para saber como instalar o `az cli` , consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

Para [instalar](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) a `azure-iot` extensão, execute o seguinte comando:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Você pode ser solicitado a instalar a `uamqp` biblioteca na primeira vez que executar um comando de extensão.

Quando você tiver instalado a `azure-iot` extensão, inicie o dispositivo para ver se as mensagens que ele está enviando estão fazendo seu caminho para IOT central.

Use os seguintes comandos para entrar na assinatura em que você tem seu aplicativo IoT Central:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Para monitorar a telemetria que seu dispositivo está enviando, use o seguinte comando:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Se o dispositivo tiver se conectado com êxito ao IoT Central, você verá uma saída semelhante à seguinte:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Para monitorar as atualizações de propriedade que seu dispositivo está trocando com IoT Central, use o seguinte comando de visualização:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Se o dispositivo enviar atualizações de propriedade com êxito, você verá uma saída semelhante à seguinte:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Se você vir os dados aparecerem no seu terminal, os dados estão fazendo isso até o IoT Central aplicativo.

Se você não vir os dados exibidos após alguns minutos, tente pressionar a `Enter` tecla ou `return` no teclado, caso a saída esteja presa.

Se você ainda não estiver vendo os dados aparecerem no seu terminal, é provável que o dispositivo esteja tendo problemas de conectividade de rede ou não esteja enviando dados corretamente para IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Verificar o status de provisionamento do seu dispositivo

Se seus dados não estiverem aparecendo no monitor, verifique o status de provisionamento do seu dispositivo executando o seguinte comando:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

A saída a seguir mostra um exemplo de um dispositivo que está impedido de se conectar:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Status de provisionamento do dispositivo | Descrição | Possível mitigação |
| - | - | - |
| Provisionado | Nenhum problema imediatamente reconhecível. | N/D |
| Registrada | O dispositivo ainda não se conectou ao IoT Central. | Verifique os logs do dispositivo para problemas de conectividade. |
| Bloqueado | O dispositivo está impedido de se conectar ao IoT Central. | O dispositivo está impedido de se conectar ao aplicativo IoT Central. Desbloqueie o dispositivo no IoT Central e tente novamente. Para saber mais, confira [bloquear dispositivos](concepts-get-connected.md#device-status-values). |
| Não aprovados | O dispositivo não está aprovado. | O dispositivo não está aprovado para se conectar ao aplicativo IoT Central. Aprove o dispositivo em IoT Central e tente novamente. Para saber mais, confira [aprovar dispositivos](concepts-get-connected.md#device-registration) |
| Não associado | O dispositivo não está associado a um modelo de dispositivo. | Associe o dispositivo a um modelo de dispositivo para que IoT Central saiba como analisar os dados. |

Saiba mais sobre os [códigos de status do dispositivo](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Códigos do Erro

Se ainda não for possível diagnosticar por que seus dados não estão aparecendo no `monitor-events` , a próxima etapa será procurar códigos de erro relatados pelo dispositivo.

Inicie uma sessão de depuração em seu dispositivo ou colete logs do seu dispositivo. Verifique se há códigos de erro que o dispositivo relata.

As tabelas a seguir mostram os códigos de erro comuns e as ações possíveis para mitigar.

Se você estiver vendo problemas relacionados ao seu fluxo de autenticação:

| Código do erro | Descrição | Possível mitigação |
| - | - | - |
| 400 | O corpo da solicitação não é válido. Por exemplo, ele não pode ser analisado ou o objeto não pode ser validado. | Certifique-se de que você está enviando o corpo da solicitação correto como parte do fluxo de atestado ou use um SDK do dispositivo. |
| 401 | O token de autorização não pode ser validado. Por exemplo, ele expirou ou não se aplica ao URI da solicitação. Esse código de erro também é retornado para dispositivos como parte do fluxo de atestado do TPM. | Verifique se o dispositivo tem as credenciais corretas. |
| 404 | A instância do serviço de provisionamento de dispositivos ou um recurso como um registro não existe. | [Arquivar um tíquete com o atendimento ao cliente](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | O `ETag` na solicitação não corresponde ao `ETag` do recurso existente, de acordo com o RFC7232. | [Arquivar um tíquete com o atendimento ao cliente](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | As operações estão sendo limitadas pelo serviço. Para limites de serviço específicos, consulte [limites do serviço de provisionamento de dispositivos no Hub IOT](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Reduzir a frequência da mensagem, dividir as responsabilidades entre mais dispositivos. |
| 500 | Ocorreu um erro interno. | [Arquivar um tíquete com o](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) atendimento ao cliente para ver se eles podem ajudá-lo. |

## <a name="payload-shape-issues"></a>Problemas de forma de carga

Quando você estabeleceu que seu dispositivo está enviando dados para IoT Central, a próxima etapa é garantir que seu dispositivo esteja enviando dados em um formato válido.

Há duas categorias principais de problemas comuns que fazem com que os dados do dispositivo não apareçam no IoT Central:

- Incompatibilidade de dados de modelo de dispositivo para dispositivo:
  - Incompatibilidade na nomenclatura, como erros de ortografia ou de correspondência de maiúsculas e minúsculas.
  - Propriedades não modeladas em que o esquema não está definido no modelo de dispositivo.
  - Incompatibilidade de esquema, como um tipo definido no modelo como `boolean` , mas os dados são uma cadeia de caracteres.
  - O mesmo nome de telemetria é definido em várias interfaces, mas o dispositivo não é compatível com IoT Plug and Play.
- A forma de dados é um JSON inválido. Para obter mais informações, consulte [telemetria, propriedade e cargas de comando](concepts-telemetry-properties-commands.md).

Para detectar em quais categorias seu problema está, execute o comando mais apropriado para seu cenário:

- Para validar a telemetria, use o comando de visualização:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Para validar as atualizações de propriedade, use o comando Preview

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Você pode ser solicitado a instalar a `uamqp` biblioteca na primeira vez em que executar um `validate` comando.

A saída a seguir mostra o exemplo de mensagens de erro e de aviso do comando validar:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Se você preferir usar uma GUI, use o IoT Central exibição de **dados brutos** para ver se algo não está sendo modelado. A exibição de **dados brutos** não detecta se o dispositivo está enviando JSON malformado.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Captura de tela da exibição de dados brutos":::

Quando você detectar o problema, talvez seja necessário atualizar o firmware do dispositivo ou criar um novo modelo de dispositivo que modela dados anteriormente não modelados.

Se você optar por criar um novo modelo que modela os dados corretamente, migre os dispositivos do modelo antigo para o novo modelo. Para saber mais, confira [gerenciar dispositivos no aplicativo IOT central do Azure](howto-manage-devices.md).

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode arquivar um [tíquete de suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).

Para obter mais informações, consulte [Opções de ajuda e suporte do Azure IOT](../../iot-fundamentals/iot-support-help.md).
