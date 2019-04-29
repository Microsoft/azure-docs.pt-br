---
title: Como usar Hubs de Notificação com PHP
description: Aprenda a usar Hubs de Notificação do Azure por meio de um back-end do PHP.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872167"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Como usar Hubs de Notificação no PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Você pode acessar todos os recursos dos Hubs de Notificação por meio de um back-end Java/PHP/Ruby usando a interface REST do Hub de Notificação, conforme descrito no tópico do MSDN [APIs REST dos Hubs de Notificação](https://msdn.microsoft.com/library/dn223264.aspx).

Neste tópico, mostramos como:

* Criar um cliente REST para recursos dos Hubs de Notificação no PHP;
* Seguir o [Tutorial de introdução](notification-hubs-ios-apple-push-notification-apns-get-started.md) para a plataforma móvel de sua escolha, implementando a parte sobre back-end no PHP.

## <a name="client-interface"></a>Interface do cliente

A interface principal do cliente pode oferecer os mesmos métodos disponíveis no [SDK dos Hubs de Notificação do .NET](https://msdn.microsoft.com/library/jj933431.aspx); o que permite a conversão direta de todos os tutoriais e exemplos disponíveis atualmente neste site, bem como daqueles enviados pela comunidade na internet.

Você pode encontrar todos os códigos disponíveis na [amostra de wrapper do PHP REST].

Por exemplo, para criar um cliente:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Para enviar uma notificação nativa do iOS:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementação

Se você ainda não fez isso, siga o [Tutorial de introdução] até a última seção, onde será necessário implementar o back-end.
Além disso, se quiser, você poderá usar o código da [amostra de wrapper do PHP REST] e ir diretamente para a seção [Concluir o tutorial](#complete-tutorial).

Todos os detalhes para implementar um wrapper completo do REST podem ser encontrados em [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Nesta seção, descrevemos a implementação do PHP das principais etapas necessárias para acessar os pontos de extremidade de REST dos Hubs de Notificação:

1. Analisar a cadeia de conexão
2. Gerar o token de autorização
3. Realizar a chamada do HTTP

### <a name="parse-the-connection-string"></a>Analisar a cadeia de conexão

Aqui está a classe principal que implementa o cliente, cujo construtor analisa a cadeia de conexão:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Criar um token de segurança

Confira a documentação do Azure para saber mais sobre como [Criar um token de segurança de SAS](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Adicione o método `generateSasToken` à classe `NotificationHub` para criar o token com base no URI da solicitação atual e nas credenciais extraídas da cadeia de conexão.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Enviar uma notificação

Primeiro, vamos definir uma classe que representa uma notificação.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Essa classe é um contêiner para um corpo de notificação nativa ou um conjunto de propriedade no caso de uma notificação de modelo, e um conjunto de cabeçalhos que contém propriedades específicas de formato (plataforma nativa ou modelo) e plataforma (como a propriedade de expiração da Apple e cabeçalhos WNS).

Consulte a [documentação de APIs REST dos Hubs de Notificação](https://msdn.microsoft.com/library/dn495827.aspx) e os formatos específicos de notificação das plataformas para conhecer todas as opções disponíveis.

Munidos com essa classe, agora podemos gravar os métodos de envio de notificação dentro da classe `NotificationHub`:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Os métodos acima enviam uma solicitação HTTP POST para o ponto de extremidade `/messages` de seu hub de notificação, com o corpo e os cabeçalhos corretos para o envio da notificação.

## <a name="complete-tutorial"></a>Concluir o tutorial

Agora você pode concluir o tutorial de introdução enviando a notificação por meio de um back-end do PHP.

Inicialize seu cliente dos Hubs de Notificação (substitua a cadeia de conexão e o nome do hub conforme indicado no [Tutorial de introdução]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Em seguida, adicione o código de envio dependendo da sua plataforma móvel de destino.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (não Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Silverlight para Windows Phone 8.0 e 8.1

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Executar o código PHP agora deve produzir uma notificação que aparece no dispositivo de destino.

## <a name="next-steps"></a>Próximas etapas

Neste tópico, mostramos como criar um cliente REST simples do Java para Hubs de Notificação. A partir daqui, você pode:

* Baixar a [amostra de wrapper do PHP REST]completa, que contém todos os códigos acima.
* Continuar a aprender sobre o recurso de criação de tags dos Hubs de Notificação no [tutorial Últimas Notícias]
* Aprender sobre como enviar notificações por push para usuários individuais no [tutorial Notificação de Usuários]

Para saber mais, veja também a [Central de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).

[amostra de wrapper do PHP REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Tutorial de introdução]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
