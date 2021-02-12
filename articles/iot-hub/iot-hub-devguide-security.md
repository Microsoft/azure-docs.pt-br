---
title: Entender a segurança de Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ como controlar o acesso ao Hub IoT para aplicativos de dispositivos e de back-end. Inclui informações sobre tokens de segurança e suporte a certificados x. 509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 3ddc8c78bac47ed85266037341328585e3c7cb1c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825133"
---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT

Esta seção descreve as opções para proteger o Hub IoT. O Hub IoT usa *permissões* para conceder acesso a cada ponto de extremidade do Hub IoT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

Este artigo apresenta:

* As permissões diferentes que você pode conceder a um aplicativo de dispositivo ou back-end para acessar o hub IoT.
* O processo de autenticação e os tokens que ele usa para verificar permissões.
* Como analisar credenciais para limitar o acesso a recursos específicos.
* Suporte de Hub IoT para certificados X.509.
* Mecanismos de autenticação de dispositivo personalizados que usam registros de identidade de dispositivo existente ou esquemas de autenticação.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Você deve ter permissões adequadas para acessar qualquer um dos pontos de extremidade de Hub IoT. Por exemplo, um dispositivo deve incluir um token contendo as credenciais de segurança juntamente com todas as mensagens que ele envia para o Hub IoT.

## <a name="access-control-and-permissions"></a>Controle e permissões de acesso

Você pode conceder [permissões](#iot-hub-permissions) das seguintes maneiras:

* **Políticas de acesso compartilhado no nível do Hub IoT**. As políticas de acesso compartilhado podem conceder qualquer combinação de [permissões](#iot-hub-permissions). É possível definir políticas no [portal do Azure](https://portal.azure.com) programaticamente, usando as [APIs REST de Recursos do Hub IoT](/rest/api/iothub/iothubresource) ou usando a CLI [az iot hub policy](/cli/azure/iot/hub/policy). Um hub IoT recém-criado tem as seguintes políticas padrão:
  
  | Política de acesso compartilhado | Permissões |
  | -------------------- | ----------- |
  | iothubowner | Toda Permissão |
  | serviço | Permissões **ServiceConnect** |
  | dispositivo | Permissões **DeviceConnect** |
  | registryRead | Permissões **RegistryRead** |
  | registryReadWrite | Permissões **RegistryRead** e **RegistryWrite** |

* **Credenciais de segurança de acordo com o dispositivo**. Cada Hub IoT contém um [registro de identidade](iot-hub-devguide-identity-registry.md) Para cada dispositivo nesse registro de identidade, você pode configurar credenciais de segurança que concedem permissões de **DeviceConnect** com escopo nos pontos de extremidade correspondentes do dispositivo.

Por exemplo, em uma solução de IoT típica:

* O componente de gerenciamento de dispositivo usa a política *registryReadWrite* .
* O componente de processador de eventos usa a política *service* .
* O componente de lógica de negócios do dispositivo em tempo de execução usa a política *service*.
* Os dispositivos individuais se conectam usando as credenciais armazenadas no registro de identidade do Hub IoT.

> [!NOTE]
> Para obter informações detalhadas, consulte [permissões](#iot-hub-permissions).

## <a name="authentication"></a>Autenticação

O Hub IoT do Azure concede acesso aos pontos de extremidade, verificando um token com base nas políticas de acesso compartilhado e nas credenciais de segurança de registro de identidade.

As credenciais de segurança, como as chaves simétricas, nunca são enviadas pela conexão.

> [!NOTE]
> O provedor de recursos do Hub IoT do Azure é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Para saber mais sobre como construir e usar os tokens de segurança, confira o artigo [Tokens de segurança do Hub IoT](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Especificações de protocolo

Cada protocolo com suporte, como MQTT, AMQP e HTTPS, transporta os tokens de maneiras diferentes.

Ao usar MQTT, o pacote CONNECT tem a deviceId como a ClientId, no campo `{iothubhostname}/{deviceId}` Nome de Usuário e um token SAS no campo Senha. `{iothubhostname}` deve ser o CName completo do Hub IoT (por exemplo, contoso.azure-devices.net).

Quando usa [AMQP](https://www.amqp.org/), o Hub IoT dá suporte ao [SASL PLAIN](https://tools.ietf.org/html/rfc4616) e à [Segurança baseada em declarações AMQP](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Se você usar a segurança baseada em declarações AMQP, o padrão especificará como transmitir esses tokens.

Para SASL PLAIN, o **nome de usuário** pode ser:

* `{policyName}@sas.root.{iothubName}` se forem usados tokens de nível do Hub IoT.
* `{deviceId}@sas.{iothubname}` se forem usados tokens no escopo do dispositivo.

Em ambos os casos, o campo de senha contém o token, conforme descrito no artigo [Tokens de segurança do Hub IoT](iot-hub-devguide-security.md#security-tokens).

O HTTPS implementa a autenticação incluindo um token válido no cabeçalho da solicitação **Authorization**.

#### <a name="example"></a>Exemplo

Nome de usuário (a DeviceId diferencia maiúsculas de minúsculas): `iothubname.azure-devices.net/DeviceId`

Senha (é possível gerar um token SAS com o comando de extensão da CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) ou o [Azure IoT Tools para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Os [SDKs do IoT do Azure](iot-hub-devguide-sdks.md) geram tokens automaticamente durante a conexão com o serviço. Em alguns casos, os SDKs do IoT do Azure não dão suporte a todos os protocolos ou a todos os métodos de autenticação.

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para SASL PLAIN

Ao usar o SASL PLAIN com AMQP, um cliente que está se conectando a um hub IoT pode usar um único token para cada conexão TCP. Quando o token expirar, a conexão TCP será desconectada do serviço, disparando uma reconexão. Esse comportamento, embora não seja problemático para um aplicativo back-end, é prejudicial para um aplicativo de dispositivo, pelos seguintes motivos:

* Gateways normalmente se conectam em nome de vários dispositivos. Ao usar SASL PLAIN, eles precisam criar uma conexão TCP distinta para cada dispositivo que se conecta a um hub IoT. Esse cenário aumenta de forma considerável o consumo de energia e de recursos de rede, além de aumentar a latência de cada conexão de dispositivo.

* Os dispositivos com limitações de recursos são afetados de forma adversa pelo aumento do uso de recursos para se reconectar após cada expiração do token.

## <a name="scope-iot-hub-level-credentials"></a>Escopo das credenciais no nível do Hub IoT

Você pode definir o escopo das políticas de segurança no nível do Hub IoT por meio da criação de tokens com um URI de recursos restrito. Por exemplo, o ponto de extremidade para enviar mensagens do dispositivo para a nuvem é **/devices/{deviceId}/messages/events**. Você também pode usar uma política de acesso compartilhado no nível do Hub IoT com permissões **DeviceConnect** para assinar um token cujo resourceURI é **/devices/{deviceId}** . Essa abordagem cria um token que pode ser usado somente para enviar mensagens em nome do dispositivo **deviceId**.

Esse mecanismo é semelhante à [política do editor dos Hubs de Eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) e permite implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança

O Hub IoT usa tokens de segurança para autenticar dispositivos e serviços a fim de evitar o envio de chaves durante a transmissão. Além disso, os tokens de segurança têm limite de escopo e de prazo de validade. Os [SDKs do IoT do Azure](iot-hub-devguide-sdks.md) geram tokens automaticamente sem precisar de configuração especial. Alguns cenários exigem que você gere e use tokens de segurança diretamente. Esses cenários incluem:

* O uso direto de superfícies de MQTT, AMQP ou HTTPS.

* A implementação do padrão de serviço de token, conforme explicado em [Autenticação de dispositivo personalizada](iot-hub-devguide-security.md#custom-device-and-module-authentication).

O Hub IoT também permite que os dispositivos se autentiquem no Hub IoT usando [X.509 certificates](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Estrutura do token de segurança

Você usa tokens de segurança para conceder aos dispositivos e serviços acesso de tempo limitado à funcionalidade específica do Hub IoT. Para obter autorização para se conectar ao Hub IoT, os dispositivos e serviços devem enviar tokens de segurança assinados com um acesso compartilhado ou uma chave simétrica. Essas chaves são armazenadas com uma identidade de dispositivo no registro de identidade.

Um token assinado com uma chave de acesso compartilhada concede acesso a todas funcionalidades associadas às permissões da política de acesso compartilhado. Um token assinado com uma chave simétrica de uma identidade de dispositivo apenas concede a permissão **DeviceConnect** para a identidade do dispositivo associado.

O token de segurança tem o seguinte formato:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Veja os valores esperados:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 no formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: a chave é decodificada da base64 e usada como chave para executar o cálculo de HMAC-SHA256. |
| {resourceURI} |Prefixo de URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome de host do Hub IoT (sem protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiry} |As cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970. |
| {URL-encoded-resourceURI} |Codificação de URL em letras minúsculas do URI de recurso em letras minúsculas |
| {policyName} |O nome da política de acesso compartilhado a qual se refere esse token. Ausente se o token se referir às credenciais de registro do dispositivo. |

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
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Como comparação, o código Python equivalente para gerar um token de segurança é:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

A funcionalidade em C# para gerar um token de segurança é:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```

Para o Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Como o prazo de validade do token é validado em computadores do Hub IoT, o descompasso no relógio do computador que gera o token deve ser mínimo.

### <a name="use-sas-tokens-in-a-device-app"></a>Usar tokens SAS em um aplicativo de dispositivo

Há duas maneiras de obter permissões **DeviceConnect** com o Hub IoT com tokens de segurança: usar a [chave de dispositivo simétrica do registro de identidade](#use-a-symmetric-key-in-the-identity-registry) ou usar uma [chave de acesso compartilhado](#use-a-shared-access-policy).

Lembre-se que toda funcionalidade acessível por meio de dispositivos fica exposta por padrão em pontos de extremidade com o prefixo `/devices/{deviceId}`.

> [!IMPORTANT]
> A única maneira de o Hub IoT autenticar um dispositivo específico é usando a chave simétrica de identidade do dispositivo. Em casos nos quais uma política de acesso compartilhado é usada para acessar a funcionalidade do dispositivo, a solução deve considerar o componente emissor do token de segurança como um subcomponente confiável.

Os pontos de extremidade voltados para o dispositivo são (independentemente do protocolo):

| Ponto de extremidade | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Enviar mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Receber mensagens da nuvem para o dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Usar uma chave simétrica no registro de identidade

Ao usar a chave simétrica da identidade de um dispositivo para gerar um token, o elemento policyName (`skn`) do token é omitido.

Por exemplo, um token criado para acessar todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave simétrica para a identidade `{device id}` ,
* sem nome de política,
* qualquer data de validade

Um exemplo usando a função Node.js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

O resultado, que concede acesso a todas as funcionalidades para o dispositivo1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> É possível gerar um token SAS com o comando de extensão da CLI [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token) ou o [Azure IoT Tools para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Usar uma política de acesso compartilhado

Ao criar um token a partir de uma política de acesso compartilhado, defina o campo `skn` como o nome da política. Essa política deve conceder a permissão **DeviceConnect**.

Os dois cenários principais para usar as políticas de acesso compartilhado para acessar a funcionalidade do dispositivo são:

* [gateways de protocolo em nuvem](iot-hub-devguide-endpoints.md),
* [serviços de token](iot-hub-devguide-security.md#custom-device-and-module-authentication) usados para implementar esquemas de autenticação personalizada.

Como a política de acesso compartilhado pode conceder acesso de conexão como qualquer dispositivo, é importante usar o URI do recurso correto durante a criação dos tokens de segurança. Essa configuração é especialmente importante para os serviços de token, que precisam estabelecer o escopo do token para um dispositivo específico usando o URI do recurso. Esse ponto é menos relevante para os gateways de protocolo, pois eles já estão mediando o tráfego para todos os dispositivos.

Por exemplo, um serviço de token usando a política de acesso compartilhado criada anteriormente chamada **dispositivo** criaria um token com os seguintes parâmetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma das chaves da política `device` ,
* nome da política: `device`,
* qualquer data de validade

Um exemplo usando a função Node.js anterior seria:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concede acesso a todas as funcionalidades para o dispositivo1, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Um gateway de protocolo poderia usar o mesmo token para todos os dispositivos, simplesmente definindo o URI do recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Usar tokens de segurança de componentes de serviço

Os componentes de serviço só podem gerar tokens de segurança usando políticas de acesso compartilhado que concedem as permissões apropriadas, conforme explicado anteriormente.

Veja as funções de serviço expostas nos pontos de extremidade:

| Ponto de extremidade | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, recuperar e excluir as identidades do dispositivo. |
| `{iot hub host name}/messages/events` |Receber mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/servicebound/feedback` |Receber comentários das mensagens da nuvem para o dispositivo. |
| `{iot hub host name}/devicebound` |Enviar mensagens da nuvem para o dispositivo. |

Por exemplo, um serviço que usa a política de acesso compartilhado criada anteriormente chamada **registryRead** criaria um token com os seguintes parâmetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma das chaves da política `registryRead` ,
* nome da política: `registryRead`,
* qualquer data de validade

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

O resultado, que concederia acesso de leitura em todas as identidades de dispositivo, seria:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Certificados X.509 com suporte

Você pode usar qualquer certificado X. 509 para autenticar um dispositivo com o IoT Hub, carregando uma impressão digital do certificado ou uma autoridade de certificação (CA) do Hub IoT do Azure. A autenticação usando impressões digitais do certificado verifica se a impressão digital apresentada corresponde à impressão digital configurada. Autenticação usando uma autoridade de certificado valida a cadeia de certificados. De qualquer forma, o handshake do TLS exige que o dispositivo tenha um certificado e uma chave privada válidos. Veja a especificação do TLS para obter detalhes, por exemplo: [RFC 5246 – o protocolo TLS versão 1.2](https://tools.ietf.org/html/rfc5246/).

Os certificados compatíveis incluem:

* **Um certificado X.509 existente**. Talvez um dispositivo já tenha um certificado X.509 associado a ele. O dispositivo pode usar este certificado para se autenticar no Hub IoT. Funciona com impressão digital ou com a atentificação CA. 

* **Certificado X.509 assinado por autoridade de certificação**. Para identificar um dispositivo e autenticá-lo com um Hub IoT, você pode usar um certificado X.509 gerado e assinado por uma Autoridade de Certificação (AC). Funciona com impressão digital ou com a atentificação CA.

* **Um certificado X-509 gerado automaticamente e autoassinado**. Um fabricante de dispositivos ou um implantador interno pode gerar esses certificados e armazenar a chave privada correspondente (e o certificado) no dispositivo. Você pode usar ferramentas como o [OpenSSL](https://www.openssl.org/) e o utilitário [SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) do Windows para essa finalidade. Só funciona com autenticação de impressão digital.

Um dispositivo pode usar um certificado X.509 ou um token de segurança para autenticação, mas não ambos. Com a autenticação de certificado X. 509, verifique se você tem uma estratégia em vigor para lidar com a substituição de certificado quando um certificado existente expirar.

Não há suporte para a funcionalidade a seguir em dispositivos que usam autenticação de autoridade de certificação X. 509:

* HTTPS, MQTT sobre WebSockets e AMQP sobre protocolos WebSockets.
* Carregamentos de arquivos (todos os protocolos).

Para obter mais informações sobre autenticação usando autoridade de certificação, consulte [Autenticação de dispositivo usando Certificados de Autoridade de Certificação X.509](iot-hub-x509ca-overview.md). Para obter informações sobre como carregar e verificar uma autoridade de certificação com o Hub IoT, consulte [Configurar a segurança X. 509 no Hub IOT do Azure](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrar um certificado X.509 para um dispositivo

O [SDK do Serviço IoT do Azure para C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versão 1.0.8 ou superior) dá suporte ao registro de um dispositivo que usa um certificado X.509 para autenticação. Outras APIs, como a importação/exportação de dispositivos também oferece suporte a certificados X.509.

Também é possível usar o comando de extensão da CLI [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) para configurar certificados X.509 para dispositivos.

### <a name="c-support"></a>Suporte a C\#

A classe **RegistryManager** fornece uma maneira programática de registrar um dispositivo. Em particular, os métodos **AddDeviceAsync** e **UpdateDeviceAsync** permitem que você se registre e atualize um dispositivo no registro de identidade do Hub IoT. Esses dois métodos têm uma instância **Dispositivo** como entrada. A classe **Device** inclui uma propriedade **Authentication** que permite especificar as impressões digitais primárias e secundárias do certificado X.509. A impressão digital representa um hash SHA256 do certificado X.509 (armazenado usando a codificação binária DER). Você tem a opção de especificar uma impressão digital primária ou uma impressão digital secundária, ou ambas. As impressões digitais primárias e secundárias têm suporte para lidar com cenários de substituição do certificado.

Veja um exemplo de snippet de código em C\# para registrar um dispositivo usando uma impressão digital do certificado X.509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Usar um certificado X.509 durante operações em tempo de execução

O [SDK do dispositivo IoT do Azure para .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (versão 1.0.11 ou superior) oferece suporte ao uso de certificados X.509.

### <a name="c-support"></a>Suporte a C\#

A classe **DeviceAuthenticationWithX509Certificate** dá suporte à criação de instâncias **DeviceClient** usando um certificado X.509. O certificado X.509 deve estar no formato PFX (também chamado de PKCS nº 12) que inclui a chave privada.

Veja um snippet de código de exemplo:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Autenticação personalizada de dispositivo e módulo

Você pode usar o [registro de identidade](iot-hub-devguide-identity-registry.md) do Hub IoT para configurar credenciais de segurança e controle de acesso por dispositivo/módulo usando [tokens](iot-hub-devguide-security.md#security-tokens). Se uma solução IoT já tiver um registro de identidade personalizado e/ou esquema de autenticação, considere a criação de um *serviço de token* para integrar essa infraestrutura existente ao Hub IoT. Dessa forma, você pode usar outros recursos de IoT em sua solução.

Um serviço de token é um serviço de nuvem personalizado. Ele usa uma *política de acesso compartilhado* do Hub IoT com permissões **DeviceConnect** ou **ModuleConnect** para criar tokens *device-scoped* ou *module-scoped*. Esses tokens permitem que um dispositivo ou módulo se conectem ao seu Hub IoT.

![Etapas do padrão do serviço de token](./media/iot-hub-devguide-security/tokenservice.png)

Estas são as principais etapas do padrão de serviço do token:

1. Crie uma política de acesso compartilhado do Hub IoT com permissões **DeviceConnect** ou **ModuleConnect** para seu Hub IoT. Você pode criar essa política no [portal do Azure](https://portal.azure.com) ou de forma programática. O serviço de token usa essa política para assinar os tokens criados.

2. Quando um dispositivo/módulo precisar acessar o Hub IoT, ele solicitará um token assinado ao serviço de token. O dispositivo pode autenticar com o seu esquema personalizado de registro/autenticação de identidade para determinar a identidade do dispositivo/módulo usada pelo serviço de token para criar o token.

3. O serviço de token retorna um token. O token é criado usando `/devices/{deviceId}` ou `/devices/{deviceId}/module/{moduleId}` como `resourceURI`, com `deviceId` como o dispositivo que está sendo autenticado ou `moduleId` como o módulo que está sendo autenticado. O serviço de token usa a política de acesso compartilhado para construir o token.

4. O dispositivo/módulo usa o token diretamente com o Hub IoT.

> [!NOTE]
> Você pode usar a classe do .NET [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) ou a classe Java [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) para criar um token no serviço de token.

O serviço de token pode definir a expiração do token como desejado. Quando o token expira, o Hub IoT rompe a conexão do dispositivo/módulo. Em seguida, o dispositivo/módulo deve solicitar um novo token ao serviço de token. Um tempo de expiração curto aumenta a carga no dispositivo/módulo e no serviço de token.

Para que um dispositivo/módulo conecte-se ao hub, você ainda deverá adicioná-lo ao registro de identidade do Hub IoT — mesmo que esteja usando um token e não uma chave para conectar. Portanto, você pode continuar a usar o controle de acesso por dispositivo/por módulo habilitando ou desabilitando as identidades de dispositivo/módulo no [registro de identidade](iot-hub-devguide-identity-registry.md). Essa abordagem reduz os riscos de usar tokens com tempos de expiração longos.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado

O padrão de serviço do token é a maneira recomendada de implementar um esquema personalizado de registro/autenticação de identidade com o Hub IoT. Esse padrão é recomendado porque o Hub IoT continua tratando a maior parte do tráfego da solução. No entanto, se o esquema de autenticação personalizado estiver entremeado com o protocolo, você poderá exigir um *gateway personalizado* para processar todo o tráfego. Um exemplo de tal cenário é usar [TLS (Transport Layer Security) e as PSKs (chaves pré-compartilhadas)](https://tools.ietf.org/html/rfc4279). Para saber mais, confira o artigo sobre [gateway do protocolo](iot-hub-protocol-gateway.md).

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre como controlar o acesso ao seu Hub IoT.

## <a name="iot-hub-permissions"></a>Permissões de Hub IoT

A tabela a seguir lista as permissões que você pode usar para controlar o acesso ao seu hub IoT.

| Permissão | Observações |
| --- | --- |
| **RegistryRead** |Concede acesso de leitura ao Registro de identidade. Para saber mais, confira [Registro de identidade](iot-hub-devguide-identity-registry.md). <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **RegistryReadWrite** |Concede acesso de leitura e gravação ao Registro de identidade. Para saber mais, confira [Registro de identidade](iot-hub-devguide-identity-registry.md). <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **ServiceConnect** |Concede acesso aos pontos de extremidade de comunicação e de monitoramento voltados para o serviço de nuvem. <br/>Concede permissão para recebimento de mensagens do dispositivo para a nuvem, envio de mensagens da nuvem para o dispositivo e obtenção das confirmações de entrega correspondentes. <br/>Concede permissão para recuperar as confirmações de entrega dos uploads de arquivos. <br/>Concede permissão para acessar dispositivos ou módulos gêmeos para atualizar as marcas e propriedades desejadas, recuperar propriedades relatadas e executar consultas. <br/>Essa permissão é usada pelos serviços de nuvem de back-end. |
| **DeviceConnect** |Concede acesso aos pontos de extremidade de comunicação voltados para o dispositivo. <br/>Concede permissão de envio de mensagens do dispositivo para a nuvem e de recebimento de mensagens da nuvem para o dispositivo. <br/>Concede permissão para executar o upload do arquivo de um dispositivo. <br/>Concede permissão para receber notificações de propriedade do dispositivo gêmeo desejado e atualizar propriedades relatadas do dispositivo gêmeo. <br/>Concede permissão para executar o uploads de arquivo. <br/>Essa permissão é usada por dispositivos. |

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md) descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.

* [Limitação e cotas](iot-hub-devguide-quotas-throttling.md) descreve as cotas e os comportamentos de limitação que se aplicam ao serviço do Hub IoT.

* [SDKs do dispositivo e do serviço IoT do Azure](iot-hub-devguide-sdks.md) lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.

* [Linguagem de consulta do Hub IoT](iot-hub-devguide-query-language.md) descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.

* O [suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md) fornece mais informações sobre o suporte do Hub IoT ao protocolo MQTT.

* [RFC 5246 – o protocolo TLS versão 1.2](https://tools.ietf.org/html/rfc5246/) fornece mais informações sobre a autenticação TLS.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como controlar o acesso ao Hub IoT, pode ser interessante ler os seguintes tópicos do Guia do desenvolvedor do Hub IoT:

* [Usar dispositivos gêmeos para sincronizar o estado e as configurações](iot-hub-devguide-device-twins.md)
* [Invocar um método direto em um dispositivo](iot-hub-devguide-direct-methods.md)
* [Agendar trabalhos em vários dispositivos](iot-hub-devguide-jobs.md)

Se você quiser experimentar alguns dos conceitos descritos neste artigo, consulte os seguintes tutoriais de Hub IoT:

* [Introdução ao Hub IoT do Azure](quickstart-send-telemetry-node.md)
* [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT](iot-hub-csharp-csharp-c2d.md)
* [Como processar mensagens de dispositivo para nuvem do Hub IoT](tutorial-routing.md)
