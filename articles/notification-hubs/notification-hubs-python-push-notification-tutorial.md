---
title: Como usar Hubs de notificação com Python
description: Aprenda a usar Hubs de Notificação do Azure de um back-end Python.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: e2d0c7089ea070d82c75337c07fcc4a9df1c7c28
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359818"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Como usar Hubs de notificação do Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Você pode acessar todos os recursos dos Hubs de Notificação por meio de um back-end do Java/PHP/Python/Ruby usando a interface REST do Hub de Notificação, conforme descrito no artigo do MSDN [APIs REST dos Hubs de Notificação](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Isso é uma implementação de referência de exemplo para implementar o envia notificação em Python e não é oficialmente suportada notificações Hub Python SDK. O exemplo foi criado usando Python 3.4.

Este artigo mostra como:

- Crie um cliente REST para recursos de Hubs de notificação em Python.
- Envie notificações usando a interface do Python para as API do REST do Hub de notificação.
- Obtenha um despejo da solicitação/resposta HTTP REST para fins educativos/depuração.

Você pode seguir o [tutorial Introdução](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) para a plataforma móvel da sua escolha, implementando a parte de back-end em Python.

> [!NOTE]
> O escopo do exemplo é limitado apenas para enviar notificações e não faz nenhum gerenciamento de registro.

## <a name="client-interface"></a>Interface do cliente

A principal interface do cliente pode fornecer os mesmos métodos que estão disponíveis no [SDK dos Hubs de Notificação .NET](https://msdn.microsoft.com/library/jj933431.aspx). Essa interface permite a tradução diretamente de todos os tutoriais e exemplos atualmente disponíveis neste site e que conta com a colaboração da comunidade na Internet.

Você pode encontrar todo o código disponível na [amostra de wrapper REST Python].

Por exemplo, para criar um cliente:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Para enviar uma notificação do sistema Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementação

Se ainda não fez isso, siga o [tutorial Introdução] até a última seção, onde será necessário implementar o back-end.

Todos os detalhes para implementar um wrapper completo do REST podem ser encontrados em [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Esta seção descreve a implementação Python das principais etapas necessárias para acessar os pontos de extremidade REST de Hubs de notificação e envio de notificações

1. Analisar a cadeia de conexão
2. Gerar o token de autorização
3. Enviar uma notificação usando a API REST do HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de conexão

Aqui está a principal classe que implementa o cliente cujos construtor analisa a cadeia de conexão:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Criar token de segurança

Os detalhes da criação de token de segurança estão disponíveis [aqui](https://msdn.microsoft.com/library/dn495627.aspx).
Adicione os métodos a seguir à classe `NotificationHub` para criar o token com base no URI da solicitação atual e as credenciais extraídas da cadeia de conexão.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Enviar uma notificação usando a API REST do HTTP

Primeiro, vamos definir uma classe que representa uma notificação.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Essa classe é um contêiner para um corpo de notificação nativa ou um conjunto de propriedades de uma notificação de modelo, um conjunto de cabeçalhos que contém o formato (plataforma nativa ou modelo) e propriedades específicas da plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de APIs REST dos Hubs de Notificação](https://msdn.microsoft.com/library/dn495827.aspx) e os formatos específicos de notificação das plataformas para conhecer todas as opções disponíveis.

Agora, com essa classe, escreva a envie os métodos de notificação dentro da classe `NotificationHub`.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Esses métodos acima enviam uma solicitação de HTTP POST para o ponto de extremidade /messages de seu hub de notificação, com o corpo e os cabeçalhos corretos para o envio da notificação.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Usando a propriedade de depuração para habilitar o registro em log detalhado

A habilitação da propriedade de depuração ao inicializar o Hub de notificação grava as informações do registro em log detalhadas sobre a solicitação HTTP e despejo da resposta, bem como o resultado do envio da mensagem de notificação detalhada.
A propriedade chamada [propriedade TestSend de Hubs de Notificação](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) retorna informações detalhadas sobre o resultado de envio da notificação.
Para usá-la - inicialize usando o seguinte código:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

O Envio do Hub de notificação solicita que a URL HTTP seja anexada com uma cadeia de consulta "test" como um resultado.

## <a name="complete-tutorial"></a>Concluir o tutorial

Agora você pode concluir o Tutorial de introdução ao enviar a notificação de um back-end do Python.

Inicialize seu cliente dos Hubs de Notificação (substitua a cadeia de conexão e o nome do hub conforme indicado no [tutorial Introdução]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino. Este exemplo também adiciona métodos de nível superiores para habilitar as notificações de envio baseadas na plataforma send_windows_notification por exemplo, para Windows e send_apple_notification (para a Apple) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Silverlight para Windows Phone 8.0 e 8.1

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Executar o código do Python deve produzir uma notificação que aparece em seu dispositivo de destino.

## <a name="examples"></a>Exemplos

### <a name="enabling-the-debug-property"></a>Habilitando a propriedade `debug`

Quando habilitar o sinalizador de depuração ao inicializar o NotificationHub, você verá a solicitação HTTP detalhada e o despejo de resposta, bem como NotificationOutcome semelhante ao seguinte, em que você poderá entender quais cabeçalhos HTTP são passados na solicitação e qual a resposta HTTP foi recebida do Hub de notificação:

![][1]

Você verá o resultado do Hub de Notificação detalhado, por exemplo.

- quando a mensagem é enviada com êxito para o serviço de notificação por Push.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Se não houvesse nenhum destino encontrado para qualquer notificação por push, em seguida, você provavelmente veria a seguinte saída na resposta (que indica que não havia nenhum registro encontrado para entregar a notificação provavelmente porque os registros tinham algumas marcas incompatíveis)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Transmissão de notificação do sistema para Windows

Observe os cabeçalhos que são enviados quando você está enviando uma transmissão de notificação do sistema para cliente do Windows.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Enviar notificação especificando uma marca (ou expressão de marca)

Observe as Marcas do cabeçalho HTTP que são adicionadas à solicitação HTTP (no exemplo a seguir, a notificação é enviada somente para os registros com conteúdo “esportes”)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Enviar notificação especificando várias marcas

Observe como as Marcas do cabeçalho HTTP se alteram quando várias marcas são enviadas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Notificação modelada

Observe que o Formato de cabeçalho HTTP se altera e o corpo da carga é enviado como parte do corpo da solicitação HTTP:

**Lado cliente – modelo registrado:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Lado servidor – envio da payload:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Próximas etapas

Este artigo mostra como criar um cliente REST do Python para os Hubs de Notificação. A partir daqui, você pode:

- Baixe o [amostra de wrapper REST Python]completo, que contém todo o código neste artigo.
- Continue a aprender sobre o recurso de marcação dos Hubs de Notificação no [tutorial Últimas notícias]
- Continue a aprender sobre o recurso de modelos de Hubs de notificação no [tutorial Localização de notícias]

<!-- URLs -->
[amostra de wrapper REST Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[tutorial Introdução]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[tutorial Últimas notícias]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[tutorial Localização de notícias]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
