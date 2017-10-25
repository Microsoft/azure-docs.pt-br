---
title: "Como fazer uma chamada telefônica do Twilio (PHP) | Microsoft Docs"
description: "Saiba como fazer uma chamada telefônica e enviar uma mensagem SMS com o serviço de API do Twilio no Azure. Exemplos são para o aplicativo PHP."
documentationcenter: php
services: 
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 9866a196b3be10548d7a431430e570b41c190fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Como fazer uma chamada telefônica usando o Twilio em um aplicativo PHP no Azure
O exemplo a seguir mostra como você pode usar a Twilio para fazer uma chamada de uma página da web PHP hospedada no Azure. O aplicativo resultante solicitará valores de chamada telefônica ao usuário, conforme mostrado na seguinte captura de tela.

![Formulário de chamada do Azure usando a Twilio e o PHP][twilio_php]

Você precisará fazer o seguinte para usar o código deste tópico:

1. Adquirir uma conta e um token de autenticação da Twilio do seu [Console do Twilio][twilio_console]. Para uma introdução ao Twilio, avalie os preços em [http://www.twilio.com/pricing][twilio_pricing]. Você pode se inscrever para uma conta de avaliação em [https://www.twilio.com/try-twilio][try_twilio].
2. Obtenha a [biblioteca do Twilio para PHP](https://github.com/twilio/twilio-php) ou a instale como um pacote PEAR. Para obter mais informações, veja o [arquivo Leiame](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale o SDK do Azure para PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Criar um formulário da web para fazer uma chamada
O código HTML a seguir mostra como criar uma página da web (**callform.html**) que recupera dados do usuário para fazer uma chamada:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Como criar o código para fazer a chamada
O seguinte código mostra como criar **makecall.php**, que é chamada quando o usuário envia o formulário exibido pelo **callform.html**. O código abaixo cria a mensagem da chamada e gera a chamada. Além disso, certifique-se de usar sua conta do Twilio e o token de autenticação do [Console do Twilio][twilio_console] em vez dos valores de espaço reservado atribuídos a **$sid** e **$token** no código a seguir.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => http://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Além de fazer a chamada, o **makecall.php** exibe alguns metadados da chamada, conforme mostrado na imagem abaixo. Para obter mais informações sobre chamar metadados, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Resposta de chamada do Azure usando a Twilio e o PHP][twilio_php_response]

## <a name="run-the-application"></a>Executar o aplicativo
A próxima etapa é [implantar o aplicativo nos Aplicativos Web do Azure com o Git](app-service/app-service-web-get-started-php.md) (embora nem todas as informações da etapa sejam relevantes). 

## <a name="next-steps"></a>Próximas etapas
Esse código foi fornecido para mostrar a funcionalidade básica usando a Twilio no PHP no Azure. Antes de implantar o Azure na produção, convém adicionar mais tratamento de erros ou outros recursos. Por exemplo:

* Em vez de usar um formulário da web, você pode usar os blobs de armazenamento ou o Banco de Dados SQL do Azure para armazenar números de telefone e texto de chamada. Para obter informações sobre como usar os blobs do armazenamento do Azure no PHP, consulte [Usando o Armazenamento do Azure com aplicativos PHP][howto_blob_storage_php]. Para obter informações sobre como usar o Banco de Dados SQL no PHP, consulte [Usando o Banco de Dados SQL com aplicativos PHP][howto_sql_azure_php].
* O código **makecall.php** usa a URL fornecida pela Twilio ([http://twimlets.com/message][twimlet_message_url]) para fornecer uma resposta em TwiML (linguagem de marcação do Twilio) que informa ao Twilio sobre como proceder com a chamada. Por exemplo, a TwiML que é retornada pode conter um verbo `<Say>` que resulta no texto que está sendo falado para o destinatário da chamada. Em vez de usar a URL fornecida pela Twilio, você pode criar seu próprio serviço para responder à solicitação da Twilio. Para obter mais informações, consulte [Como usar a Twilio para obter recursos de voz e SMS em PHP][howto_twilio_voice_sms_php]. Mais informações sobre a TwiML podem ser encontradas em [http://www.twilio.com/docs/api/twiml][twiml] e mais informações sobre `<Say>` e outros verbos da Twilio podem ser encontradas em [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leia as diretrizes de segurança da Twilio em [https://www.twilio.com/docs/security][twilio_docs_security].

Para obter informações adicionais sobre a Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Consulte também
* [Como usar o Twilio para obter recursos de voz e SMS no PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
