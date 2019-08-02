---
title: Entender o suporte do MQTT do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ suporte para dispositivos que se conectam a um ponto de extremidade do Hub IoT voltado para o dispositivo usando o protocolo MQTT. Inclui informações sobre suporte interno do MQTT nos SDKs do dispositivo IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 0fc641431f97a12797d675a42a1a1b8b2355fcf0
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440953"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Comunicar com o hub IoT usando o protocolo MQTT

O Hub IoT permite que os dispositivos comuniquem-se com os pontos de extremidade do dispositivo de Hub IoT usando:

* [MQTT v 3.1.1](https://mqtt.org/) na porta 8883
* MQTT v3.1.1 sobre WebSocket na porta 443.

O Hub IoT não é um agente MQTT completo e não dá suporte a todos os comportamentos especificados no padrão MQTT v3.1.1. Este artigo descreve como dispositivos podem usar comportamentos com suporte do MQTT para comunicar-se com o Hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Todas as comunicações de dispositivo com o Hub IoT devem ser protegidas usando TLS/SSL. Portanto, o Hub IoT não fornece suporte para conexões não seguras na porta 1883.

## <a name="connecting-to-iot-hub"></a>Conectando-se ao Hub IoT

Um dispositivo pode usar o protocolo MQTT para se conectar a um hub IoT usando qualquer uma das opções a seguir.

* Bibliotecas nos [SDKs do IOT do Azure](https://github.com/Azure/azure-iot-sdks).
* O protocolo MQTT diretamente.

## <a name="using-the-device-sdks"></a>Usando os SDKs de dispositivo

Os [SDKs do dispositivo](https://github.com/Azure/azure-iot-sdks) que dão suporte ao protocolo MQTT estão disponíveis para Java, Node. js C#, C, e Python. Os SDKs do dispositivo usam a cadeia de conexão do Hub IoT padrão para estabelecer uma conexão com um Hub IoT. Para usar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido como **MQTT**. Por padrão, os SDKs do dispositivo se conectam a um Hub IoT com o sinalizador **CleanSession** definido como **0** e usam **QoS 1** para troca de mensagens com o Hub IoT.

Quando um dispositivo está conectado a um Hub IoT, os SDKs do dispositivo fornecem métodos que permitem que o dispositivo troque mensagens com um Hub IoT.

A tabela a seguir contém links para exemplos de código de cada linguagem compatível e especifica o parâmetro a ser usado para estabelecer uma conexão com o Hub IoT usando o protocolo MQTT.

| Linguagem | Parâmetro do protocolo |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrando um aplicativo de dispositivo de AMQP para MQTT

Se você estiver usando os [SDKs do dispositivo](https://github.com/Azure/azure-iot-sdks), alternar de usando AMQP para MQTT requer a alteração do parâmetro de protocolo na inicialização do cliente, conforme mencionado anteriormente.

Ao fazer isso, verifique os seguintes itens:

* AMQP retorna erros para várias condições, enquanto MQTT encerra a conexão. Como resultado, sua lógica de manipulação de exceções pode exigir algumas alterações.

* O MQTT não oferece suporte  às operações de rejeição ao receber [mensagens da nuvem para o dispositivo](iot-hub-devguide-messaging.md). Se o aplicativo de back-end precisar receber uma resposta do aplicativo do dispositivo, considere o uso de [métodos diretos](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Usando o protocolo MQTT diretamente (como um dispositivo)

Se um dispositivo não puder usar os SDKs do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT na porta 8883. No pacote do **Connect** , o dispositivo deve usar os seguintes valores:

* No campo **ClientId**, use o **deviceId**.

* Para o campo **Username**, use `{iothubhostname}/{device_id}/?api-version=2018-06-30`, onde `{iothubhostname}` é o CName completo do Hub IoT.

    Por exemplo, se o nome de seu Hub IoT for **contoso.azure-devices.net** e se o nome do dispositivo for **MyDevice01**, o campo **Username** completo deverá conter:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* No campo **Senha** use um token SAS. O formato do token SAS é o mesmo, conforme descrito para os protocolos HTTPS e AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Se você usa a autenticação de certificado X.509, as senhas de token SAS não são necessárias. Para obter mais informações, consulte [Configurar a segurança X. 509 em seu hub IOT do Azure](iot-hub-security-x509-get-started.md)

  Para obter mais informações sobre como gerar tokens SAS, consulte a seção dispositivo de [usando tokens de segurança do Hub IOT](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Ao testar, você também pode usar as [Ferramentas do Azure IOT de](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) plataforma cruzada para Visual Studio Code ou a ferramenta de [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) para gerar rapidamente um token SAS que você pode copiar e colar em seu próprio código:

### <a name="for-azure-iot-tools"></a>Para ferramentas de IoT do Azure

1. Expanda a guia **AZURE IOT HUB DEVICES** no canto inferior esquerdo do Visual Studio Code.
  
2. Clique com o botão direito no dispositivo e selecione **Generate SAS Token for Device**.
  
3. Definir **tempo de expiração** e pressione 'Enter'.
  
4. O token SAS é criado e copiado para a área de transferência.

### <a name="for-device-explorer"></a>Para Device Explorer

1. Acesse a guia **Gerenciamento** no **Gerenciador de Dispositivo**.

2. Clique em **Token SAS** (parte superior direita).

3. Em **SASTokenForm**, selecione seu dispositivo no menu suspenso **DeviceID**. Defina o **TTL**.

4. Clique em **Gerar** para criar o token.

   O token de SAS gerado tem a seguinte estrutura:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   A parte desse token para usar como o campo **Senha** para conectar usando MQTT é:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Para que o MQTT conecte e desconecte pacotes, o Hub IoT emite um evento no canal **Monitoramento de Operações** . Este evento possui informações adicionais que podem ajudá-lo a solucionar problemas de conectividade.

O aplicativo de dispositivo pode especificar uma mensagem **Will** no pacote **CONNECT**. O aplicativo do dispositivo devem usar `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}`como o nome do tópico **Will** para definir mensagens **Will** a serem encaminhadas como uma mensagem de telemetria. Nesse caso, se a conexão de rede for fechada, mas um pacote **DISCONNECT** não tiver sido recebido anteriormente do dispositivo, o Hub IoT enviará a mensagem **Will** fornecida no pacote **CONNECT** para o canal de telemetria. O canal de telemetria pode ser o ponto de extremidade padrão **Eventos**, ou um ponto de extremidade personalizado definido pelo roteamento do Hub IoT. A mensagem tem a propriedade **iothub-MessageType** com um valor de **Will** atribuído a ele.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Usando o protocolo MQTT diretamente (como um módulo)

Conectar ao Hub IoT por meio de MQTT usando uma identidade de módulo é semelhante ao dispositivo (descrito [acima](#using-the-mqtt-protocol-directly-as-a-device)), mas você precisa usar o seguinte:

* Defina a ID do cliente como `{device_id}/{module_id}`.

* Se autenticando com o nome de usuário e senha, defina o nome de usuário como `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` e use o token SAS associado à identidade de módulo como sua senha.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como tópico para a publicação de telemetria.

* Use `devices/{device_id}/modules/{module_id}/messages/events/` como o tópico WILL.

* Os tópicos gêmeos GET e PATCH são idênticos para módulos e dispositivos.

* Os tópicos de status gêmeos são idênticos para módulos e dispositivos.

## <a name="tlsssl-configuration"></a>Configuração de TLS/SSL

Para usar o protocolo MQTT diretamente, o cliente *deve* se conectar por TLS/SSL. Tentativas de ignorar essa etapa falham com erros de conexão.

Para estabelecer uma conexão TLS, você precisará baixar e referenciar o certificado de raiz DigiCert Baltimore. Esse certificado é utilizado pelo Azure para proteger a conexão. Você pode encontrar esse certificado no repositório [Azure-IOT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) . Mais informações sobre esses certificados podem ser encontradas no [site do DigiCert](https://www.digicert.com/digicert-root-certificates.htm).

Um exemplo de como implementar isso usando a versão Python da [biblioteca PAHO MQTT](https://pypi.python.org/pypi/paho-mqtt) pelo Eclipse Foundation pode ser semelhante ao seguinte.

Primeiro, instale a biblioteca Paho do seu ambiente de linha de comando:

```cmd/sh
pip install paho-mqtt
```

Em seguida, implemente o cliente em um script Python. Substitua os espaços reservados conforme a seguir:

* `<local path to digicert.cer>` é o caminho para um arquivo local que contém o certificado DigiCert Baltimore Root. Você pode criar esse arquivo, copiando as informações sobre o certificado do [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) no SDK de IoT do Azure para C. Inclua as linhas `-----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----`, remova as marcas `"` no início e no final de cada linha, e remova os caracteres `\r\n` no final de cada linha.

* `<device id from device registry>` é a ID de um dispositivo que você adicionou ao seu Hub IoT.

* `<generated SAS token>` é um token de SAS para o dispositivo criado, conforme descrito anteriormente neste artigo.

* `<iot hub name>` o nome do seu Hub IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

## <a name="sending-device-to-cloud-messages"></a>Enviando mensagens de dispositivo para nuvem

Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens ao IoT Hub usando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **nome de tópico**. O elemento `{property_bag}` habilita o dispositivo a enviar mensagens com propriedades adicionais em um formato codificado de URL. Por exemplo:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Esse `{property_bag}` elemento usa a mesma codificação que as cadeias de caracteres de consulta no protocolo HTTPS.

Veja a seguir uma lista dos comportamentos específicos à implementação do Hub IoT:

* O Hub IoT não dá suporte a mensagens de QoS 2. Se um aplicativo de dispositivo publicar uma mensagem com o **QoS 2**, o Hub IoT fechará a conexão de rede.

* O Hub IoT não persiste mensagens de retenção. Se um dispositivo envia uma mensagem com o sinalizador **RETAIN** definido como 1, o Hub IoT adiciona a propriedade de aplicativo **x-opt-retain** à mensagem. Nesse caso, em vez de persistir a mensagem de retenção, o Hub IoT a transmite ao aplicativo de back-end.

* O Hub IoT oferece suporte apenas a uma conexão MQTT ativa por dispositivo. Qualquer nova conexão MQTT em nome da mesma ID de dispositivo faz com que o Hub IoT descarte a conexão existente.

Para obter mais informações, consulte [Guia do desenvolvedor de mensagens](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Recebendo mensagens da nuvem para o dispositivo

Para receber mensagens do Hub IoT, um dispositivo deve fazer uma assinatura usando `devices/{device_id}/messages/devicebound/#` como um **Filtro do Tópico**. O curinga de vários níveis `#` no filtro de tópico é usado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O Hub IoT não permite o uso dos caracteres curinga `#` ou `?` para filtragem de subtópicos. Como o Hub IoT não é um agente de mensagens pub-sub de uso geral, ele dá suporte somente para nomes de tópicos e filtros tópicos documentados.

O dispositivo só receberá mensagens do Hub IoT depois de assinar com êxito o ponto de extremidade específico ao dispositivo, representado pelo filtro de tópico `devices/{device_id}/messages/devicebound/#`. Depois que uma assinatura é estabelecida, o dispositivo receberá mensagens de nuvem para dispositivo que foram enviadas após o horário da assinatura. Se o dispositivo se conectar com o sinalizador **CleanSession** definido como **0**, a assinatura será persistida entre as diferentes sessões. Neste caso, a próxima vez que o dispositivo conectar-se com **CleanSession 0**, ele receberá todas as mensagens pendentes enviadas a ele enquanto estava desconectado. Se o dispositivo usar o sinalizador **CleanSession** definido como **1**, não receberá todas as mensagens do Hub IoT até que ele se inscreva no ponto de extremidade do dispositivo.

O Hub IoT entrega mensagens com o **Nome do Tópico** `devices/{device_id}/messages/devicebound/` ou `devices/{device_id}/messages/devicebound/{property_bag}` quando há propriedades de mensagens. `{property_bag}` contém pares de chave/valor codificados de URL das propriedades da mensagem. Somente propriedades de aplicativo e propriedades do sistema definível pelo usuário (como **messageId** ou **correlationId**) estão incluídas no recipiente de propriedades. Os nomes de propriedade do sistema têm o prefixo **$** ; as propriedades de aplicativo usam o nome da propriedade original sem prefixo.

Quando um aplicativo do dispositivo assina um tópico com o **QoS 2**, o Hub IoT concede, no máximo, o nível 1 do QoS no pacote **SUBACK**. Depois disso, o Hub IoT entrega mensagens ao dispositivo usando QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Recuperando as propriedades de um dispositivo gêmeo

Primeiro, um dispositivo assina `$iothub/twin/res/#` para receber respostas da operação. Em seguida, ele envia uma mensagem vazia ao tópico `$iothub/twin/GET/?$rid={request id}`, com um valor preenchido como a **ID da solicitação**. O serviço então envia uma mensagem de resposta que contém os dados do dispositivo gêmeo no tópico `$iothub/twin/res/{status}/?$rid={request id}`, usando a mesma **ID de solicitação** da solicitação.

A ID de solicitação pode ser qualquer valor válido para um valor de propriedade de mensagem, de acordo com o [Guia do desenvolvedor de mensagens do Hub IOT](iot-hub-devguide-messaging.md)e o status é validado como um inteiro.

O corpo de resposta contém a seção de propriedades do dispositivo gêmeo, como no seguinte exemplo de resposta:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Os códigos de status possíveis são:

|Status | Descrição |
| ----- | ----------- |
| 204 | Êxito (nenhum conteúdo retorna) |
| 429 | Muitas solicitações (limitadas), de acordo com a [limitação do Hub IOT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros do servidor |

Para obter mais informações, consulte [Guia do desenvolvedor do dispositivo gêmeos](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Atualizar as propriedades relatadas do dispositivo gêmeo

Para atualizar as propriedades relatadas, o dispositivo emite uma solicitação para o Hub IoT por meio de uma publicação ao longo de um tópico MQTT designado. Depois de processar a solicitação, o Hub IoT responde o status de êxito ou falha da operação de atualização por meio de uma publicação para outro tópico. Este tópico pode ser assinado pelo dispositivo para notificá-lo sobre o resultado de sua solicitação de atualização de gêmeos. Para implementar esse tipo de interação de solicitação/resposta em MQTT, podemos utilizar a noção de ID de solicitação (`$rid`) fornecida inicialmente pelo dispositivo em sua solicitação de atualização. Essa ID de solicitação também está incluído na resposta do Hub IoT para permitir que o dispositivo correlacionar a resposta à sua solicitação anterior específica.

A sequência a seguir descreve como um dispositivo atualiza as propriedades relatadas no dispositivo gêmeo no Hub IoT:

1. Primeiro, um dispositivo deve assinar o tópico `$iothub/twin/res/#` para receber respostas da operação do Hub IoT.

2. Um dispositivo envia uma mensagem que contém a atualização do dispositivo gêmeo para o tópico `$iothub/twin/PATCH/properties/reported/?$rid={request id}`. Essa mensagem inclui um valor de **id da solicitação**.

3. Em seguida, o serviço envia uma mensagem de resposta que contém o novo valor de ETag para a coleção de propriedades relatadas no tópico `$iothub/twin/res/{status}/?$rid={request id}`. Essa mensagem de resposta usa a mesma **id de solicitação** da solicitação.

O corpo da mensagem de solicitação contém um documento JSON, que contém novos valores para propriedades relatadas. Cada membro no documento JSON atualiza ou adiciona o membro correspondente no documento do dispositivo gêmeo. Um membro definido como `null` exclui o membro do objeto recipiente. Por exemplo:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Os códigos de status possíveis são:

|Status | Descrição |
| ----- | ----------- |
| 200 | Êxito |
| 400 | Solicitação inválida. JSON malformado |
| 429 | Muitas solicitações (limitadas), de acordo com a [limitação do Hub IOT](iot-hub-devguide-quotas-throttling.md) |
| 5** | Erros do servidor |

O trecho de código python abaixo, demonstra o processo de atualização de propriedades relatadas de gêmeos sobre MQTT (usando o cliente Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Após o êxito do gêmeo relatado na operação de atualização de propriedades acima, a mensagem de publicação do Hub IoT terá o seguinte tópico: `$iothub/twin/res/204/?$rid=1&$version=6`, onde `204` é o código de status indicando sucesso, `$rid=1` corresponde à ID da solicitação fornecido pelo dispositivo no código, e `$version` corresponde à versão da seção de propriedades relatadas de gêmeos de dispositivo após a atualização.

Para obter mais informações, consulte [Guia do desenvolvedor do dispositivo gêmeos](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Recebendo notificações de atualização de propriedades desejadas

Quando um dispositivo é conectado, o Hub IoT envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}`, que contêm o conteúdo da atualização executada pelo back-end da solução. Por exemplo:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Em relação às atualizações de propriedade, valores `null` significam que o membro do objeto JSON está sendo excluído. Além disso, observe que `$version` indica a nova versão da seção de propriedades desejada do dispositivo gêmeo.

> [!IMPORTANT]
> O Hub IoT gera notificações de alteração somente quando os dispositivos estão conectados. Certifique-se de implementar o fluxo de reconexão do [dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow) para manter as propriedades desejadas sincronizadas entre o Hub IOT e o aplicativo do dispositivo.

Para obter mais informações, consulte [Guia do desenvolvedor do dispositivo gêmeos](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Responder a um método direto

Primeiro, um dispositivo precisa assinar `$iothub/methods/POST/#`. O Hub IoT envia solicitações de método para o tópico `$iothub/methods/POST/{method name}/?$rid={request id}` com um corpo vazio ou JSON válido.

Para responder, o dispositivo envia uma mensagem com um JSON válido ou um corpo vazio para o tópico `$iothub/methods/res/{status}/?$rid={request id}`. Nessa mensagem, a **ID da Solicitação** deve corresponder com o da mensagem de solicitação e o **status** deve ser um número inteiro.

Para obter mais informações, consulte [Guia do desenvolvedor do método direto](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Considerações adicionais

Como uma consideração final, se você precisar personalizar o comportamento do protocolo MQTT no lado da nuvem, examine o gateway do [protocolo IOT do Azure](iot-hub-protocol-gateway.md). Esse software permite que você implante um gateway de protocolo personalizado de alto desempenho que faz interface diretamente com o Hub IoT. O gateway do protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar as implantações de MQTT de nível industrial ou outros protocolos personalizados. Essa abordagem exige, no entanto, que você execute e opere um gateway de protocolo personalizado.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT](https://mqtt.org/documentation).

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Catálogo de dispositivos Azure Certified para IoT](https://catalog.azureiotsolutions.com/)
* [Suporte a protocolos adicionais](iot-hub-protocol-gateway.md)
* [Comparar com hubs de eventos](iot-hub-compare-event-hubs.md)
* [Dimensionamento, alta disponibilidade e DR](iot-hub-scaling.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
