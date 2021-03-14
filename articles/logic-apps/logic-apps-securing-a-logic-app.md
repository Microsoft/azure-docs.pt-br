---
title: Proteger o acesso e os dados
description: Acesso seguro a entradas, saídas, gatilhos baseados em solicitação, histórico de execuções, tarefas de gerenciamento e acesso a outros recursos nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 7b082c226b38633d6c34ee2fe4d5227252b2bfcb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556376"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteger o acesso e os dados nos Aplicativos Lógicos do Azure

Os aplicativos lógicos do Azure dependem do [armazenamento do Azure](../storage/index.yml) para armazenar e [criptografar automaticamente os dados em repouso](../security/fundamentals/encryption-atrest.md). Essa criptografia protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Por padrão, o armazenamento do Azure usa chaves gerenciadas pela Microsoft para criptografar seus dados. Para obter mais informações, consulte [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

Para controlar ainda mais o acesso e proteger dados confidenciais em aplicativos lógicos do Azure, você pode configurar a segurança adicional nessas áreas:

* [Acesso para chamadas de entrada para gatilhos baseados em solicitação](#secure-inbound-requests)
* [Acesso a operações de aplicativo lógico](#secure-operations)
* [Acesso a entradas e saídas do histórico de execuções](#secure-run-history)
* [Acesso a entradas de parâmetro](#secure-action-parameters)
* [Acesso para chamadas de saída para outros serviços e sistemas](#secure-outbound-requests)
* [Bloquear a criação de conexões para conectores específicos](#block-connections)
* [Diretrizes de isolamento para aplicativos lógicos](#isolation-logic-apps)
* [Linha de base de segurança do Azure para aplicativos lógicos do Azure](../logic-apps/security-baseline.md)

Para obter mais informações sobre segurança no Azure, consulte estes tópicos:

* [Visão geral da criptografia do Azure](../security/fundamentals/encryption-overview.md)
* [Criptografia de dados em repouso no Azure](../security/fundamentals/encryption-atrest.md)
* [Azure Security Benchmark](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Acesso para chamadas de entrada para gatilhos baseados em solicitação

As chamadas de entrada que um aplicativo lógico recebe por meio de um gatilho baseado em solicitação, como o gatilho de [solicitação](../connectors/connectors-native-reqres.md) ou o gatilho de [webhook http](../connectors/connectors-native-webhook.md) , dão suporte à criptografia e são protegidas com o protocolo [TLS 1,2 no mínimo](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL). Os aplicativos lógicos aplicam essa versão ao receber uma chamada de entrada para o gatilho de solicitação ou um retorno de chamada para o gatilho ou ação de webhook HTTP. Se você obtiver erros de handshake de TLS, use o TLS 1.2. Para mais informações, consulte [Solucionar o problema do TLS 1.0](/security/solving-tls1-problem).

As chamadas de entrada dão suporte a esses conjuntos de codificação:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Aqui estão maneiras adicionais que você pode limitar o acesso a gatilhos que recebem chamadas de entrada para seu aplicativo lógico para que somente clientes autorizados possam chamar seu aplicativo lógico:

* [Gerar SAS (assinaturas de acesso compartilhado)](#sas)
* [Habilitar o OAuth do Azure AD (Open Authorization do Azure Active Directory)](#enable-oauth)
* [Expor seu aplicativo lógico com o gerenciamento de API do Azure](#azure-api-management)
* [Restringir endereços IP de entrada](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar SAS (assinaturas de acesso compartilhado)

Cada ponto de extremidade de solicitação em um aplicativo lógico inclui uma [SAS (assinatura de acesso compartilhado)](/rest/api/storageservices/constructing-a-service-sas) na URL do ponto de extremidade, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL contém o parâmetro de consulta `sp`, `sv` e `sig`, conforme descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica as permissões para os métodos HTTP permitidos a serem usados. |
| `sv` | Especifica a versão SAS a ser usada para gerar a assinatura. |
| `sig` | Especifica a assinatura a ser usada para autenticar o acesso ao gatilho. Essa assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos de URL e propriedades. Nunca exposta ou publicada, essa chave é mantida criptografada e armazenada com o aplicativo lógico. Seu aplicativo lógico autoriza somente gatilhos que contenham uma assinatura válida criada com a chave secreta. |
|||

Chamadas de entrada para um ponto de extremidade de solicitação podem usar apenas um esquema de autorização, SAS ou [Azure Active Directory autenticação aberta](#enable-oauth). Embora o uso de um esquema não desabilite o outro esquema, o uso de ambos os esquemas ao mesmo tempo causa um erro porque o serviço não sabe qual esquema deve ser escolhido.

Para mais informações sobre como proteger o acesso com SAS, confira estas seções neste tópico:

* [Regenerar chaves de acesso](#access-keys)
* [Criar URLs de retorno de chamada prestes a expirar](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para gerar uma nova chave de acesso de segurança a qualquer momento, use a API REST ou o portal do Azure. Todas as URLs geradas anteriormente que usam a chave antiga são invalidadas e não estão mais autorizadas a disparar o aplicativo lógico. As URLs que você recupera após a regeneração são assinadas com a nova chave de acesso.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico que tem a chave que você quer regenerar.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Chaves de Acesso**.

1. Selecione a chave que você quer regenerar e conclua o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de retorno de chamada prestes a expirar

Se você compartilhar a URL do ponto de extremidade para um gatilho baseado em solicitação com outras partes, poderá gerar URLs de retorno de chamada que usam chaves específicas e têm datas de validade. Dessa forma, você pode reverter tranquilamente as chaves ou restringir o acesso para disparar seu aplicativo lógico com base em um período específico. Para especificar uma data de validade para uma URL, use a API REST dos [Aplicativos Lógicos](/rest/api/logic/workflowtriggers), por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `NotAfter` usando uma cadeia de caracteres de data JSON. Essa propriedade retorna uma URL de retorno de chamada válida somente até a data e hora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Ao gerar ou listar URLs de retorno de chamada para um gatilho baseado em solicitação, você pode especificar a chave a ser usada para assinar a URL. Para gerar uma URL assinada por uma chave específica, use a [API REST dos Aplicativos Lógicos](/rest/api/logic/workflowtriggers), por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`. Essa propriedade retorna uma URL assinada pela chave de segurança especificada.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Habilitar o OAuth do Azure AD (Open Authorization do Azure Active Directory)

Para chamadas de entrada para um ponto de extremidade que é criado por um gatilho baseado em solicitação, você pode habilitar o [OAuth do Azure ad](../active-directory/develop/index.yml) definindo ou adicionando uma política de autorização para seu aplicativo lógico. Dessa forma, as chamadas de entrada usam [tokens de acesso](../active-directory/develop/access-tokens.md) OAuth para autorização.

Quando seu aplicativo lógico recebe uma solicitação de entrada que inclui um token de acesso OAuth, o serviço de aplicativos lógicos do Azure compara as declarações do token com as declarações especificadas por cada política de autorização. Se houver uma correspondência entre as declarações do token e todas as declarações em pelo menos uma política, a autorização terá sucesso na solicitação de entrada. O token pode ter mais declarações do que o número especificado pela política de autorização.

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>Considerações antes de habilitar o OAuth do Azure AD

* Uma chamada de entrada para o ponto de extremidade de solicitação pode usar apenas um esquema de autorização, o OAuth do Azure AD ou a [SAS (assinatura de acesso compartilhado)](#sas). Embora o uso de um esquema não desabilite o outro esquema, o uso de ambos os esquemas ao mesmo tempo causa um erro porque o serviço de aplicativos lógicos não sabe qual esquema deve ser escolhido.

* Somente esquemas de autorização de [tipo de portador](../active-directory/develop/active-directory-v2-protocols.md#tokens) têm suporte para tokens de acesso OAuth do Azure AD, o que significa que o `Authorization` cabeçalho do token de acesso deve especificar o `Bearer` tipo.

* Seu aplicativo lógico é limitado a um número máximo de políticas de autorização. Cada política de autorização também tem um número máximo de [declarações](../active-directory/develop/developer-glossary.md#claim). Para obter mais informações, confira [Limites e configuração para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Uma política de autorização deve incluir pelo menos a declaração do **emissor** , que tem um valor que começa com `https://sts.windows.net/` ou `https://login.microsoftonline.com/` (OAuth v2) como a ID do emissor do Azure AD.

  Por exemplo, suponha que seu aplicativo lógico tenha uma política de autorização que exija dois tipos de declaração, **público** e **emissor**. Esta [seção de conteúdo](../active-directory/develop/access-tokens.md#payload-claims) de exemplo para um token de acesso decodificado inclui os dois tipos de declaração em que `aud` é o valor **público** e `iss` é o valor do **emissor** :

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>Habilitar o OAuth do Azure AD para seu aplicativo lógico

Siga estas etapas para o portal do Azure ou seu modelo de Azure Resource Manager:

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://portal.azure.com), adicione uma ou mais políticas de autorização ao seu aplicativo lógico:

1. No [portal do Azure](https://portal.microsoft.com), encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Autorização**. Depois que o painel Autorização for aberto, selecione **Adicionar política**.

   ![Selecione "Autorização" > "Adicionar política"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Forneça informações sobre a política de autorização especificando os [tipos de declaração](../active-directory/develop/developer-glossary.md#claim) e os valores que seu aplicativo lógico espera no token de acesso apresentado por cada chamada de entrada para o gatilho de solicitação:

   ![Fornecer informações para a política de autorização](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome da política** | Sim | O nome que você deseja usar para a política de autorização |
   | **Declarações** | Sim | Os tipos de declaração e os valores que seu aplicativo lógico aceita de chamadas de entrada. O valor da declaração é limitado a um [número máximo de caracteres](logic-apps-limits-and-config.md#authentication-limits). Estes são os tipos de declaração disponíveis: <p><p>- **Emissor** <br>- **Público-alvo** <br>- **Assunto** <br>- **ID do JWT** (identificador de token da Web JSON) <p><p>No mínimo, a lista de **declarações** deve incluir a declaração do **emissor** , que tem um valor que começa com `https://sts.windows.net/` ou `https://login.microsoftonline.com/` como a ID do emissor do Azure AD. Para mais informações sobre esses tipos de declaração, confira [Declarações nos tokens de segurança do Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Você também pode especificar seu tipo e valor de declaração. |
   |||

1. Para adicionar outra declaração, selecione uma destas opções:

   * Para adicionar outro tipo de declaração, selecione **Adicionar declaração padrão**, selecione o tipo e especifique o valor.

   * Para adicionar sua própria declaração, selecione **Adicionar declaração personalizada**. Para obter mais informações, consulte [como fornecer declarações opcionais para seu aplicativo](../active-directory/develop/active-directory-optional-claims.md). Sua declaração personalizada é então armazenada como parte da ID do JWT; por exemplo, `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"` . 

1. Para adicionar outra política de autorização, selecione **Adicionar política**. Repita as etapas anteriores para configurar a política.

1. Quando terminar, selecione **Salvar**.

1. Para incluir o `Authorization` cabeçalho do token de acesso nas saídas do gatilho baseado em solicitação, consulte [incluir o cabeçalho ' Authorization ' nas saídas do gatilho de solicitação](#include-auth-header).


As propriedades do fluxo de trabalho, como as políticas, não aparecem no modo de exibição de código do aplicativo lógico na portal do Azure. Para acessar suas políticas programaticamente, chame a seguinte API por meio de Azure Resource Manager (ARM): `https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820` . Certifique-se de substituir os valores de espaço reservado para sua ID de assinatura do Azure, nome do grupo de recursos e nome do fluxo de trabalho.


<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[Modelo do Resource Manager](#tab/azure-resource-manager)

No modelo do ARM, defina uma política de autorização seguindo estas etapas e a sintaxe abaixo:

1. Na `properties` seção para a [definição de recurso do aplicativo lógico](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition), adicione um `accessControl` objeto, se não houver nenhum, que contenha um `triggers` objeto.

   Para obter mais informações sobre o `accessControl` objeto, consulte [restringir os intervalos de IP de entrada no modelo de Azure Resource Manager](#restrict-inbound-ip-template) e a referência de modelo de fluxos de [trabalho Microsoft. Logic](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. No `triggers` objeto, adicione um `openAuthenticationPolicies` objeto que contém o `policies` objeto em que você define uma ou mais políticas de autorização.

1. Forneça um nome para a política de autorização, defina o tipo de política como `AAD` e inclua uma `claims` matriz na qual você especifique um ou mais tipos de declaração.

   No mínimo, a `claims` matriz deve incluir o tipo de declaração do emissor em que você define a propriedade da Declaração `name` como `iss` e define o `value` para começar com `https://sts.windows.net/` ou `https://login.microsoftonline.com/` como a ID do emissor do Azure AD. Para mais informações sobre esses tipos de declaração, confira [Declarações nos tokens de segurança do Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Você também pode especificar seu tipo e valor de declaração.

1. Para incluir o `Authorization` cabeçalho do token de acesso nas saídas do gatilho baseado em solicitação, consulte [incluir o cabeçalho ' Authorization ' nas saídas do gatilho de solicitação](#include-auth-header).

Aqui está a sintaxe a seguir:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Incluir o cabeçalho ' Authorization ' nas saídas do gatilho de solicitação

Para aplicativos lógicos que [habilitam Azure Active Directory autenticação aberta (Azure ad OAuth)](#enable-oauth) para autorizar chamadas de entrada para acessar gatilhos baseados em solicitação, você pode habilitar o gatilho de solicitação ou saídas de gatilho de webhook http para incluir o `Authorization` cabeçalho do token de acesso OAuth. Na definição de JSON subjacente do gatilho, adicione e defina a `operationOptions` propriedade como `IncludeAuthorizationHeadersInOutputs` . Aqui está um exemplo para o gatilho de solicitação:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Para saber mais, consulte esses tópicos:

* [Referência de esquema para tipos de ação e gatilho – gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Referência de esquema para tipos de ação e gatilho-gatilho de webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Referência de esquema para tipos de ação e gatilho-opções de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Expor seu aplicativo lógico com o gerenciamento de API do Azure

Para adicionar mais [protocolos de autenticação](../active-directory/develop/authentication-vs-authorization.md) ao seu aplicativo lógico, considere usar o serviço de [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) . Esse serviço ajuda você a expor seu aplicativo lógico como uma API e oferece monitoramento, segurança, política e documentação avançados para qualquer ponto de extremidade. O Gerenciamento de API pode expor um ponto de extremidade público ou privado para seu aplicativo lógico. Para autorizar o acesso a esse ponto de extremidade, você pode usar o OAuth do Azure AD, o [certificado do cliente](#client-certificate-authentication)ou outros padrões de segurança para autorizar o acesso a esse ponto de extremidade. Quando o Gerenciamento de API recebe uma solicitação, o serviço envia a solicitação ao aplicativo lógico, fazendo também quaisquer transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas o gerenciamento de API chame seu aplicativo lógico, você pode [restringir os endereços IP de entrada do aplicativo lógico](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Restringir endereços IP de entrada

Junto à SAS (Assinatura de Acesso Compartilhado), você talvez queira limitar os clientes que podem acessar seu aplicativo lógico. Por exemplo, se você gerenciar seu ponto de extremidade de solicitação usando o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md), poderá restringir seu aplicativo lógico para aceitar solicitações somente do endereço IP da [instância do serviço de gerenciamento de API que você criar](../api-management/get-started-create-service-instance.md).

Independentemente de qualquer endereço IP que você especificar, você ainda pode executar um aplicativo lógico que tenha um gatilho baseado em solicitação usando a [API REST dos aplicativos lógicos: gatilhos de fluxo de trabalho – solicitação de execução](/rest/api/logic/workflowtriggers/run) ou usando o gerenciamento de API. Porém, esse cenário ainda requer [autenticação](../active-directory/develop/authentication-vs-authorization.md) em relação à API REST do Azure. Todos os eventos aparecem no Log de Auditoria do Azure. Verifique se você definiu as políticas de controle de acesso de forma adequada.

Para restringir os endereços IP de entrada para seu aplicativo lógico, siga estas etapas para o portal do Azure ou seu modelo de Azure Resource Manager:

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://portal.azure.com), esse filtro afeta os gatilhos *e* as ações, ao contrário da descrição no portal em **endereços IP de entrada permitidos**. Para configurar esse filtro separadamente para gatilhos e ações, use o `accessControl` objeto em um modelo de Azure Resource Manager para seu aplicativo lógico ou a [API REST de aplicativos lógicos: fluxo de trabalho-criar ou atualizar operação](/rest/api/logic/workflows/createorupdate).

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Na seção **configuração do controle de acesso** , em **endereços IP de entrada permitidos**, escolha o caminho para seu cenário:

   * Para tornar seu aplicativo lógico que possa ser chamado somente como um aplicativo lógico aninhado usando a [ação interna de aplicativos lógicos do Azure](../logic-apps/logic-apps-http-endpoint.md), selecione **somente outros aplicativos lógicos**, que *só* funcionarão quando você usar a ação **aplicativos lógicos do Azure** para chamar o aplicativo lógico aninhado.

     Essa opção grava uma matriz vazia em seu recurso de aplicativo lógico e requer que apenas chamadas de aplicativos lógicos pai que usam a ação interna de **aplicativos lógicos do Azure** possam disparar o aplicativo lógico aninhado.

   * Para fazer com que seu aplicativo lógico seja chamado somente como um aplicativo aninhado usando a ação HTTP, selecione **intervalos de IP específicos**, *não* **apenas outros aplicativos lógicos**. Quando a caixa **intervalos de IP para gatilhos** for exibida, insira os [endereços IP de saída](../logic-apps/logic-apps-limits-and-config.md#outbound)do aplicativo lógico pai. Um intervalo de IP válido usa estes formatos: x. x. x *. x/x* ou x. x. x. x *-x.* x. x.

     > [!NOTE]
     > Se você usar a opção **somente outros aplicativos lógicos** e a ação http para chamar seu aplicativo lógico aninhado, a chamada será bloqueada e você receberá um erro "401 não autorizado".

   * Para cenários em que você deseja restringir chamadas de entrada de outros IPs, quando a caixa **intervalos de IP para gatilhos** for exibida, especifique os intervalos de endereços IP que o gatilho aceita. Um intervalo de IP válido usa estes formatos: x. x. x *. x/x* ou x. x. x. x *-x.* x. x.

1. Opcionalmente, em **restringir chamadas para obter mensagens de entrada e saída do histórico de execução para os endereços IP fornecidos**, você pode especificar os intervalos de endereços IP para chamadas de entrada que podem acessar mensagens de entrada e saída no histórico de execução.

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[Modelo do Resource Manager](#tab/azure-resource-manager)

No modelo do ARM, especifique os intervalos de endereços IP de entrada permitidos na definição de recurso do aplicativo lógico usando a `accessControl` seção. Nesta seção, use as `triggers` seções, `actions` e opcionais, `contents` conforme apropriado, incluindo a `allowedCallerIpAddresses` seção com a `addressRange` propriedade e defina o valor da propriedade para o intervalo de IP permitido no formato *x.* x. x. x/x ou *x.x.x. x-x. x* . x. x.

* Se seu aplicativo lógico aninhado usar a única opção de **outros aplicativos lógicos** , que permite chamadas de entrada somente de outros aplicativos lógicos que usam a ação interna de aplicativos lógicos do Azure, defina a `allowedCallerIpAddresses` propriedade como uma matriz vazia (**[]**) e *omita* a `addressRange` propriedade.

* Se seu aplicativo lógico aninhado usar a opção **intervalos de IP específicos** para outras chamadas de entrada, como outros aplicativos lógicos que usam a ação http, inclua a `allowedCallerIpAddresses` seção e defina a `addressRange` propriedade como o intervalo de IP permitido.

Este exemplo mostra uma definição de recurso para um aplicativo lógico aninhado que permite chamadas de entrada somente de aplicativos lógicos que usam a ação interna de aplicativos lógicos do Azure:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Este exemplo mostra uma definição de recurso para um aplicativo lógico aninhado que permite chamadas de entrada de aplicativos lógicos que usam a ação HTTP:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicativo lógico

Você pode permitir que apenas usuários ou grupos específicos executem tarefas específicas, como gerenciar, editar e exibir aplicativos lógicos. Para controlar suas permissões, use o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/role-assignments-portal.md) para que você possa atribuir funções personalizadas ou internas aos membros em sua assinatura do Azure:

* [Colaborador de Aplicativo Lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite o gerenciamento de aplicativos lógicos, mas você não pode alterar o acesso a eles.

* [Operador de Aplicativo Lógico](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite a leitura, habilitação e desabilitação de aplicativos lógicos, mas você não pode editá-los ou atualizá-los.

Para impedir que outras pessoas alterem ou excluam seu aplicativo lógico, você pode usar [Bloqueio de Recursos do Azure](../azure-resource-manager/management/lock-resources.md). Essa funcionalidade ajuda a evitar que outras pessoas alterem ou excluam recursos de produção.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acessar dados do histórico de execuções

Durante a execução de um aplicativo lógico, todos os dados são [criptografados durante o trânsito](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) usando protocolo TLS e [em repouso](../security/fundamentals/encryption-atrest.md). Quando seu aplicativo lógico terminar de ser executado, você poderá exibir o histórico dessa execução, incluindo as etapas que foram executadas, o status, a duração, as entradas e as saídas de cada ação. Esse alto nível de detalhes fornece informações sobre como seu aplicativo lógico foi executado e onde você pode começar a solucionar problemas que surjam.

Quando você exibe o histórico de execução do aplicativo lógico, os Aplicativos Lógicos autenticam seu acesso e fornecem links de entradas e saídas para as solicitações e respostas de cada execução. No entanto, para ações que lidam com senhas, segredos, chaves ou outras informações confidenciais, convém impedir que outras pessoas exibam e acessem esses dados. Por exemplo, se o seu aplicativo lógico obtiver um segredo do [Azure Key Vault](../key-vault/general/overview.md) para usar ao autenticar uma ação HTTP, oculte esse segredo da exibição.

Para controlar o acesso às entradas e saídas no histórico de execuções do aplicativo lógico, você tem estas opções:

* [Restringir o acesso pelo intervalo de endereços IP](#restrict-ip).

  Essa opção ajuda a proteger o acesso ao histórico de execuções com base nas solicitações de um intervalo de endereços IP específico.

* [Proteger os dados no histórico de execução usando a ofuscação](#obfuscate).

  Em muitos gatilhos e ações, você pode proteger as entradas, as saídas ou ambas no histórico de execuções de um aplicativo lógico.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por intervalo de endereços IP

Você pode limitar o acesso às entradas e saídas no histórico de execuções do aplicativo lógico para que somente as solicitações de intervalos de endereços IP específicos possam exibir esses dados.

Por exemplo, para impedir que qualquer pessoa acesse entradas e saídas, especifique um intervalo de endereços IP, como `0.0.0.0-0.0.0.0`. Apenas uma pessoa com permissões de administrador pode remover essa restrição, o que dá a possibilidade de acesso "Just-In-Time" aos dados do aplicativo lógico.

Para especificar os intervalos de IP permitidos, siga estas etapas para o portal do Azure ou seu modelo de Azure Resource Manager:

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **Configurações**, selecione **Configurações de fluxo de trabalho**.

1. Em **Configuração de controle de acesso** > **Endereços IP de entrada permitidos**, selecione **Intervalos IP específicos**.

1. Em **intervalos IP para conteúdo**, especifique os intervalos de endereços IP que podem acessar o conteúdo de entradas e saídas.

   Um intervalo IP válido usa estes formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x*

#### <a name="resource-manager-template"></a>[Modelo do Resource Manager](#tab/azure-resource-manager)

No modelo do ARM, especifique os intervalos de IP usando a seção `accessControl` com a `contents` seção na definição de recurso do aplicativo lógico, por exemplo:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Proteger dados no histórico de execuções usando ofuscação

Muitos gatilhos e ações têm configurações para proteger entradas, saídas ou ambos do histórico de execuções de um aplicativo lógico. Antes de usar essas configurações para ajudá-lo a proteger esses dados, examine estas considerações:

* Quando você obscurece as entradas ou saídas em um gatilho ou ação, os Aplicativos Lógicos não enviam os dados protegidos para a Análise de Logs do Azure. Além disso, não é possível adicionar [propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) a esse gatilho ou ação para monitoramento.

* A [API dos Aplicativos Lógicos para manipular o histórico de fluxo de trabalho](/rest/api/logic/) não retorna saídas seguras.

* Para proteger as saídas de uma ação que obscurece entradas ou oculta explicitamente as saídas, ative manualmente as **Saídas Seguras** nessa ação.

* Ative as **Entradas Seguras** ou **Saídas Seguras** nas ações de downstream em que você espera que o histórico de execuções oculte esses dados.

  **Configuração de Saídas Seguras**

  Quando você ativa manualmente as **Saídas Seguras** em um gatilho ou ação, os Aplicativos Lógicos ocultam essas saídas no histórico de execuções. Se uma ação de downstream usar explicitamente essas saídas protegidas como entradas, os Aplicativos Lógicos ocultarão as entradas dessa ação no histórico de execuções, mas *não habilitarão* a configuração de **Entradas Seguras** da ação.

  ![Saídas protegidas como entradas e impacto downstream na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações de Compor, Analisar JSON e Responder têm apenas a configuração de **Entradas Seguras**. Quando ativada, a configuração também oculta as saídas das ações. Se essas ações usarem explicitamente as saídas protegidas de upstream como entradas, os Aplicativos Lógicos ocultarão as entradas e saídas das ações, mas *não habilitarão* a configuração de **Entradas Seguras** dessas ações. Se uma ação de downstream usar explicitamente as saídas ocultas das ações de Compor, Analisar JSON ou Responder como entradas, os Aplicativos Lógicos *não ocultarão as entradas ou saídas da ação de downstream*.

  ![Saídas protegidas como entradas com impacto downstream em ações específicas](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Configuração de Entradas Seguras**

  Quando você ativa manualmente as **Saídas Seguras** em um gatilho ou ação, os Aplicativos Lógicos ocultam essas saídas no histórico de execuções. Se uma ação de downstream usar explicitamente as saídas visíveis desse gatilho ou ação como entradas, os Aplicativos Lógicos ocultarão as entradas dessa ação de downstream no histórico de execuções, mas *não habilitarão* as **Entradas Seguras** nessa ação e não ocultarão as saídas dessa ação.

  ![Entradas protegidas e impacto downstream na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se as ações de Compor, Analisar JSON e Responder usarem explicitamente as saídas visíveis do gatilho ou da ação que tem as entradas protegidas, os Aplicativos Lógicos ocultarão as entradas e saídas dessas ações, mas *não habilitarão* a configuração de **Entradas Seguras** dessas ações. Se uma ação de downstream usar explicitamente as saídas ocultas das ações de Compor, Analisar JSON ou Responder como entradas, os Aplicativos Lógicos *não ocultarão as entradas ou saídas da ação de downstream*.

  ![Entradas protegidas e impacto downstream em ações específicas](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Proteger entradas e saídas no designer

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

   ![Abrir o aplicativo lógico no Designer do Aplicativo Lógico](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou na ação em que você deseja proteger dados confidenciais, selecione o botão de reticências ( **...** ) e, em seguida, selecione **Configurações**.

   ![Abrir configurações de gatilho ou ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ative as **Entradas Seguras**, **Saídas Seguras** ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ativar "Entradas Seguras" ou "Saídas Seguras"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou o gatilho agora mostra um ícone de cadeado na barra de título.

   ![A barra de título da ação ou do gatilho mostra o ícone de cadeado](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Os tokens que representam saídas seguras de ações anteriores também mostram ícones de cadeado. Por exemplo, quando você seleciona tal saída da lista de conteúdo dinâmico a ser usada em uma ação, esse token mostra um ícone de cadeado.

   ![Selecionar o token para saída segura](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois que o aplicativo lógico for executado, você poderá exibir o histórico dessa execução.

   1. No painel de **Visão Geral** do aplicativo lógico, selecione a execução que você deseja exibir.

   1. No painel **Executar** do aplicativo lógico, expanda as ações que você deseja examinar.

      Se você optar por obscurecer as entradas e saídas, esses valores agora aparecerão ocultos.

      ![Entradas e saídas ocultas no histórico de execuções](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteger entradas e saídas no modo de exibição de código

Na definição de gatilho ou ação subjacente, adicione ou atualize a matriz `runtimeConfiguration.secureData.properties` com um destes valores ou ambos:

* `"inputs"`: Protege as entradas no histórico de execuções.
* `"outputs"`: Protege as saídas no histórico de execuções.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso a entradas de parâmetro

Se você implantar em ambientes diferentes, considere a possibilidade de parametrização dos valores na definição do fluxo de trabalho que variam de acordo com esses ambientes. Dessa forma, você pode evitar dados embutidos em código usando um [modelo do Azure Resource Manager](../azure-resource-manager/templates/overview.md) para implantar seu aplicativo lógico, proteger dados confidenciais definindo parâmetros protegidos e passar esses dados como entradas separadas por meio dos [parâmetros do modelo](../azure-resource-manager/templates/template-parameters.md) usando um [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md).

Por exemplo, se você estiver autenticando ações HTTP com o [OAuth do Azure AD](#azure-active-directory-oauth-authentication), defina e proteja os parâmetros que aceitam a ID e o segredo do cliente usados para autenticação. Para definir esses parâmetros em seu aplicativo lógico, use a seção `parameters` na definição de fluxo de trabalho do aplicativo lógico e um modelo do Resource Manager para implantação. Para proteger valores de parâmetro que você não deseja mostrar ao editar seu aplicativo lógico ou exibir o histórico de execuções, você pode definir os parâmetros usando o tipo `securestring` ou `secureobject` e a codificação conforme necessário. Parâmetros que têm esse tipo não são retornados com a definição de recurso e não são acessíveis ao exibir o recurso após a implantação. Para acessar esses valores de parâmetro durante o runtime, use a expressão `@parameters('<parameter-name>')` dentro de sua definição de fluxo de trabalho. Essa expressão é avaliada apenas em runtime e é descrita pela [Linguagem de Definição do Fluxo de Trabalho](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se você usar um parâmetro em um cabeçalho ou corpo de solicitação, esse parâmetro poderá ser visível quando você exibir o histórico de execuções do aplicativo lógico e a solicitação HTTP de saída. Defina também suas políticas de acesso de conteúdo adequadamente.
> Você também pode usar [ofuscação](#obfuscate) para ocultar entradas e saídas em seu histórico de execuções. Cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Portanto, se um segredo for usado lá, ele não será recuperável.

Para mais informações, confira estas seções neste tópico:

* [Proteger parâmetros em definições de fluxo de trabalho](#secure-parameters-workflow)
* [Proteger dados no histórico de execuções usando ofuscação](#obfuscate)

Se você [automatizar a implantação para aplicativos lógicos usando modelos do Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), poderá definir os [parâmetros de modelo](../azure-resource-manager/templates/template-parameters.md) seguros, que são avaliados na implantação, usando os tipos `securestring` e `secureobject`. Para definir parâmetros de modelo, use a seção de `parameters` de nível superior do modelo, que é separada e diferente da seção `parameters` da definição de fluxo de trabalho. Para fornecer os valores dos parâmetros de modelo, use um [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md) separado.

Por exemplo, se você usar segredos, poderá definir e usar parâmetros de modelo protegidos que recuperem esses segredos do [Azure Key Vault](../key-vault/general/overview.md) na implantação. Em seguida, você pode fazer referência ao cofre de chaves e ao segredo em seu arquivo de parâmetro. Para saber mais, consulte esses tópicos:

* [Passar valores confidenciais na implantação usando o Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)
* [Proteger parâmetros em modelos de Azure Resource Manager](#secure-parameters-deployment-template) mais adiante neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteger parâmetros em definições de fluxo de trabalho

Para proteger informações confidenciais na definição de fluxo de trabalho do aplicativo lógico, use parâmetros protegidos para que essas informações não fiquem visíveis depois que você salvar seu aplicativo lógico. Por exemplo, suponha que você tenha uma ação HTTP que requer autenticação básica e que usa um nome de usuário e uma senha. Na definição de fluxo de trabalho, a seção `parameters` define os parâmetros `basicAuthPasswordParam` e `basicAuthUsernameParam` usando o tipo `securestring`. Em seguida, a definição de ação faz referência a esses parâmetros na seção `authentication`.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Proteger parâmetros em modelos de implantação do Azure Resource Manager

Um [modelo do Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) para um aplicativo lógico tem várias seções `parameters`. Para proteger senhas, chaves, segredos e outras informações confidenciais, defina parâmetros protegidos no nível do modelo e da definição de fluxo de trabalho usando o tipo `securestring` ou `secureobject`. Em seguida, você pode armazenar esses valores no [Azure Key Vault](../key-vault/general/overview.md) e usar o [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md) para fazer referência ao cofre de chaves e ao segredo. Depois, o modelo recupera essas informações na implantação. Para obter mais informações, confira [Passar valores confidenciais na implantação usando o Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md).

Aqui estão mais informações sobre as seções `parameters`:

* No nível superior do modelo, uma seção `parameters` define os parâmetros para os valores que o modelo usa na *implantação*. Por exemplo, esses valores podem incluir cadeias de conexão para um ambiente de implantação específico. Você pode armazenar esses valores em um [arquivo de parâmetro](../azure-resource-manager/templates/parameter-files.md) separado, o que torna a alteração desses valores mais fácil.

* Dentro da definição de recurso do aplicativo lógico, mas fora de sua definição de fluxo de trabalho, uma seção `parameters` especifica os valores para os parâmetros da definição de fluxo de trabalho. Nessa seção, você pode atribuir esses valores usando expressões de modelo que fazem referência aos parâmetros do modelo. Essas expressões são avaliadas na implantação.

* Dentro de sua definição de fluxo de trabalho, uma seção `parameters` define os parâmetros que seu aplicativo lógico usa em runtime. Em seguida, você pode fazer referência a esses parâmetros dentro do fluxo de trabalho do aplicativo lógico usando expressões de definição de fluxo de trabalho, que são avaliadas em runtime.

Este modelo de exemplo tem várias definições de parâmetros protegidos que usam o tipo `securestring`:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma senha que é passada para o parâmetro `basicAuthPasswordParam` definição de fluxo de trabalho |
| `TemplateUsernameParam` | Um parâmetro de modelo que aceita um nome de usuário que é passado para o parâmetro `basicAuthUserNameParam` da definição de fluxo de trabalho |
| `basicAuthPasswordParam` | Um parâmetro de definição de fluxo de trabalho que aceita a senha para autenticação básica em uma ação HTTP |
| `basicAuthUserNameParam` | Um parâmetro de definição de fluxo de trabalho que aceita o nome de usuário para autenticação básica em uma ação HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Acesso para chamadas de saída para outros serviços e sistemas

Com base na capacidade do ponto de extremidade de destino, chamadas de saída enviadas pelo [gatilho http ou ação http](../connectors/connectors-native-http.md), dão suporte à criptografia e são protegidas com [TLS (Transport Layer Security) 1,0, 1,1 ou 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como protocolo SSL (SSL). Os aplicativos lógicos negociam com o ponto de extremidade de destino usando a versão mais alta possível com suporte. Por exemplo, se o ponto de extremidade de destino der suporte a 1,2, o gatilho ou ação HTTP usará a 1,2 primeiro. Caso contrário, o conector usará a próxima versão com suporte mais alta.

Aqui estão informações sobre certificados autoassinados TLS/SSL:

* Para aplicativos lógicos no ambiente global multilocatário do Azure, o conector HTTP não permite certificados TLS/SSL autoassinados. Se seu aplicativo lógico fizer uma chamada HTTP para um servidor e apresentar um certificado autoassinado por TLS/SSL, a chamada HTTP falhará com um `TrustFailure` erro.

* Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), o conector http permite certificados autoassinados para Handshakes TLS/SSL. No entanto, primeiro você deve [habilitar o suporte de certificado autoassinado](../logic-apps/create-integration-service-environment-rest-api.md#request-body) para um ISE existente ou um novo ISE usando a API REST de aplicativos lógicos e instalar o certificado público no `TrustedRoot` local.

Aqui estão mais maneiras de ajudar a proteger pontos de extremidade que manipulam chamadas enviadas de seu aplicativo lógico:

* [Adicionar autenticação a solicitações de saída](#add-authentication-outbound).

  Quando você usa o gatilho HTTP ou a ação para enviar chamadas de saída, você pode adicionar autenticação à solicitação enviada pelo seu aplicativo lógico. Por exemplo, você pode selecionar estes tipos de autenticação:

  * [Autenticação Básica](#basic-authentication)

  * [Autenticação de certificado de cliente](#client-certificate-authentication)

  * [Autenticação OAuth do Active Directory](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerenciada](#managed-identity-authentication)

* Restringir o acesso a endereços IP de aplicativo lógico.

  Todas as chamadas para pontos de extremidade de aplicativos lógicos originam-se de endereços IP especificamente designados que se baseiam nas regiões dos seus aplicativos lógicos. Você pode adicionar filtragem que aceite solicitações somente de endereços IP. Para obter esses endereços IP, confira [Limites e configuração para Aplicativos Lógicos do Azure](logic-apps-limits-and-config.md#firewall-ip-configuration).

* Aumente a segurança de conexões com sistemas locais.

  Os Aplicativos Lógicos do Azure fornecem integração com esses serviços para ajudar a fornecer uma comunicação local mais segura e confiável.

  * Gateway de dados local

    Muitos conectores gerenciados nos Aplicativos Lógicos do Azure facilitam conexões seguras com sistemas locais, como o Sistema de Arquivos, o SQL, o SharePoint e o DB2. O gateway envia dados de fontes locais em canais criptografados por meio do Barramento de Serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba [como o gateway de dados local funciona](logic-apps-gateway-install.md#gateway-cloud-service).

  * Conectar-se por meio do Gerenciamento de API do Azure

    O [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) fornece opções de conexão locais, como a rede privada virtual site a site e a integração do [ExpressRoute](../expressroute/expressroute-introduction.md) para o proxy seguro e a comunicação com sistemas locais. Se você tiver uma API que fornece acesso ao seu sistema local e tiver exposto essa API criando uma instância de serviço de [Gerenciamento de API](../api-management/get-started-create-service-instance.md), poderá chamar essa API no fluxo de trabalho do seu aplicativo lógico selecionando o gatilho ou a ação interna de gerenciamento de API no designer de aplicativo lógico.

    > [!NOTE]
    > O conector mostra apenas os serviços de gerenciamento de API em que você tem permissões para exibir e conectar, mas não mostra serviços de gerenciamento de API baseados em consumo.

    1. No designer do aplicativo lógico, digite `api management` na caixa de pesquisa. Escolha a etapa com base em se você está adicionando um gatilho ou uma ação:<p>

       * Se você estiver adicionando um gatilho, que é sempre a primeira etapa no fluxo de trabalho, selecione **escolher um gatilho de gerenciamento de API do Azure**.

       * Se você estiver adicionando uma ação, selecione **escolher uma ação de gerenciamento de API do Azure**.

       Este exemplo adiciona um gatilho:

       ![Adicionar gatilho de gerenciamento de API do Azure](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Selecione a instância do serviço de gerenciamento de API criada anteriormente.

       ![Selecionar instância do serviço de gerenciamento de API](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Selecione a chamada à API a ser usada.

       ![Selecionar API existente](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a solicitações de saída

Os pontos de extremidade HTTP e HTTPS dão suporte a vários tipos de autenticação. Em alguns gatilhos e ações que você usa para enviar chamadas de saída ou solicitações para esses pontos de extremidade, você pode especificar um tipo de autenticação. No designer do aplicativo lógico, gatilhos e ações que dão suporte à escolha de um tipo de autenticação têm uma propriedade de **autenticação** . No entanto, essa propriedade nem sempre pode ser exibida por padrão. Nesses casos, no gatilho ou na ação, abra a lista **Adicionar novo parâmetro** e selecione **autenticação**.

> [!IMPORTANT]
> Para proteger informações confidenciais que seu aplicativo lógico manipula, use parâmetros protegidos e codifique os dados conforme necessário.
> Para obter mais informações sobre como usar e proteger parâmetros, confira [Acesso a entradas de parâmetro](#secure-action-parameters).

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>Tipos de autenticação para gatilhos e ações que dão suporte à autenticação

Esta tabela identifica os tipos de autenticação que estão disponíveis nos gatilhos e ações em que você pode selecionar um tipo de autenticação:

| Tipo de autenticação | Gatilhos e ações com suporte |
|---------------------|--------------------------------|
| [Basic](#basic-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos do Azure, HTTP, HTTP + Swagger, Webhook HTTP |
| [Certificado do Cliente](#client-certificate-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos do Azure, HTTP, HTTP + Swagger, Webhook HTTP |
| [OAuth do Active Directory](#azure-active-directory-oauth-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos do Azure, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Bruta](#raw-authentication) | Gerenciamento de API do Azure, Serviços de Aplicativos do Azure, Azure Functions, HTTP, HTTP + Swagger, Webhook HTTP |
| [Identidade gerenciada](#managed-identity-authentication) | **Gatilhos e ações internas** <p><p>Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, webhook HTTP <p><p>**Conectores gerenciados** <p><p>Azure AD Identity Protection, automação do Azure, instância de contêiner do Azure, Data Explorer do Azure, Azure Data Factory, Azure Data Lake, grade de eventos do Azure, Azure IoT Central v3, Azure Key Vault, Azure Resource Manager, Azure Sentinel, HTTP com o Azure AD <p><p>**Observação**: o suporte para conectores gerenciados está atualmente em visualização. |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>Autenticação Básica

Se a opção [Básica](../active-directory-b2c/secure-rest-api.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Basic | O tipo de autenticação a ser usado |
| **Nome de usuário** | `username` | Sim | <*nome do usuário*>| O nome de usuário para autenticar o acesso ao ponto de extremidade de serviço de destino |
| **Senha** | `password` | Sim | <'*senha*> | A senha para autenticar o acesso ao ponto de extremidade de serviço de destino |
||||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em runtime. Este exemplo de definição de ação HTTP especifica o `type` da autenticação como `Basic` e usa a [função parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>Autenticação de certificado do cliente

Se a opção [Certificado do Cliente](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Certificado do Cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a ser usado. Você pode gerenciar certificados com o [Gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates.md). <p></p>**Observação**: os conectores personalizados não dão suporte à autenticação baseada em certificado para chamadas de entrada e de saída. |
| **Pfx** | `pfx` | Sim | <*conteúdo de arquivo pfx codificado*> | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) <p><p>Para converter o arquivo PFX em formato codificado em base64, você pode usar o PowerShell seguindo estas etapas: <p>1. Salve o conteúdo do certificado em uma variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converta o conteúdo do certificado usando a função `ToBase64String()` e salve esse conteúdo em um arquivo de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**Solução de problemas**: se você usar o `cert mmc/PowerShell` comando, poderá obter esse erro: <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>Para resolver esse erro, tente converter o arquivo PFX em um arquivo PEM e voltar novamente usando o `openssl` comando: <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>Posteriormente, quando você obtém a cadeia de caracteres codificada em base64 para o arquivo PFX recém convertido do certificado, a cadeia de caracteres agora funciona nos aplicativos lógicos do Azure. |
| **Senha** | `password`| Não | <*senha para arquivo pfx*> | A senha para acessar o arquivo PFX |
|||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em runtime. Este exemplo de definição de ação HTTP especifica o `type` da autenticação como `ClientCertificate` e usa a [função parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Para mais informações sobre como proteger serviços usando a autenticação de certificado do cliente, confira estes tópicos:

* [Melhorar a segurança para APIs usando a autenticação de certificado de cliente no Gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Melhorar a segurança para serviços de back-end usando a autenticação de certificado de cliente no Gerenciamento de API do Azure](../api-management/api-management-howto-mutual-certificates.md)
* [Melhorar a segurança de seu serviço RESTfuL usando certificados de cliente](../active-directory-b2c/secure-rest-api.md)
* [Credenciais de certificado para autenticação de aplicativo](../active-directory/develop/active-directory-certificate-credentials.md)
* [Usar um certificado TLS/SSL no seu código no Serviço de Aplicativo do Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Autenticação do Azure Active Directory

Em gatilhos de solicitação, você pode usar [Azure Active Directory autenticação aberta (Azure ad OAuth)](../active-directory/develop/index.yml)para autenticar chamadas de entrada depois de [configurar as políticas de autorização do Azure ad](#enable-oauth) para seu aplicativo lógico. Para todos os outros gatilhos e ações que fornecem o tipo de autenticação **OAuth do Active Directory** como opção, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **OAuth do Active Directory** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a ser usado. Os Aplicativos Lógicos do Azure atualmente seguem o [protocolo OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Autoridade** | `authority` | Não | <*URL-for-authority-token-issuer*> | A URL para a autoridade que fornece o token de acesso. Por padrão, esse valor é `https://login.windows.net`. |
| **Locatário** | `tenant` | Sim | <*tenant-ID*> | A ID do locatário para o locatário do Azure AD |
| **Público-alvo** | `audience` | Sim | <*resource-to-authorize*> | O recurso que você deseja usar para autorização, por exemplo, `https://management.core.windows.net/` |
| **ID do Cliente** | `clientId` | Sim | <*client-ID*> | A ID do cliente para o aplicativo solicitando a autorização |
| **Tipo de Credencial** | `credentialType` | Sim | Certificado <br>ou <br>Segredo | O tipo de credencial que o cliente usa para solicitar a autorização. Essa propriedade e o valor não aparecem na definição subjacente do aplicativo lógico, mas determinam as propriedades que aparecem para o tipo de credencial selecionado. |
| **Segredo** | `secret` | Sim, somente para o tipo de credencial "Segredo" | <*segredo do cliente*> | O segredo do cliente para solicitar autorização |
| **Pfx** | `pfx` | Sim, mas somente para o tipo de credencial "Certificado" | <*conteúdo de arquivo pfx codificado*> | O conteúdo codificado na base64 do arquivo PFX (Troca de Informações Pessoais) |
| **Senha** | `password` | Sim, mas somente para o tipo de credencial "Certificado" | <*senha para arquivo pfx*> | A senha para acessar o arquivo PFX |
|||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em runtime. Este exemplo de definição de ação HTTP especifica o `type` da autenticação como `ActiveDirectoryOAuth`, o tipo de credencial como `Secret` e usa a [função parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>Autenticação bruta

Se a opção **Bruta** estiver disponível, você poderá usar esse tipo de autenticação quando tiver que usar [esquemas de autenticação](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) que não seguem o [protocolo OAuth 2.0](https://oauth.net/2/). Com esse tipo, você cria manualmente o valor do cabeçalho de autorização enviado com a solicitação de saída e especifica esse valor de cabeçalho em seu gatilho ou ação.

Confira a seguir um cabeçalho de exemplo para uma solicitação HTTPS que segue o [protocolo OAuth 1.0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

No gatilho ou na ação que dá suporte à autenticação bruta, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Obrigatório | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Raw | O tipo de autenticação a ser usado |
| **Valor** | `value` | Sim | <*valor do cabeçalho de autorização*> | O valor do cabeçalho de autorização a ser usado para autenticação |
||||||

Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em runtime. Este exemplo de definição de ação HTTP especifica o `type` da autenticação como `Raw` e usa a [função parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>Autenticação de identidade gerenciada

Quando a opção de [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) está disponível no [gatilho ou na ação que dá suporte à autenticação de identidade gerenciada](#add-authentication-outbound), seu aplicativo lógico pode usar a identidade atribuída pelo sistema ou uma *única* identidade atribuída pelo usuário criada manualmente para autenticar o acesso aos recursos do Azure protegidos pelo Azure Active Directory (AD do Azure), em vez de credenciais, segredos ou tokens do Azure AD. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não tem os segredos de gerenciamento ou usam diretamente tokens do Azure AD. Saiba mais sobre [serviços do Azure que dão suporte a identidades gerenciadas para a autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

1. Antes que seu aplicativo lógico possa usar uma identidade gerenciada, siga as etapas em [Autenticar o acesso aos recursos do Azure usando identidades gerenciadas nos Aplicativos Lógicos do Azure](../logic-apps/create-managed-service-identity.md). Essas etapas habilitam a identidade gerenciada em seu aplicativo lógico e configuram o acesso da identidade ao recurso de destino do Azure.

1. Antes que uma função do Azure possa usar uma identidade gerenciada, primeiro [habilite a autenticação para as funções do Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

1. No gatilho ou ação que dá suporte ao uso de uma identidade gerenciada, forneça estas informações:

   **Gatilhos e ações internas**

   | Propriedade (designer) | Propriedade (JSON) | Obrigatório | Valor | Descrição |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Sim | **Identidade gerenciada** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a ser usado |
   | **Identidade gerenciada** | `identity` | Sim | * **Identidade gerenciada atribuída ao sistema** <br>ou <br>`SystemAssigned` <p><p>* <*nome da identidade atribuído pelo usuário*> | A identidade gerenciada a ser usada |
   | **Público-alvo** | `audience` | Sim | <*ID do recurso de destino*> | A ID do recurso de destino que você deseja acessar. <p>Por exemplo, `https://storage.azure.com/` cria os [tokens de acesso](../active-directory/develop/access-tokens.md) para autenticação válidos em todas as contas de armazenamento. No entanto, você também pode especificar uma URL de serviço raiz, como `https://fabrikamstorageaccount.blob.core.windows.net` para uma conta de armazenamento específica. <p>**Observação**: A propriedade **Público** pode estar oculta em alguns gatilhos ou ações. Para tornar essa propriedade visível, no gatilho ou na ação, abra a lista **Adicionar novo parâmetro** e selecione **Público**. <p><p>**Importante**: Verifique se essa ID de recurso de destino *corresponde exatamente* ao valor que o Azure AD espera, incluindo quaisquer barras à direita necessárias. Portanto, a ID de recurso `https://storage.azure.com/` para todas as contas de armazenamento de BLOBs do Azure requer uma barra à direita. No entanto, a ID de recurso para uma conta de armazenamento específica não requer esse tipo de barra. Para encontrar essas IDs de recurso, confira [Serviços do Azure que dão suporte ao Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Quando você usa [parâmetros protegidos](#secure-action-parameters) para manipular e proteger informações confidenciais, por exemplo, em um [modelo do Azure Resource Manager para automatizar a implantação](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), você pode usar expressões para acessar esses valores de parâmetro em runtime. Por exemplo, essa definição de ação HTTP especifica a autenticação `type` como `ManagedServiceIdentity` e usa a [função Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores de parâmetro:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **Gatilhos e ações de conector gerenciado**

   | Propriedade (designer) | Obrigatório | Valor | Descrição |
   |---------------------|----------|-------|-------------|
   | **Nome da conexão** | Yes | <*nome da conexão*> ||
   | **Identidade gerenciada** | Yes | **Identidade gerenciada atribuída pelo sistema** <br>ou <br> <*nome-de-identidade gerenciado pelo usuário*> | O tipo de autenticação a ser usado |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Bloquear a criação de conexões

Se sua organização não permitir a conexão a recursos específicos usando seus conectores em aplicativos lógicos do Azure, você poderá [bloquear a capacidade de criar essas conexões](../logic-apps/block-connections-connectors.md) para conectores específicos em fluxos de trabalho de aplicativo lógico usando [Azure Policy](../governance/policy/overview.md). Para obter mais informações, consulte [Bloquear conexões criadas por conectores específicos nos aplicativos lógicos do Azure](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Diretrizes de isolamento para aplicativos lógicos

Você pode usar os aplicativos lógicos do Azure no [Azure governamental](../azure-government/documentation-government-welcome.md) que dão suporte a todos os níveis de impacto nas regiões descritas pelas [diretrizes de isolamento do nível 5 de impacto do Azure governamental](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) e pelo [SRG (guia de requisitos de segurança) do departamento de defesa dos EUA](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). Para atender a esses requisitos, os aplicativos lógicos dão suporte à capacidade de criar e executar fluxos de trabalho em um ambiente com recursos dedicados para que você possa reduzir o impacto no desempenho de outros locatários do Azure em seus aplicativos lógicos e evitar compartilhar recursos de computação com outros locatários.

* Para executar seu próprio código ou executar a transformação XML, [crie e chame uma função do Azure](../logic-apps/logic-apps-azure-functions.md), em vez de usar o [recurso de código embutido](../logic-apps/logic-apps-add-run-inline-code.md) ou fornecer [assemblies para usar como mapas](../logic-apps/logic-apps-enterprise-integration-maps.md), respectivamente. Além disso, configure o ambiente de hospedagem para seu aplicativo de funções para obedecer aos seus requisitos de isolamento.

  Por exemplo, para atender aos requisitos de impacto nível 5, crie seu aplicativo de funções com o [plano do serviço de aplicativo](../azure-functions/dedicated-plan.md) usando o tipo de [preço **isolado**](../app-service/overview-hosting-plans.md) junto com um [ambiente do serviço de aplicativo (ase)](../app-service/environment/intro.md) que também usa o tipo de preço **isolado** . Nesse ambiente, os aplicativos de funções são executados em máquinas virtuais do Azure dedicadas e em redes virtuais do Azure, que fornecem isolamento de rede sobre o isolamento de computação para seus aplicativos e recursos de expansão máxima. Para obter mais informações, consulte [diretrizes de isolamento do nível 5 de impacto do Azure governamental – Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Para saber mais, consulte esses tópicos:<p>

  * [Azure App planos de serviço](../app-service/overview-hosting-plans.md)
  * [Opções de rede do Azure Functions](../azure-functions/functions-networking-options.md)
  * [Hosts dedicados do Azure para máquinas virtuais](../virtual-machines/dedicated-hosts.md)
  * [Isolamento de máquina virtual no Azure](../virtual-machines/isolation.md)
  * [Implantar serviços dedicados do Azure em redes virtuais](../virtual-network/virtual-network-for-azure-services.md)

* Para criar aplicativos lógicos que são executados em recursos dedicados e podem acessar recursos protegidos por uma rede virtual do Azure, você pode criar um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Algumas redes virtuais do Azure usam pontos de extremidade privados ([link privado do Azure](../private-link/private-link-overview.md)) para fornecer acesso aos serviços de PaaS do Azure, como o armazenamento do azure, Azure Cosmos DB ou banco de dados SQL do Azure, serviços de parceiro ou serviços de cliente hospedados no Azure. Se seus aplicativos lógicos precisarem de acesso a redes virtuais que usam pontos de extremidade privados, você deverá criar, implantar e executar esses aplicativos lógicos dentro de um ISE.

  * Para obter mais controle sobre as chaves de criptografia usadas pelo armazenamento do Azure, você pode configurar, usar e gerenciar sua própria chave usando [Azure Key Vault](../key-vault/general/overview.md). Esse recurso também é conhecido como "Bring Your Own Key" (BYOK) e sua chave é chamada de "chave gerenciada pelo cliente". Para obter mais informações, consulte [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ambientes do serviço de integração (ISEs) em aplicativos lógicos do Azure](../logic-apps/customer-managed-keys-integration-service-environment.md).

Para saber mais, consulte esses tópicos:

* [Isolamento na nuvem pública do Azure](../security/fundamentals/isolation-choices.md)
* [Segurança para aplicativos IaaS altamente confidenciais no Azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Próximas etapas

* [Linha de base de segurança do Azure para aplicativos lógicos do Azure](../logic-apps/security-baseline.md)
* [Automatizar a implantação para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Monitorar aplicativos lógicos](../logic-apps/monitor-logic-apps-log-analytics.md)
