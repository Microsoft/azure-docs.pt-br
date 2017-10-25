---
title: "Autenticação do Barramento de Serviço do Azure com Assinaturas de Acesso Compartilhado | Microsoft Docs"
description: "Visão geral da Autenticação do Barramento de Serviço usando a visão geral de Assinaturas de Acesso Compartilhado, detalhes sobre a autenticação SAS com o Barramento de Serviço do Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: a2760072acb7c62204759f3ec0d3cb9899460f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Autenticação do Barramento de Serviço com Assinaturas de Acesso Compartilhado

As *Assinaturas de Acesso Compartilhado* (SAS) são o mecanismo de segurança principal para mensagens do Barramento de Serviço. Este artigo discute as SAS, como elas funcionam e como usá-las de maneira independente da plataforma.

A autenticação por SAS permite que os aplicativos se autentiquem no Barramento de Serviço usando uma chave de acesso configurada no namespace ou na entidade do sistema de mensagens (fila ou tópico) à qual estão associados direitos específicos. Você poderá então usar essa chave para gerar um token SAS que os clientes poderão usar para se autenticarem no Barramento de Serviço.

O suporte à autenticação SAS está incluído no SDK do Azure versão 2.0 e posteriores.

## <a name="overview-of-sas"></a>Visão geral das SAS

Assinaturas de acesso compartilhado são um mecanismo de autenticação com base em hashes seguros SHA-256 ou URIs. As SAS são um mecanismo extremamente poderoso usado por todos os serviços do Barramento de Serviço. Na utilização real, as SAS têm dois componentes: uma *política de acesso compartilhado* e uma *Assinatura de Acesso Compartilhado* (normalmente chamada de *token*).

A autenticação SAS no Barramento de Serviço envolve a configuração de uma chave criptográfica com direitos associados em um recurso do Barramento de Serviço. Os clientes solicitam acesso aos recursos do Barramento de Serviço ao apresentarem um token SAS. Esse token consiste no URI do recurso sendo acessado e uma expiração assinada com a chave  configurada.

É possível configurar regras de autorização de Assinatura de Acesso Compartilhado nas [retransmissões](service-bus-fundamentals-hybrid-solutions.md#relays), [filas](service-bus-fundamentals-hybrid-solutions.md#queues) e [tópicos](service-bus-fundamentals-hybrid-solutions.md#topics) do Barramento de Serviço.

A autenticação SAS utiliza os seguintes elementos:

* [Regra de autorização de Acesso Compartilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule): uma chave criptográfica primária de 256 bits na representação Base64, uma chave secundária opcional e um nome de chave e os direitos associados (uma coleção de direitos *Listen*, *Send* ou *Manage*).
* [Assinatura de Acesso Compartilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) : gerado usando o HMAC-SHA256 de uma cadeia de caracteres de recurso, que consiste no URI do recurso acessado e em uma expiração, com a chave de criptografia. A assinatura e outros elementos descritos nas seções a seguir são formatados em uma cadeia de caracteres para formar o token SAS.

## <a name="shared-access-policy"></a>Política de acesso compartilhado

Uma coisa importante para entender sobre a SAS é que ela começa com uma política. Para cada política, você toma decisões quanto a três informações: **nome**, **escopo** e **permissões**. O **nome** é apenas isso, um nome exclusivo dentro do escopo. O escopo é bastante simples: é o URI do recurso em questão. Para um namespace do Barramento de Serviço, o escopo é o FQDN (nome de domínio totalmente qualificado), como `https://<yournamespace>.servicebus.windows.net/`.

As permissões disponíveis para uma política são bastante autoexplicativas:

* Enviar
* Escutar
* Gerenciar

Depois de você criar a política, ela recebe uma *Chave primária* e uma *Chave secundária*. Essas chaves são criptograficamente fortes. Não as perca ou divulgue - elas sempre estarão disponíveis no [portal do Azure][Azure portal]. Você pode usar qualquer uma das chaves geradas e pode gerá-las novamente a qualquer momento. No entanto, se você gera novamente ou altera a chave primária da política, quaisquer Assinaturas de acesso compartilhado criadas por meio dela serão invalidadas.

Quando você cria um namespace do Barramento de Serviço, é criada automaticamente uma política para todo o namespace chamada **RootManageSharedAccessKey**, e essa política tem todas as permissões. Você não faz logon como **raiz**. É possível criar políticas adicionais na guia **Configurar** para o namespace no Portal. É importante observar que um único nível de árvore no Barramento de Serviço (namespace, fila etc.) só pode ter até 12 políticas anexadas a ele.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração da autenticação de Assinatura de Acesso Compartilhado
Você pode configurar a regra [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em namespaces, filas ou tópicos do Barramento de Serviço. A configuração de um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em uma assinatura do Barramento de Serviço não tem suporte no momento, mas você pode usar regras configuradas em um namespace ou em um tópico para proteger o acesso a assinaturas. Para obter um exemplo funcional que ilustre este procedimento, confira o exemplo [Usando a autenticação de Assinatura de Acesso Compartilhado (SAS) com assinaturas do Barramento de Serviço](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

Você pode configurar até 12 dessas regras em um namespace, fila ou tópico do Barramento de Serviço. As regras configuradas em um namespace do Barramento de Serviço se aplicam a todas as entidades nesse namespace.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Nessa figura, as regras de autorização *manageRuleNS*, *sendRuleNS* e *listenRuleNS* se aplicam à fila Q1 e ao tópico T1, enquanto *listenRuleQ* e *sendRuleQ* se aplicam somente à fila Q1 e *sendRuleT* se aplica apenas ao tópico T1.

Os parâmetros de chave de uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) são os seguintes:

| Parâmetro | Descrição |
| --- | --- |
| *KeyName* |Uma cadeia de caracteres que descreve a regra de autorização. |
| *PrimaryKey* |Uma chave primária de 256 bits codificada em base64 para assinatura e validação do token SAS. |
| *SecondaryKey* |Uma chave secundária de 256 bits codificada em base64 para assinatura e validação do token SAS. |
| *AccessRights* |Uma lista de direitos de acesso concedidos pela regra de autorização. Esses direitos podem ser qualquer coleção de direitos Escutar, Enviar e Gerenciar. |

Quando um namespace do Barramento de Serviço é provisionado, uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) com [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) definido como **RootManageSharedAccessKey** é criada por padrão.

## <a name="generate-a-shared-access-signature-token"></a>Gerar uma Assinatura de Acesso Compartilhado (token)

A política em si não é o token de acesso para o Barramento de Serviço. Ela é o objeto por meio do qual o token de acesso é gerado - usando a chave primária ou secundária. Qualquer cliente que tenha acesso às chaves de assinatura especificadas na regra de autorização de acesso compartilhado poderá gerar o token SAS. O token é gerado ao criar cuidadosamente uma cadeia de caracteres no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Em que `signature-string` é o hash SHA-256 do escopo do token (**escopo** conforme descrito na seção anterior) com um CRLF anexado e uma hora de validade (em segundos desde a época: `00:00:00 UTC` em 1º de janeiro de 1970). 

> [!NOTE]
> Para evitar um tempo de expiração curto de token, é recomendável que você codificar o valor de tempo de expiração como pelo menos um inteiro não assinado de 32 bits ou, preferencialmente, um inteiro longo (64 bits).  
> 
> 

O hash é semelhante ao seguinte pseudocódigo e retorna 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Os valores não hash estão na cadeia de caracteres **SharedAccessSignature** para que o destinatário possa calcular o hash com os mesmos parâmetros para garantir que o mesmo resultado seja retornado. O URI especifica o escopo e o nome da chave identifica a política a ser usada para computar o hash. Isso é importante de um ponto de vista de segurança. Se a assinatura não coincidir com aquela que o destinatário (Barramento de Serviço) calcula, o acesso é negado. Nesse ponto, você pode ter certeza de que o remetente tinha acesso à chave e de que deve ter os direitos especificados na política.

Observe que você deve usar o URI do recurso codificado para esta operação. O URI do recurso é o URI completo do recurso do Barramento de Serviço ao qual o acesso é solicitado. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>`; ou seja, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

A regra de autorização de acesso compartilhado usada para assinar deve ser configurada na entidade especificada por esse URI ou por um de seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos sob o `<resourceURI>` usado na `signature-string`.

O [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) no token SAS refere-se ao **keyName** da regra de autorização de acesso compartilhado usada para gerar o token.

O *URL-encoded-resourceURI* deve ser igual ao URI usado na cadeia de caracteres a assinar durante o cálculo da assinatura. Ele deve ser [codificado por percentual](https://msdn.microsoft.com/library/4fkewx0t.aspx).

É recomendável que você regenere periodicamente as chaves usadas no objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Geralmente, os aplicativos devem usar a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) para gerar um token SAS. Ao regenerar as chaves, você deverá substituir a [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) pela chave primária antiga e gerar uma nova chave como a nova chave primária. Isso permite que você continue usando tokens para autorização que tenham sido emitidos com a antiga chave primária e que ainda não tenham expirado.

Se uma chave estiver comprometida e se você precisar revogar as chaves, será possível gerar novamente a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) e a [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) de uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), substituindo-as por novas chaves. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Como usar a autenticação de Assinatura de Acesso Compartilhado com Barramento de Serviço

Os cenários a seguir incluem a configuração de regras de autorização, a geração de tokens SAS e a autorização de cliente.

Para um exemplo funcional completo de um aplicativo do Barramento de Serviço que ilustre a configuração e use a autorização SAS, confira [Autenticação de Assinatura de Acesso Compartilhado com o Barramento de Serviço](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Há um exemplo relacionado que ilustra o uso de regras de autorização SAS configuradas em namespaces ou em tópicos para proteger assinaturas do Barramento de Serviço disponível aqui: [Usando a autenticação de Assinatura de Acesso Compartilhado (SAS) com as assinaturas do Barramento de Serviço](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Acessar regras de autorização de Acesso Compartilhado em um namespace

As operações na raiz do namespace do Barramento de Serviço exigem a autenticação de certificado. Você deve carregar um certificado de gerenciamento da sua assinatura do Azure. Para carregar um certificado de gerenciamento, siga as etapas [aqui](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate), usando o [portal do Azure][Azure portal]. Para saber mais sobre certificados de gerenciamento do Azure, confira [Visão geral sobre certificados do Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

O ponto de extremidade para acessar regras de autorização de acesso compartilhado em um namespace do Barramento de Serviço é o seguinte:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Para criar um objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em um namespace do Barramento de Serviço, execute uma operação POST nesse ponto de extremidade com as informações da regra serializadas como JSON ou XML. Por exemplo:

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Da mesma forma, use uma operação GET no ponto de extremidade para ler as regras de autorização configuradas no namespace.

Para atualizar ou excluir uma regra de autorização específica, use o ponto de extremidade a seguir:

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Acessar regras de Autorização de Acesso Compartilhado em uma entidade

É possível acessar um objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado em uma fila ou tópico do Barramento de Serviço por meio da coleção [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) na [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou na [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

O código a seguir mostra como adicionar regras de autorização para uma fila.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Usar a autorização de Assinatura de Acesso Compartilhado

Aplicativos usando o SDK do .NET do Azure com as bibliotecas .NET do Barramento de Serviço podem usar a autorização SAS por meio da classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . O código a seguir ilustra o uso do provedor do token para enviar mensagens para uma fila do Barramento de Serviço.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Os aplicativos também podem usar SAS para autenticação usando uma cadeia de conexão SAS em métodos que aceitem cadeias de conexão.

Observe que, para usar a autorização SAS com as retransmissões do Barramento de Serviço, você poderá utilizar as chaves SAS configuradas no namespace do Barramento de Serviço. Se você criar explicitamente uma retransmissão no namespace ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) com uma [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), será possível definir as regras SAS apenas para essa retransmissão. Para usar a autorização SAS com assinaturas do Barramento de Serviço, você poderá usar as chaves SAS configuradas em um namespace ou em um tópico do Barramento de Serviço.

## <a name="use-the-shared-access-signature-at-http-level"></a>Usar a Assinatura de Acesso Compartilhado (no nível do HTTP)

Agora que sabe como criar Assinaturas de acesso compartilhado para qualquer entidade no Barramento de Serviço, você está pronto para executar um HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Lembre-se de que isso funciona para tudo. Você pode criar SAS para uma fila, tópico ou assinatura. 

Se você fornecer a um remetente ou um cliente um token SAS, eles não têm a chave diretamente e não podem reverter o hash para obtê-la. Dessa forma, você tem controle sobre o que eles podem acessar e por quanto tempo. É importante se lembrar de que se você alterar a chave primária da política, quaisquer Assinaturas de acesso compartilhado criadas por meio dela serão invalidadas.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Usar a Assinatura de Acesso Compartilhado (no nível do AMQP)

Na seção anterior, você viu como usar o token SAS com uma solicitação HTTP POST para envio dos dados ao Barramento de Serviço. Como você sabe, é possível acessar o Barramento de Serviço usando o protocolo AMQP (Advanced Message Queuing Protocol), que é o protocolo preferencial por motivos de desempenho em muitos cenários. O uso de tokens SAS com AMQP é descrito no documento [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , em estado de rascunho funcional desde 2013, mas que conta com amplo suporte do Azure no momento.

Antes de começar a enviar dados ao Barramento de Serviço, o editor precisa enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido chamado **$cbs** (veja-o como uma fila "especial" usada pelo serviço para adquirir e validar todos os tokens SAS). O editor deve especificar o campo **ReplyTo** dentro da mensagem AMQP; esse é o nó em que o serviço responde ao editor com o resultado da validação do token (um padrão simples de solicitação/resposta entre o editor e o serviço). Esse nó de resposta é criado "dinamicamente", falando sobre "criação dinâmica de nó remoto", como descrito pela especificação do AMQP 1.0. Depois de verificar a validade do token SAS, o editor poderá começar a enviar dados ao serviço.

As etapas a seguir mostram como enviar o token SAS com o protocolo AMQP usando a biblioteca [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . Isso será útil se você não puder usar o SDK oficial do Barramento de Serviço (por exemplo, no WinRT, no .Net Compact Framework, no .Net Micro Framework e no Mono) ao desenvolver em C\#. Obviamente, essa biblioteca é útil para entender como funciona a segurança baseada em declarações no nível do AMQP, como você viu que funciona no nível HTTP (com uma solicitação HTTP POST e o token SAS enviados dentro do cabeçalho "Authorization"). Se não precisar desse conhecimento avançado sobre AMQP, você poderá usar o SDK oficial do Barramento de Serviço com aplicativos do .Net Framework, que farão exatamente isso para você.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

O método `PutCbsToken()` recebe a *conexão* (instância da classe de conexão AMQP, conforme fornecida pela [biblioteca AMQP .Net Lite](https://github.com/Azure/amqpnetlite)), que representa a conexão TCP com o serviço, e o parâmetro *sasToken*, o token SAS a ser enviado. 

> [!NOTE]
> É importante que a conexão seja criada com o **mecanismo de autenticação SASL definido como EXTERNAL** (e não o padrão PLAIN com nome de usuário e senha usados quando você não precisa enviar o token SAS).
> 
> 

Em seguida, o editor cria dois links AMQP para enviar o token SAS e receber a resposta (resultado da validação do token) do serviço.

A mensagem AMQP contém um conjunto de propriedades e mais informações do que uma mensagem simples. O token SAS é o corpo da mensagem (usando o construtor). A propriedade **"ReplyTo"** é definida como o nome do nó para receber o resultado da validação no link receptor (você pode alterar o nome dele se quiser e ele será criado dinamicamente pelo serviço). As três últimas propriedades application/custom são usadas pelo serviço para indicar o tipo de operação que ele deve executar. Conforme descrito pela especificação do rascunho CBS, elas devem ser o **nome da operação** ("put-token"), o **tipo de token** (nesse caso, um "servicebus.windows.net:sastoken") e o **"nome" do público-alvo** ao qual o token se aplica (toda a entidade).

Depois de enviar o token SAS pelo link do remetente, o editor deverá ler a resposta no link receptor. A resposta é uma mensagem AMQP simples com uma propriedade de aplicativo chamada **"código de status"** , que pode conter os mesmos valores que um código de status HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Direitos necessários para operações do Barramento de Serviço

A tabela a seguir mostra os direitos de acesso necessários para diversas operações em recursos do Barramento de Serviço.

| Operação | Declaração Obrigatória | Escopo da Declaração |
| --- | --- | --- |
| **Namespace** | | |
| Configurar regra de autorização em um namespace |Gerenciar |Qualquer endereço de namespace |
| **Registro do Serviço** | | |
| Enumerar Políticas de Privacidade |Gerenciar |Qualquer endereço de namespace |
| Iniciar a escuta em um namespace |Escutar |Qualquer endereço de namespace |
| Enviar mensagens a um ouvinte em um namespace |Enviar |Qualquer endereço de namespace |
| **Fila** | | |
| Criar uma fila |Gerenciar |Qualquer endereço de namespace |
| Excluir uma fila |Gerenciar |Qualquer endereço de fila válido |
| Enumerar filas |Gerenciar |/$Resources/Queues |
| Obter a descrição da fila |Gerenciar |Qualquer endereço de fila válido |
| Configurar regra de autorização para uma fila |Gerenciar |Qualquer endereço de fila válido |
| Enviar para a fila |Enviar |Qualquer endereço de fila válido |
| Receber mensagens de uma fila |Escutar |Qualquer endereço de fila válido |
| Abandonar ou concluir as mensagens após o recebimento da mensagem em modo de bloqueio de pico |Escutar |Qualquer endereço de fila válido |
| Adiar uma mensagem para recuperação posterior |Escutar |Qualquer endereço de fila válido |
| Colocar uma mensagem nas mensagens mortas |Escutar |Qualquer endereço de fila válido |
| Obter o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| Definir o estado associado a uma sessão de fila de mensagens |Escutar |Qualquer endereço de fila válido |
| **Tópico** | | |
| Criar um tópico |Gerenciar |Qualquer endereço de namespace |
| Excluir um tópico |Gerenciar |Qualquer endereço de tópico válido |
| Enumerar tópicos |Gerenciar |/$Resources/Topics |
| Obter a descrição do tópico |Gerenciar |Qualquer endereço de tópico válido |
| Configurar regra de autorização para um tópico |Gerenciar |Qualquer endereço de tópico válido |
| Enviar ao tópico |Enviar |Qualquer endereço de tópico válido |
| **Assinatura** | | |
| Criar uma assinatura |Gerenciar |Qualquer endereço de namespace |
| Excluir assinatura |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Enumerar assinaturas |Gerenciar |../myTopic/Subscriptions |
| Obter descrição da assinatura |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Abandonar ou concluir as mensagens após o recebimento da mensagem em modo de bloqueio de pico |Escutar |../myTopic/Subscriptions/mySubscription |
| Adiar uma mensagem para recuperação posterior |Escutar |../myTopic/Subscriptions/mySubscription |
| Colocar uma mensagem nas mensagens mortas |Escutar |../myTopic/Subscriptions/mySubscription |
| Obter o estado associado a uma sessão de tópico |Escutar |../myTopic/Subscriptions/mySubscription |
| Definir o estado associado a uma sessão de tópico |Escutar |../myTopic/Subscriptions/mySubscription |
| **Regras** | | |
| Criar uma regra |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Excluir uma regra |Gerenciar |../myTopic/Subscriptions/mySubscription |
| Enumerar regras |Gerenciar ou Escutar |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, confira os tópicos a seguir.

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Como usar filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com