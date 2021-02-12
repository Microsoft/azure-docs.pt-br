---
title: Configurações de upstream no serviço de Signaler do Azure
description: Obtenha uma introdução de configurações de upstream e protocolos de mensagens de upstream.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724046"
---
# <a name="upstream-settings"></a>Configurações de upstream

Upstream é um recurso de visualização que permite que o serviço de sinalizador do Azure envie mensagens e eventos de conexão para um conjunto de pontos de extremidade no modo sem servidor. Você pode usar upstream para invocar um método de Hub de clientes no modo sem servidor e deixar que os pontos de extremidade sejam notificados quando as conexões do cliente forem conectadas ou desconectadas.

> [!NOTE]
> Somente o modo sem servidor pode definir configurações de upstream.

## <a name="details-of-upstream-settings"></a>Detalhes das configurações de upstream

As configurações de upstream consistem em uma lista de itens com diferenciação de ordem. Cada item consiste em:

* Um modelo de URL, que especifica para onde as mensagens são enviadas.
* Um conjunto de regras.
* Configurações de autenticação. 

Quando o evento especificado ocorre, as regras de um item são verificadas uma a uma na ordem. As mensagens serão enviadas para a URL de upstream do primeiro item correspondente.

### <a name="url-template-settings"></a>Configurações de modelo de URL

Você pode parametrizar a URL para dar suporte a vários padrões. Há três parâmetros predefinidos:

|Parâmetro predefinido|Descrição|
|---------|---------|
|82801ER| Um hub é um conceito do serviço de Signaler do Azure. Um hub é uma unidade de isolamento. O escopo de usuários e a entrega de mensagens é restrito a um Hub.|
|Categorias| Uma categoria pode ser um dos seguintes valores: <ul><li>**conexões**: eventos de tempo de vida da conexão. Ele é acionado quando uma conexão de cliente é conectada ou desconectada. Ele inclui eventos conectados e desconectados.</li><li>**mensagens**: acionadas quando os clientes invocam um método de Hub. Ele inclui todos os outros eventos, exceto aqueles na categoria **conexões** .</li></ul>|
|circunstância| Para a categoria **mensagens** , um evento é o destino em uma [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) que os clientes enviam. Para a categoria **conexões** , somente *conectada* e *desconectada* são usadas.|

Esses parâmetros predefinidos podem ser usados no padrão de URL. Os parâmetros serão substituídos por um valor especificado quando você estiver avaliando a URL de upstream. Por exemplo: 
```
http://host.com/{hub}/api/{category}/{event}
```
Quando uma conexão de cliente no Hub "chat" estiver conectada, uma mensagem será enviada para esta URL:
```
http://host.com/chat/api/connections/connected
```
Quando um cliente no Hub "chat" invoca o método de Hub `broadcast` , uma mensagem será enviada para esta URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>Key Vault referência de segredo nas configurações de modelo de URL

A URL de upstream não é criptografia em repouso. Se você tiver informações confidenciais, é recomendável usar Key Vault para salvá-las onde o controle de acesso tem melhor seguro. Basicamente, você pode habilitar a identidade gerenciada do serviço do Azure Signalr e, em seguida, conceder permissão de leitura em uma instância de Key Vault e usar Key Vault referência em vez de texto não criptografado no padrão de URL upstream.

1. Adicione uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário. Veja [como adicionar identidade gerenciada no portal do Azure](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Conceda permissão de leitura de segredo para a identidade gerenciada nas políticas de acesso no Key Vault. Consulte [atribuir uma política de acesso de Key Vault usando o portal do Azure](../key-vault/general/assign-access-policy-portal.md)

3. Substitua o texto confidencial pela sintaxe `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` no padrão de URL upstream.

> [!NOTE]
> O conteúdo secreto só é relido quando você altera as configurações de upstream ou altera a identidade gerenciada. Verifique se você concedeu permissão de leitura de segredo para a identidade gerenciada antes de usar a referência de segredo Key Vault.

### <a name="rule-settings"></a>Configurações de regra

Você pode definir regras para *regras de Hub*, *regras de categoria* e *regras de evento* separadamente. A regra de correspondência oferece suporte a três formatos. Tome as regras de evento como exemplo:
- Use um asterisco (*) para corresponder a qualquer evento.
- Use uma vírgula (,) para unir vários eventos. Por exemplo, `connected, disconnected` corresponde aos eventos conectados e desconectados.
- Use o nome do evento completo para corresponder ao evento. Por exemplo, `connected` corresponde ao evento conectado.

> [!NOTE]
> Se você estiver usando Azure Functions e o [disparador de sinalização](../azure-functions/functions-bindings-signalr-service-trigger.md), o gatilho do signalr irá expor um único ponto de extremidade no seguinte formato: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` .
> Você pode apenas definir **as configurações de modelo de URL** para essa URL e manter **as configurações de regra** padrão. Consulte [integração do serviço signalr](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) para obter detalhes sobre como localizar `<Function_App_URL>` e `<API_KEY>` .

### <a name="authentication-settings"></a>Configurações de autenticação

Você pode configurar a autenticação para cada item de configuração upstream separadamente. Quando você configura a autenticação, um token é definido no `Authentication` cabeçalho da mensagem de upstream. Atualmente, o serviço de Signaler do Azure dá suporte aos seguintes tipos de autenticação:
- `None`
- `ManagedIdentity`

Ao selecionar `ManagedIdentity` , você deve habilitar uma identidade gerenciada no serviço de signaler do Azure antecipadamente e, opcionalmente, especificar um recurso. Consulte [identidades gerenciadas para o serviço de signaler do Azure](howto-use-managed-identity.md) para obter detalhes.

## <a name="create-upstream-settings-via-the-azure-portal"></a>Criar configurações de upstream por meio do portal do Azure

1. Vá para o serviço do Azure Signalr.
2. Selecione **configurações** e alternar **modo de serviço** para sem **servidor**. As configurações de upstream serão exibidas:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Configurações de upstream":::

3. Adicione URLs sob o **padrão de URL upstream**. Em seguida, as configurações como **regras de Hub** mostrarão o valor padrão.
4. Para definir configurações para **regras de Hub**, **regras de evento**, **regras de categoria** e autenticação de **upstream**, selecione o valor de **regras de Hub**. É exibida uma página que permite que você edite as configurações:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Detalhes da configuração upstream":::

5. Para definir a **autenticação upstream**, verifique se você habilitou uma identidade gerenciada primeiro. Em seguida, selecione **usar identidade gerenciada**. De acordo com suas necessidades, você pode escolher qualquer opção em **ID do recurso de autenticação**. Consulte [identidades gerenciadas para o serviço de signaler do Azure](howto-use-managed-identity.md) para obter detalhes.

## <a name="create-upstream-settings-via-resource-manager-template"></a>Criar configurações de upstream por meio do modelo do Resource Manager

Para criar configurações de upstream usando um [modelo de Azure Resource Manager](../azure-resource-manager/templates/overview.md), defina a `upstream` Propriedade na `properties` propriedade. O trecho a seguir mostra como definir a `upstream` propriedade para criar e atualizar configurações de upstream.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Protocolos sem servidor

O serviço de sinalizador do Azure envia mensagens para pontos de extremidade que seguem os seguintes protocolos. Você pode usar a [Associação de gatilho de serviço de signalr](../azure-functions/functions-bindings-signalr-service-trigger.md) com aplicativo de funções, que manipula esses protocolos para você.

### <a name="method"></a>Método

POST

### <a name="request-header"></a>Cabeçalho da solicitação

|Nome |Descrição|
|---------|---------|
|X-ASRS-Connection-ID |A ID de conexão para a conexão do cliente.|
|X-ASRS-Hub |O Hub ao qual a conexão do cliente pertence.|
|X-ASRS-categoria |A categoria à qual a mensagem pertence.|
|X-ASRS-evento |O evento ao qual a mensagem pertence.|
|X-ASRS-Signature |Um HMAC (código de autenticação de mensagem baseado em hash) que é usado para validação. Consulte [assinatura](#signature) para obter detalhes.|
|X-ASRS-User-Claims |Um grupo de declarações da conexão do cliente.|
|X-ASRS-User-ID |A identidade do usuário do cliente que envia a mensagem.|
|X-ASRS-cliente-consulta |A consulta da solicitação quando os clientes se conectam ao serviço.|
|Autenticação |Um token opcional quando você estiver usando `ManagedIdentity` . |

### <a name="request-body"></a>Corpo da solicitação

#### <a name="connected"></a>Conectado

Content-Type: application/json

#### <a name="disconnected"></a>Desconectado

Tipo de conteúdo: `application/json`

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|Erro |string |A mensagem de erro de uma conexão fechada. Vazio quando as conexões fecham sem erros.|

#### <a name="invocation-message"></a>Mensagem de invocação

Tipo de conteúdo: `application/json` ou `application/x-msgpack`

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|InvocationId |string | Uma cadeia de caracteres opcional que representa uma mensagem de invocação. Encontre detalhes em [invocações](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Destino |string | O mesmo que o evento e o mesmo que o destino em uma [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argumentos |Matriz de objeto |Uma matriz que contém argumentos a serem aplicados ao método referido em `Target` . |

### <a name="signature"></a>Assinatura

O serviço irá calcular o código SHA256 para o `X-ASRS-Connection-Id` valor usando a chave de acesso primária e a chave de acesso secundária como a `HMAC` chave. O serviço irá defini-lo no `X-ASRS-Signature` cabeçalho ao fazer solicitações HTTP para upstream:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para o serviço de Signaler do Azure](howto-use-managed-identity.md)
- [Desenvolvimento de funções do Azure e a configuração com o serviço do Azure SignalR](signalr-concept-serverless-development-config.md)
- [Manipular mensagens do serviço de sinalização (Associação de gatilho)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [Exemplo de associação de gatilho de serviço signalr](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)