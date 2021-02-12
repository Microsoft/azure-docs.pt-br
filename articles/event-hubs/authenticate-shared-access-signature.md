---
title: Autenticar o acesso aos hubs de eventos do Azure com assinaturas de acesso compartilhado
description: Este artigo mostra como autenticar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986481"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autenticar o acesso aos recursos de hubs de eventos usando SAS (assinaturas de acesso compartilhado)
A SAS (assinatura de acesso compartilhado) oferece controle granular sobre o tipo de acesso que você concede aos clientes que têm a assinatura de acesso compartilhado. Aqui estão alguns dos controles que você pode definir em uma SAS: 

- O intervalo em que a SAS é válida, incluindo a hora de início e a hora de expiração.
- As permissões concedidas pelas SAS. Por exemplo, uma SAS para um namespace de hubs de eventos pode conceder a permissão Listen, mas não a permissão Send.
- Somente clientes que apresentam credenciais válidas podem enviar dados para um hub de eventos.
- Um cliente não pode representar outro cliente.
- Um cliente invasor pode ser impedido de enviar dados para um hub de eventos.

Este artigo aborda a autenticação do acesso a recursos de hubs de eventos usando SAS. Para saber mais sobre como **autorizar** o acesso aos recursos de hubs de eventos usando SAS, consulte [Este artigo](authorize-access-shared-access-signature.md). 

> [!NOTE]
> A Microsoft recomenda que você use as credenciais do Azure AD quando possível como uma prática recomendada de segurança, em vez de usar as assinaturas de acesso compartilhado, que podem ser mais facilmente comprometidas. Embora você possa continuar a usar as SAS (assinaturas de acesso compartilhado) para conceder acesso refinado aos seus recursos de hubs de eventos, o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar com a revogação de uma SAS comprometida.
> 
> Para obter mais informações sobre a integração do Azure AD nos hubs de eventos do Azure, consulte [autorizar o acesso aos hubs de eventos usando o Azure ad](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Configurando para autenticação SAS
Você pode configurar a regra de autorização de acesso compartilhado EventHubs em um namespace de hubs de eventos ou uma entidade (instância de Hub de eventos ou tópico Kafka em um hub de eventos). Não há suporte para a configuração de uma regra de autorização de acesso compartilhado em um grupo de consumidores no momento, mas você pode usar regras configuradas em um namespace ou entidade para proteger o acesso ao grupo de consumidores. 

A imagem a seguir mostra como as regras de autorização se aplicam em entidades de exemplo. 

![Configurar regra de autorização](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Neste exemplo, o namespace dos hubs de eventos de exemplo (ExampleNamespace) tem duas entidades: EH1 e topic1. As regras de autorização são definidas no nível da entidade e também no nível do namespace.  

As regras de autorização manageRuleNS, sendRuleNS e listenRuleNS se aplicam à instância do hub de eventos EH1 e ao tópico T1. As regras de autorização listenRule-eh e sendRule-eh se aplicam somente à instância de autorização do hub de eventos EH1 e sendRuleT aplicam-se somente ao tópico topic1. 

Ao usar a regra de autorização sendRuleNS, os aplicativos cliente podem enviar para EH1 e topic1. Quando a regra de autorização sendRuleT é usada, ela impõe o acesso granular somente ao topic1 e, portanto, os aplicativos cliente que usam essa regra para acesso agora não podem enviar para EH1, mas apenas para topic1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de Assinatura de Acesso Compartilhado 
Qualquer cliente que tenha acesso ao nome de uma regra de autorização e a uma de suas chaves de assinatura pode gerar um token SAS. O token é gerado ao criar uma cadeia de caracteres no seguinte formato:

- `se`  – Instantâneo de expiração do token. Inteiro refletindo segundos desde a época 00:00:00 UTC em 1 de janeiro de 1970 (época do UNIX) quando o token expira
- `skn` – Nome da regra de autorização, que é o nome da chave SAS.
- `sr` – URI do recurso que está sendo acessado.
- `sig` Signature.

A cadeia de caracteres de assinatura é o hash SHA-256 calculado sobre o URI de recurso (escopo, conforme descrito na seção anterior) e a representação de cadeia de caracteres do momento de expiração do token, separados por CRLF.

O cálculo de hash é semelhante ao seguinte pseudocódigo e retorna um valor de hash de 256 bits/32 bytes. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O token contém os valores não hash para que o destinatário possa recalcular o hash com os mesmos parâmetros, verificando se o emissor possui uma chave de assinatura válida.

O URI do recurso é o URI completo do recurso do Barramento de Serviço ao qual o acesso é solicitado. Por exemplo, http:// <namespace> . ServiceBus.Windows.NET/ <entityPath> ou `sb://<namespace>.servicebus.windows.net/<entityPath>;` isto é, `http://contoso.servicebus.windows.net/eventhubs/eh1` .

O URI deve ser codificado por percentual.

A regra de autorização de acesso compartilhado usada para assinar deve ser configurada na entidade especificada por esse URI ou por um de seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/eventhubs/eh1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos prefixados com o <resourceURI> usado na cadeia de caracteres de assinatura.

> [!NOTE]
> Você gera um token de acesso para hubs de eventos usando a política de acesso compartilhado. Para obter mais informações, consulte [política de autorização de acesso compartilhado](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Gerando uma assinatura (token) de uma política 
A seção a seguir mostra a geração de um token SAS usando políticas de assinatura de acesso compartilhado,

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autenticando Publicadores de hubs de eventos com SAS 
Um editor de eventos define um ponto de extremidade virtual para um hub de eventos. O Publicador só pode ser usado para enviar mensagens a um hub de eventos e não receber mensagens.

Normalmente, um hub de eventos emprega um editor por cliente. Todas as mensagens enviadas a um dos publicadores de um hub de eventos são enfileiradas nesse hub de eventos. Os editores habilitam o controle de acesso refinado.

Cada cliente dos hubs de eventos recebe um token exclusivo que é carregado no cliente. Os tokens são produzidos de modo que cada token exclusivo conceda acesso a um Publicador exclusivo diferente. Um cliente que contém um token só pode enviar para um Publicador e nenhum outro editor. Se vários clientes compartilham o mesmo token, cada um deles compartilha o Publicador.

Todos os tokens são atribuídos com chaves SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os clientes não estão cientes da chave, o que impede os clientes de fabricar tokens. Os clientes operam nos mesmos tokens até que expirem.

Por exemplo, para definir regras de autorização com escopo para enviar/publicar apenas para os hubs de eventos, você precisa definir uma regra de autorização de envio. Isso pode ser feito em um nível de namespace ou fornecer um escopo mais granular a uma entidade específica (instância de hubs de eventos ou um tópico). Um cliente ou um aplicativo com escopo desse acesso granular é chamado de editor de hubs de eventos. Para fazer isso, siga estas etapas:

1. Crie uma chave SAS na entidade que você deseja publicar para atribuir o escopo de **envio** nela. Para obter mais informações, consulte [políticas de autorização de acesso compartilhado](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Gere um token SAS com um tempo de expiração para um Publicador específico usando a chave gerada em etapa 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Forneça o token para o cliente Publicador, que só pode ser enviado à entidade e ao Publicador ao qual o token concede acesso.

    Depois que o token expira, o cliente perde seu acesso para enviar/publicar para a entidade. 


> [!NOTE]
> Embora não seja recomendado, é possível equipar dispositivos com tokens que concedem acesso a um hub de eventos ou a um namespace. Qualquer dispositivo que mantém esse token pode enviar mensagens diretamente para esse Hub de eventos. Além disso, o dispositivo não pode ser incluídos de enviar para esse Hub de eventos.
> 
> É sempre recomendável fornecer escopos específicos e granulares.

> [!IMPORTANT]
> Depois que os tokens são criados, cada cliente é configurado com seu próprio token exclusivo.
>
> Quando o cliente envia dados para um hub de eventos, ele marca sua solicitação com o token. Para evitar que um invasor intercepte e roube o token, a comunicação entre o cliente e o hub de eventos deve ocorrer em um canal criptografado.
> 
> Se um token for roubado por um invasor, este poderá representar o cliente cujo token foi roubado. Inclusão na lista um Publicador, renderiza esse cliente inutilizável até receber um novo token que usa um Publicador diferente.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autenticando consumidores de hubs de eventos com SAS 
Para autenticar aplicativos de back-end que consomem dos dados gerados por produtores de hubs de eventos, a autenticação de token dos hubs de eventos exige que seus clientes tenham os direitos **gerenciar** ou os privilégios de **escuta** atribuídos ao namespace de seus hubs de eventos ou à instância ou ao tópico do hub de eventos. Os dados são consumidos dos hubs de eventos usando grupos de consumidores. Embora a política SAS forneça o escopo granular, esse escopo é definido somente no nível da entidade e não no nível do consumidor. Isso significa que os privilégios definidos no nível do namespace ou na instância ou no nível do tópico do hub de eventos serão aplicados aos grupos de consumidores dessa entidade.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Autorizar usando SAS](authenticate-shared-access-signature.md)
- [Autorizar usando o controle de acesso baseado em função do Azure (RBAC do Azure)](authenticate-shared-access-signature.md)
- [Saiba mais sobre os hubs de eventos](event-hubs-about.md)

Consulte os seguintes artigos relacionados:

- [Autenticar solicitações para hubs de eventos do Azure por meio de um aplicativo usando Azure Active Directory](authenticate-application.md)
- [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md)
- [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)
