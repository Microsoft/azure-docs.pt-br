---
title: Entender o suporte do AMQP do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – suporte para dispositivos que se conectam a pontos de extremidade voltados para o dispositivo do Hub IoT usando o protocolo AMQP. Inclui informações sobre o suporte interno do AMQP nos SDKs do dispositivo IoT do Azure.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: b53bb0f04bf6a739b588b14febd622f6bf7a6a63
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68354887"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Comunicar-se com o Hub IoT usando o protocolo AMQP

O Hub IoT do Azure dá suporte ao [OASIS Advanced Message Queuing Protocol (AMQP) versão 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) para fornecer uma variedade de funcionalidades por meio de pontos de extremidade voltados para o dispositivo e para o serviço. Este documento descreve o uso de clientes do AMQP para se conectar a um hub IoT para usar a funcionalidade do Hub IoT.

## <a name="service-client"></a>Cliente de serviço

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Conectar e autenticar para um hub IoT (cliente de serviço)

Para se conectar a um hub IoT usando o AMQP, um cliente pode usar a autenticação do [CBS (segurança baseada em declarações)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou do [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

As informações a seguir são necessárias para o cliente de serviço:

| Information | Valor |
|-------------|--------------|
| Nome de host do Hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome da chave | `service` |
| Tecla de acesso | Uma chave primária ou secundária associada ao serviço |
| Assinatura de acesso compartilhado | Uma assinatura de acesso compartilhado de curta duração no seguinte formato: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Para obter o código para gerar essa assinatura, consulte [controlar o acesso ao Hub IOT](./iot-hub-devguide-security.md#security-token-structure).

O trecho de código a seguir usa a [biblioteca uAMQP no Python](https://github.com/Azure/azure-uamqp-python) para se conectar a um hub IOT por meio de um link de remetente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Invocar mensagens da nuvem para o dispositivo (cliente de serviço)

Para saber mais sobre a troca de mensagens da nuvem para o dispositivo entre o serviço e o Hub IoT e entre o dispositivo e o Hub IoT, consulte [enviar mensagens da nuvem para o dispositivo do Hub IOT](iot-hub-devguide-messages-c2d.md). O cliente de serviço usa dois links para enviar mensagens e receber comentários para mensagens enviadas anteriormente de dispositivos, conforme descrito na tabela a seguir:

| Criado por | Tipo de link | Caminho do link | Descrição |
|------------|-----------|-----------|-------------|
| Serviço | Link do remetente | `/messages/devicebound` | As mensagens da nuvem para o dispositivo que são destinadas a dispositivos são enviadas para esse link pelo serviço. As mensagens enviadas por esse link têm `To` sua propriedade definida como o caminho do link do destinatário do `/devices/<deviceID>/messages/devicebound`dispositivo de destino,. |
| Serviço | Link do destinatário | `/messages/serviceBound/feedback` | As mensagens de comentários de conclusão, rejeição e abandono provenientes de dispositivos recebidos neste link pelo serviço. Para obter mais informações sobre mensagens de comentários, consulte [enviar mensagens da nuvem para o dispositivo de um hub IOT](./iot-hub-devguide-messages-c2d.md#message-feedback). |

O trecho de código a seguir demonstra como criar uma mensagem da nuvem para o dispositivo e enviá-la para um dispositivo usando a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Para receber comentários, o cliente de serviço cria um link de destinatário. O trecho de código a seguir demonstra como criar um link usando a [biblioteca uAMQP no Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Conforme mostrado no código anterior, uma mensagem de comentários da nuvem para o dispositivo tem um tipo de conteúdo de *application/vnd. Microsoft. iothub. feedback. JSON*. Você pode usar as propriedades no corpo JSON da mensagem para inferir o status de entrega da mensagem original:

* A `statusCode` chave no corpo de comentários tem um dos seguintes valores: *Êxito*, *expirado*, *DeliveryCountExceeded*, *rejeitado*ou *limpo*.

* A `deviceId` chave no corpo de comentários tem a ID do dispositivo de destino.

* A `originalMessageId` chave no corpo de comentários tem a ID da mensagem original da nuvem para o dispositivo enviada pelo serviço. Você pode usar esse status de entrega para correlacionar os comentários às mensagens da nuvem para o dispositivo.

### <a name="receive-telemetry-messages-service-client"></a>Receber mensagens de telemetria (cliente de serviço)

Por padrão, o Hub IoT armazena mensagens de telemetria de dispositivo ingeridas em um hub de eventos interno. O cliente de serviço pode usar o protocolo AMQP para receber os eventos armazenados.

Para essa finalidade, o cliente de serviço primeiro precisa se conectar ao ponto de extremidade do Hub IoT e receber um endereço de redirecionamento para os hubs de eventos internos. Em seguida, o cliente de serviço usa o endereço fornecido para se conectar ao Hub de eventos interno.

Em cada etapa, o cliente precisa apresentar as seguintes informações:

* Credenciais de serviço válidas (token de assinatura de acesso compartilhado do serviço).

* Um caminho bem formatado para a partição do grupo de consumidores da qual ele pretende recuperar mensagens. Para um determinado grupo de consumidores e ID de partição, o caminho tem o seguinte `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` formato: (o grupo de `$Default`consumidores padrão é).

* Um predicado de filtragem opcional para designar um ponto de partida na partição. Esse predicado pode estar na forma de um número de sequência, deslocamento ou carimbo de data/hora enfileirado.

O trecho de código a seguir usa a [biblioteca uAMQP no Python](https://github.com/Azure/azure-uamqp-python) para demonstrar as etapas anteriores:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Para uma determinada ID de dispositivo, o Hub IoT usa um hash da ID do dispositivo para determinar em qual partição armazenar suas mensagens. O trecho de código anterior demonstra como os eventos são recebidos de uma única partição desse tipo. No entanto, observe que um aplicativo típico geralmente precisa recuperar eventos que são armazenados em todas as partições do hub de eventos.

## <a name="device-client"></a>Cliente do dispositivo

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Conectar e autenticar em um hub IoT (cliente do dispositivo)

Para se conectar a um hub IoT usando o AMQP, um dispositivo pode usar a autenticação [CBS (segurança baseada em declarações)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) ou a [Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) .

As informações a seguir são necessárias para o cliente do dispositivo:

| Information | Valor |
|-------------|--------------|
| Nome de host do Hub IoT | `<iot-hub-name>.azure-devices.net` |
| Tecla de acesso | Uma chave primária ou secundária associada ao dispositivo |
| Assinatura de acesso compartilhado | Uma assinatura de acesso compartilhado de curta duração no seguinte formato: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Para obter o código para gerar essa assinatura, consulte [controlar o acesso ao Hub IOT](./iot-hub-devguide-security.md#security-token-structure).

O trecho de código a seguir usa a [biblioteca uAMQP no Python](https://github.com/Azure/azure-uamqp-python) para se conectar a um hub IOT por meio de um link de remetente.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Os caminhos de link a seguir têm suporte como operações de dispositivo:

| Criado por | Tipo de link | Caminho do link | Descrição |
|------------|-----------|-----------|-------------|
| Dispositivos | Link do destinatário | `/devices/<deviceID>/messages/devicebound` | As mensagens da nuvem para o dispositivo que são destinadas a dispositivos são recebidas neste link por cada dispositivo de destino. |
| Dispositivos | Link do remetente | `/devices/<deviceID>messages/events` | As mensagens do dispositivo para a nuvem enviadas de um dispositivo são enviadas por esse link. |
| Dispositivos | Link do remetente | `/messages/serviceBound/feedback` | Comentários de mensagem da nuvem para o dispositivo enviados ao serviço por meio desse link pelos dispositivos. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Receber comandos da nuvem para o dispositivo (cliente do dispositivo)

Os comandos da nuvem para o dispositivo enviados aos dispositivos chegam em um `/devices/<deviceID>/messages/devicebound` link. Os dispositivos podem receber essas mensagens em lotes e usar a carga de dados da mensagem, as propriedades da mensagem, as anotações ou as propriedades do aplicativo na mensagem, conforme necessário.

O trecho de código a seguir usa a [biblioteca uAMQP em Python](https://github.com/Azure/azure-uamqp-python)) para receber mensagens da nuvem para o dispositivo por um dispositivo.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Enviar mensagens de telemetria (cliente do dispositivo)

Você também pode enviar mensagens de telemetria de um dispositivo usando o AMQP. O dispositivo pode, opcionalmente, fornecer um dicionário de propriedades do aplicativo ou várias propriedades de mensagem, como a ID da mensagem.

O trecho de código a seguir usa a [biblioteca uAMQP no Python](https://github.com/Azure/azure-uamqp-python) para enviar mensagens do dispositivo para a nuvem de um dispositivo.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Observações adicionais

* As conexões AMQP podem ser interrompidas devido a uma falha de rede ou à expiração do token de autenticação (gerado no código). O cliente de serviço deve lidar com essas circunstâncias e restabelecer a conexão e os links, se necessário. Se um token de autenticação expirar, o cliente poderá evitar uma queda de conexão proativamente renovando o token antes da expiração.

* O cliente deve, ocasionalmente, ser capaz de lidar com redirecionamentos de link corretamente. Para entender essa operação, consulte a documentação do cliente do AMQP.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o protocolo AMQP, consulte a [especificação AMQP v 1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Para saber mais sobre as mensagens do Hub IoT, consulte:

* [Mensagens da nuvem para o dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Suporte para protocolos adicionais](iot-hub-protocol-gateway.md)
* [Suporte para o protocolo MQTT (transporte de telemetria de enfileiramento de mensagens)](./iot-hub-mqtt-support.md)