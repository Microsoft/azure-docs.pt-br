---
title: Pontos de extremidade de segurança no Serviço de Provisionamento de Dispositivo IoT | Microsoft Docs
description: Conceitos - como controlar o acesso ao Serviço de Provisionamento de Dispositivos no IoT para aplicativos de back-end. Inclui informações sobre tokens de autenticação de segurança.
author: wesmc7777
manager: timlt
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: wesmc
ms.openlocfilehash: 7ff622ceac9c49eda7ba6bca1a8bb3aaabccb816
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626632"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Controlar o acesso ao Serviço de Provisionamento de Dispositivo do Hub IoT do Azure

Esta seção descreve as opções para proteger o serviço de Provisionamento de Dispositivos no IoT. O serviço de provisionamento usa *permissões* para conceder acesso a cada ponto de extremidade. As permissões limitam o acesso a uma instância de serviço com base na funcionalidade.

Este artigo descreve:

* As diferentes permissões que você pode conceder a um aplicativo de back-end para acessar o serviço de provisionamento.
* O processo de autenticação e os tokens que ele usa para verificar permissões.

### <a name="when-to-use"></a>Quando usar

Você deve ter permissões adequadas para acessar qualquer um dos pontos de extremidade do serviço de provisionamento. Por exemplo, um aplicativo de back-end deve incluir um token que contém credenciais de segurança juntamente com cada mensagem que ele envia para o serviço.

## <a name="access-control-and-permissions"></a>Controle e permissões de acesso

Você pode conceder [permissões](#device-provisioning-service-permissions) das seguintes maneiras:

* **Políticas de autorização de acesso compartilhado**. As políticas de acesso compartilhado podem conceder qualquer combinação de [permissões](#device-provisioning-service-permissions). Você pode definir políticas no [Portal do Azure][lnk-management-portal] ou de forma programática usando as [APIs REST do Serviço de Provisionamento de Dispositivo][lnk-resource-provider-apis]. Um serviço de provisionamento recém-criado tem a seguinte política padrão:

* **provisioningserviceowner**: Política com todas as permissões.

> [!NOTE]
> Para obter informações detalhadas, consulte [permissões](#device-provisioning-service-permissions).

## <a name="authentication"></a>Authentication

O Serviço de Provisionamento do Hub IoT do Azure concede acesso aos pontos de extremidade, verificando um token com base nas políticas de acesso compartilhado. As credenciais de segurança, como as chaves simétricas, nunca são enviadas pela conexão.

> [!NOTE]
> O provedor de recursos do Serviço de Provisionamento de Dispositivo é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Azure Resource Manager][lnk-azure-resource-manager].

Para saber mais sobre como construir e usar os tokens de segurança, veja a próxima seção.

O HTTP é o único protocolo com suporte e implementa a autenticação incluindo um token válido no cabeçalho da solicitação **Authorization**.

#### <a name="example"></a>Exemplo
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Os [SDKs do Serviço de Provisionamento de Dispositivo de IoT do Azure][lnk-sdks] geram tokens automaticamente durante a conexão com o serviço.

## <a name="security-tokens"></a>Tokens de segurança

Os Serviços de Provisionamento de Dispositivo usam tokens de segurança para autenticar serviços para evitar o envio de chaves durante a transmissão. Além disso, os tokens de segurança têm limite de escopo e de prazo de validade. Os [SDKs do Serviço de Provisionamento de Dispositivo do IoT do Azure][lnk-sdks] geram tokens automaticamente sem precisar de configuração especial. Alguns cenários exigem que você gere e use tokens de segurança diretamente. Esses cenários incluem o uso direto da superfície HTTP.

### <a name="security-token-structure"></a>Estrutura do token de segurança

Você usa tokens de segurança para conceder acesso vinculado ao tempo para serviços para uma funcionalidade específica no Serviço de Provisionamento de Dispositivo de IoT. Para obter autorização para se conectar ao serviço de provisionamento, os dispositivos e serviços devem enviar tokens de segurança assinados com um acesso compartilhado ou uma chave simétrica.

Um token assinado com uma chave de acesso compartilhada concede acesso a todas funcionalidades associadas às permissões da política de acesso compartilhado. 

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Veja os valores esperados:

| Value | DESCRIÇÃO |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 no formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: a chave é decodificada da base64 e usada como chave para executar o cálculo de HMAC-SHA256.|
| {expiry} |As cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970. |
| {URL-encoded-resourceURI} | Codificação de URL em letras minúsculas do URI de recurso em letras minúsculas. Prefixo de URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome de host do Serviço de Provisionamento de Dispositivo de IoT (sem protocolo). Por exemplo, `mydps.azure-devices-provisioning.net`. |
| {policyName} |O nome da política de acesso compartilhado a qual se refere esse token. |

**Observação sobre o prefixo**: o prefixo URI é computado por segmento e não por caractere. Por exemplo, `/a/b` é um prefixo para `/a/b/c`, mas não para `/a/bc`.

O seguinte snippet de Node.js mostra uma função chamada **generateSasToken** que calcula o token a partir das entradas `resourceUri, signingKey, policyName, expiresInMins`. As seções a seguir detalharão como inicializar as entradas diferentes para casos de uso com token diferentes.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Como comparação, o código Python equivalente para gerar um token de segurança é:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Como o prazo de validade do token é validado em computadores do Serviço de Provisionamento de Dispositivo de IoT, o descompasso no relógio do computador que gera o token deve ser mínimo.

### <a name="use-security-tokens-from-service-components"></a>Usar tokens de segurança de componentes de serviço

Os componentes de serviço só podem gerar tokens de segurança usando políticas de acesso compartilhado que concedem as permissões apropriadas, conforme explicado anteriormente.

Veja as funções de serviço expostas nos pontos de extremidade:

| Ponto de extremidade | Funcionalidade |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Fornece operações de registro de dispositivo com o Serviço de Provisionamento de Dispositivo. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Fornece operações para gerenciar grupos de registro de dispositivo. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Fornece operações para recuperar e gerenciar o status de registros do dispositivo. |


Por exemplo, um serviço gerado usando uma política de acesso compartilhado criada anteriormente chamada **enrollmentread** criaria um token com os seguintes parâmetros:

* URI de recurso: `{mydps}.azure-devices-provisioning.net`,
* chave de assinatura: uma das chaves da política `enrollmentread` ,
* nome da política: `enrollmentread`,
* qualquer validade time.backn

![Criar uma política de acesso compartilhado para a instância do serviço de Provisionamento de Dispositivos no portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concederia acesso para ler todos os relatórios do registro, seria:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre como controlar o acesso ao seu Serviço de Provisionamento de Dispositivo de IoT.

### <a name="device-provisioning-service-permissions"></a>Permissões do Serviço de Provisionamento de Dispositivo

A tabela a seguir lista as permissões que você pode usar para controlar o acesso ao seu Serviço de Provisionamento de Dispositivo de IoT.

| Permissão | Observações |
| --- | --- |
| **ServiceConfig** |Concede acesso para alterar as configurações de serviço. <br/>Essa permissão é usada pelos serviços de nuvem back-end. |
| **EnrollmentRead** |Concede acesso de leitura para os registros do dispositivo e os grupos de registro. <br/>Essa permissão é usada pelos serviços de nuvem back-end. |
| **EnrollmentWrite** |Concede acesso de gravação para os registros do dispositivo e os grupos de registro. <br/>Essa permissão é usada pelos serviços de nuvem back-end. |
| **RegistrationStatusRead** |Concede acesso de leitura ao status de registro do dispositivo. <br/>Essa permissão é usada pelos serviços de nuvem back-end. |
| **RegistrationStatusWrite**  |Concede acesso de exclusão ao status de registro do dispositivo. <br/>Essa permissão é usada pelos serviços de nuvem back-end. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
