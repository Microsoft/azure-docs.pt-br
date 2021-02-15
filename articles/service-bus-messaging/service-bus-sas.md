---
title: Controle de acesso do barramento de serviço do Azure com assinaturas de acesso compartilhado
description: Visão geral da controle de acesso do Barramento de Serviço usando a visão geral de Assinaturas de Acesso Compartilhado, detalhes sobre a autenticação SAS com o Barramento de Serviço do Azure.
ms.topic: article
ms.date: 01/19/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bdc167c437a79d609db25a2e3c48b71e0a748b2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598827"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Controle de acesso do Barramento de Serviço com Assinaturas de Acesso Compartilhado

Este artigo discute as *assinaturas de acesso compartilhado* (SAS), como elas funcionam e como usá-las de forma independente da plataforma.

SAS protege o acesso ao Barramento de Serviço com base nas regras de autorização. Eles são configurados em um namespace ou em uma entidade de mensagens (fila ou tópico). Uma regra de autorização tem nome, está associada a direitos específicos e executa um par de chaves de criptografia. Use o nome e a chave da regra por meio do SDK do Barramento de Serviço ou em seu próprio código para gerar um token SAS. Um cliente pode passar o token para o Barramento de Serviço para comprovar a autorização para a operação solicitada.

> [!NOTE]
> O barramento de serviço do Azure dá suporte à autorização de acesso a um namespace do barramento de serviço e suas entidades usando o Azure Active Directory (AD do Azure). A autorização de usuários ou aplicativos usando o token 2,0 do OAuth retornado pelo Azure AD fornece segurança superior e facilidade de uso sobre SAS (assinaturas de acesso compartilhado). Com o Azure AD, não é necessário armazenar os tokens no código e arriscar as vulnerabilidades de segurança potenciais.
>
> A Microsoft recomenda usar o Azure AD com seus aplicativos do barramento de serviço do Azure quando possível. Para obter mais informações, consulte os seguintes artigos:
> - [Autentique e autorize um aplicativo com Azure Active Directory para acessar entidades do barramento de serviço do Azure](authenticate-application.md).
> - [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos do barramento de serviço do Azure](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Visão geral das SAS

Assinaturas de Acesso Compartilhado são um mecanismo de autorização baseada em declarações usando tokens simples. Usando SAS, chaves nunca são passadas na conexão. As chaves são usadas para assinar criptograficamente informações que posteriormente podem ser verificadas pelo serviço. A SAS pode ser usada como esquema de nome de usuário e senha no qual o cliente está em posse imediata de um nome de regra de autorização e uma chave correspondente. A SAS também pode ser usada como modelo de segurança federada, no qual o cliente recebe um token de acesso de tempo limitado e assinado de um serviço de token de segurança sem nunca ter posse da chave de assinatura.

A autenticação de SAS no Barramento de Serviço está configurada com [Regras de Autorização de Acesso Compartilhado](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) designadas com direitos de acesso associados e um par de chaves de criptografia primário e secundário. As chaves são valores de 256 bits na representação Base64. Você pode configurar regras no nível de namespace, em [retransmissões](../azure-relay/relay-what-is-it.md) de Barramento de Serviço, [filas](service-bus-messaging-overview.md#queues) e [tópicos](service-bus-messaging-overview.md#topics).

O token de [Assinatura de Acesso Compartilhado](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) contém o nome da regra de autorização escolhida, o URI do recurso que deve ser acessado, uma expiração instantânea e uma assinatura criptográfica HMAC-SHA256 calculada com esses campos usando a chave de criptografia primária ou secundária da regra de autorização escolhida.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso compartilhado

Cada namespace do Barramento de Serviço e cada entidade do Barramento de Serviço tem uma política de Autorização de Acesso Compartilhado composta de regras. A política no nível de namespace se aplica a todas as entidades dentro do namespace, independentemente de suas configurações de política individuais.

Para cada regra de política de autorização, você toma decisões quanto a três informações: **nome**, **escopo** e **direitos**. O **nome** é apenas isso, um nome exclusivo dentro do escopo. O escopo é bastante simples: é o URI do recurso em questão. Para um namespace do Barramento de Serviço, o escopo é o FQDN (nome de domínio totalmente qualificado), como `https://<yournamespace>.servicebus.windows.net/`.

Os direitos concedidos pela regra de política podem ser uma combinação de:

* 'Enviar' - confere o direito de enviar mensagens para a entidade
* ' Escutar '-confere o direito de recebimento (fila, assinaturas) e todos os tratamentos de mensagens relacionados
* 'Gerenciar' - confere o direito de gerenciar a topologia do namespace, incluindo a criação e a exclusão de entidades

O direito de 'Gerenciar' inclui os direitos de 'Enviar' e 'Receber'.

Uma política de namespace ou entidade pode armazenar até 12 regras de Autorização de Acesso Compartilhado, fornecendo espaço para três conjuntos de regras, cada um abrangendo os direitos básicos e a combinação de Enviar e Escutar. Esse limite ressalta que o armazenamento da política de SAS não é pretendida como armazenamento de conta de usuário ou serviço. Se seu aplicativo precisa conceder acesso ao Barramento de Serviço com base no usuário ou em identidades de serviço, ele deve implementar um serviço de token de segurança que emite tokens SAS após uma verificação de acesso e autenticação.

Uma regra de autorização recebe uma *Chave primária* e uma *Chave secundária*. Essas chaves são criptograficamente fortes. Não as perca ou divulgue - elas sempre estarão disponíveis no [portal do Azure][Azure portal]. Você pode usar qualquer uma das chaves geradas e pode gerá-las novamente a qualquer momento. Se você gerar novamente ou alterar uma chave na política, todos os tokens emitidos anteriormente com base na chave tornam-se inválidos instantaneamente. No entanto, conexões contínuas criadas com base em tais tokens continuarão funcionando até o token expirar.

Quando você cria um namespace do Barramento de Serviço, é criada automaticamente uma regra de política chamada **RootManageSharedAccessKey** para o namespace. Essa política tem permissões de Gerenciar para todo o namespace. É recomendável que você trate essa regra como conta de **raiz** administrativa e não use-a em seu aplicativo. Você pode criar regras de política adicionais na guia **Configurar** para o namespace no portal, por meio do PowerShell ou CLI do Azure.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS
Ao usar assinaturas de acesso compartilhado nos seus aplicativos, você precisará estar ciente de dois riscos possíveis:

- Se uma SAS for vazada, ela poderá ser usada por qualquer pessoa que a obtenha, o que pode comprometer os recursos do barramento de serviço.
- Se uma SAS fornecida a um aplicativo cliente expirar e o aplicativo não puder recuperar uma nova SAS do seu serviço, a funcionalidade do aplicativo poderá ser prejudicada.

As recomendações a seguir para usar assinaturas de acesso compartilhado podem ajudar a atenuar esses riscos:

- **Fazer com que os clientes renovem automaticamente a SAS, se necessário**: os clientes devem renovar a SAS bem antes da expiração, para permitir tempo para novas tentativas se o serviço que fornece a SAS não estiver disponível. Se a sua SAS for destinada a ser usada para um pequeno número de operações imediatas e de curta duração que devem ser concluídas dentro do período de expiração, pode ser desnecessária, pois a SAS não deve ser renovada. No entanto, se você tiver um cliente que está sempre fazendo solicitações via SAS, surge a possibilidade de expiração. A principal consideração é equilibrar a necessidade de que a SAS seja de curta duração (como mencionado anteriormente) com a necessidade de garantir que o cliente esteja solicitando a renovação cedo o suficiente (para evitar a interrupção devido à expiração da SAS antes de uma renovação bem-sucedida).
- **Tenha cuidado com a hora de início da SAS**: se você definir a hora de início de SAS como **agora**, devido à distorção do relógio (diferenças na hora atual de acordo com computadores diferentes), as falhas poderão ser observadas intermitentemente pelos primeiros minutos. Em geral, defina a hora de início para pelo menos 15 minutos no passado. Ou então, não o defina, o que o tornará válido imediatamente em todos os casos. O mesmo geralmente se aplica ao tempo de expiração também. Lembre-se de que você pode fazer um observador de até 15 minutos de distorção de relógio em qualquer direção em qualquer solicitação. 
- **Seja específico com o recurso a ser acessado**: uma prática recomendada de segurança é fornecer ao usuário os privilégios mínimos necessários. Se um usuário precisar apenas de acesso de leitura a uma única entidade, conceda-lhe acesso de leitura a essa única entidade, e não acesso de leitura/gravação/exclusão a todas as entidades. Também ajudará a diminuir o dano se uma SAS for comprometida porque a SAS tem menos poder nas mãos de um invasor.
- **Nem sempre use SAS**: às vezes, os riscos associados a uma operação específica em relação aos seus hubs de eventos superam os benefícios da SAS. Para essas operações, crie um serviço de camada intermediária que grave em seus hubs de eventos após a validação, a autenticação e a auditoria da regra de negócio.
- **Sempre usar HTTPS**: sempre use HTTPS para criar ou distribuir uma SAS. Se uma SAS for passada por HTTP e interceptada, um invasor executando um anexo Man-in-the-Middle será capaz de ler a SAS e, em seguida, usá-la da mesma forma que o usuário pretendido pode ter, potencialmente comprometendo dados confidenciais ou permitindo corrupção de dados pelo usuário mal-intencionado.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuração da autenticação de Assinatura de Acesso Compartilhado

Você pode configurar a regra [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em namespaces, filas ou tópicos do Barramento de Serviço. A configuração de um [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) em uma assinatura do Barramento de Serviço não tem suporte no momento, mas você pode usar regras configuradas em um namespace ou em um tópico para proteger o acesso a assinaturas. Para obter um exemplo funcional que ilustre este procedimento, confira o exemplo [Usando a autenticação de Assinatura de Acesso Compartilhado (SAS) com assinaturas do Barramento de Serviço](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Nessa figura, as regras de autorização *manageRuleNS*, *sendRuleNS* e *listenRuleNS* se aplicam à fila Q1 e ao tópico T1, enquanto *listenRuleQ* e *sendRuleQ* se aplicam somente à fila Q1 e *sendRuleT* se aplica apenas ao tópico T1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de Assinatura de Acesso Compartilhado

Qualquer cliente que tenha acesso ao nome de uma regra de autorização e a uma de suas chaves de assinatura pode gerar um token SAS. O token é gerado ao criar uma cadeia de caracteres no seguinte formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

- `se` - Instante de expiração do token. Número inteiro que reflete os segundos desde a época `00:00:00 UTC` em 1 de janeiro de 1970 (época UNIX) quando o token expira.
- `skn` - Nome da regra de autorização.
- `sr` -URI codificado de URL do recurso que está sendo acessado.
- `sig` -Assinatura HMACSHA256 codificada para URL. O cálculo de hash é semelhante ao seguinte pseudocódigo e retorna Base64 de saída binária bruta.

    ```
    urlencode(base64(hmacsha256(urlencode('https://<yournamespace>.servicebus.windows.net/') + "\n" + '<expiry instant>', '<signing key>')))
    ```

Aqui está um exemplo de código C# para gerar um token SAS:

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

> [!IMPORTANT]
> Para obter exemplos de como gerar um token SAS usando linguagens de programação diferentes, consulte [gerar token SAS](/rest/api/eventhub/generate-sas-token). 


O token contém os valores não hash para que o destinatário possa recalcular o hash com os mesmos parâmetros, verificando se o emissor possui uma chave de assinatura válida.

O URI do recurso é o URI completo do recurso do Barramento de Serviço ao qual o acesso é solicitado. Por exemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>`; ou seja, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. 

**O URI deve ser [codificado por percentual](/dotnet/api/system.web.httputility.urlencode).**

A regra de autorização de acesso compartilhado usada para assinar deve ser configurada na entidade especificada por esse URI ou por um de seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos prefixados com o `<resourceURI>` usado na `signature-string`.


## <a name="regenerating-keys"></a>Regenerando chaves

É recomendável que você regenere periodicamente as chaves usadas no objeto [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Os encaixes de chaves primárias e secundárias existem para que você possa usar as chaves gradualmente. Se seu aplicativo geralmente usa a chave primária, você pode copiar a chave primária para o encaixe de chave secundária e somente então regenerar a chave primária. O novo valor de chave primária pode então ser configurado para os aplicativos de cliente, que têm acesso contínuo usando a antiga chave primária no encaixe secundário. Depois que todos os clientes são atualizados, você pode regenerar a chave secundária para desativar finalmente a antiga chave primária.

Se você souber ou suspeitar de uma chave comprometida e se você precisar revogar as chaves, será possível gerar novamente a [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) e a [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) de uma [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), substituindo-as por novas chaves. Este procedimento invalida todos os tokens assinados com as chaves antigas.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Autenticação de Assinatura de Acesso Compartilhado com Barramento de Serviço

Os cenários descritos a seguir incluem a configuração de regras de autorização, a geração de tokens SAS e a autorização de cliente.

Para um exemplo funcional completo de um aplicativo do Barramento de Serviço que ilustre a configuração e use a autorização SAS, confira [Autenticação de Assinatura de Acesso Compartilhado com o Barramento de Serviço](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Há um exemplo relacionado que ilustra o uso de regras de autorização SAS configuradas em namespaces ou em tópicos para proteger assinaturas do Barramento de Serviço disponível aqui: [Usando a autenticação de Assinatura de Acesso Compartilhado (SAS) com as assinaturas do Barramento de Serviço](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Acessar regras de Autorização de Acesso Compartilhado em uma entidade

Com o .NET Framework do Barramento de Serviço, é possível acessar um objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configurado em uma fila ou tópico do Barramento de Serviço por meio da coleção [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) na [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou na [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

Aplicativos usando o SDK do .NET do Azure com as bibliotecas .NET do Barramento de Serviço podem usar a autorização SAS por meio da classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . O código a seguir ilustra o uso do provedor do token para enviar mensagens para uma fila do Barramento de Serviço. Como alternativa ao uso mostrado aqui, você também pode passar um token emitido anteriormente para o método de fábrica do provedor de token.

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

Você também pode usar o provedor de token diretamente para emitir tokens para passar para outros clientes.

Cadeias de conexão podem incluir um nome de regra (*SharedAccessKeyName*) e uma chave de regra (*SharedAccessKey*) ou um token emitido anteriormente (*SharedAccessSignature*). Quando estiverem presentes na cadeia de conexão passada para qualquer construtor ou método de fábrica que aceite uma cadeia de conexão, o provedor de token SAS é automaticamente criado e preenchido.

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

Na seção anterior, você viu como usar o token SAS com uma solicitação HTTP POST para envio dos dados ao Barramento de Serviço. Como você sabe, é possível acessar o Barramento de Serviço usando o protocolo AMQP (Advanced Message Queuing Protocol), que é o protocolo preferencial por motivos de desempenho em muitos cenários. O uso do token SAS com AMQP é descrito no documento [AMQP Claim-Based versão de segurança 1,0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) que está em rascunho em funcionamento desde 2013, mas tem suporte do Azure hoje.

Antes de começar a enviar dados ao Barramento de Serviço, o editor precisa enviar o token SAS dentro de uma mensagem AMQP para um nó AMQP bem definido chamado **$cbs** (veja-o como uma fila "especial" usada pelo serviço para adquirir e validar todos os tokens SAS). O editor deve especificar o campo **ReplyTo** dentro da mensagem AMQP; esse é o nó em que o serviço responde ao editor com o resultado da validação do token (um padrão simples de solicitação/resposta entre o editor e o serviço). Esse nó de resposta é criado "dinamicamente", falando sobre "criação dinâmica de nó remoto", como descrito pela especificação do AMQP 1.0. Depois de verificar a validade do token SAS, o editor poderá começar a enviar dados ao serviço.

As etapas a seguir mostram como enviar o token SAS com o protocolo AMQP usando a biblioteca [AMQP.net Lite](https://github.com/Azure/amqpnetlite) . Isso é útil se você não pode usar o SDK oficial do barramento de serviço (por exemplo, em WinRT, .NET Compact Framework, .NET micro Framework e mono) desenvolvendo em C \# . Obviamente, essa biblioteca é útil para entender como funciona a segurança baseada em declarações no nível do AMQP, como você viu que funciona no nível HTTP (com uma solicitação HTTP POST e o token SAS enviados dentro do cabeçalho "Authorization"). Se você não precisar desse conhecimento profundo sobre o AMQP, poderá usar o SDK oficial do barramento de serviço com .NET Framework aplicativos, o que fará isso para você.

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
> É importante que a conexão seja criada com o **mecanismo de autenticação SASL definido como ANONYMOUS** (e não o padrão PLAIN com nome de usuário e senha usados quando você não precisa enviar o token SAS).
>
>

Em seguida, o editor cria dois links AMQP para enviar o token SAS e receber a resposta (resultado da validação do token) do serviço.

A mensagem AMQP contém um conjunto de propriedades e mais informações do que uma mensagem simples. O token SAS é o corpo da mensagem (usando o construtor). A propriedade **"ReplyTo"** é definida como o nome do nó para receber o resultado da validação no link receptor (você pode alterar o nome dele se quiser e ele será criado dinamicamente pelo serviço). As três últimas propriedades application/custom são usadas pelo serviço para indicar o tipo de operação que ele deve executar. Conforme descrito pela especificação do rascunho CBS, elas devem ser o **nome da operação** ("put-token"), o **tipo de token** (nesse caso, um `servicebus.windows.net:sastoken`) e o **"nome" do público-alvo** ao qual o token se aplica (toda a entidade).

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
| Agende uma mensagem para entrega posterior; por exemplo, [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Escutar | Qualquer endereço de fila válido
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

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Como usar filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com