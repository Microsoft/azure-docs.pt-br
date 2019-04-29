---
title: Referência de modelo de dados de modelo do Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as representações de entidade e tipo para itens comuns usados nos modelos de dados para os modelos de portal do desenvolvedor no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094515"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência de modelo de dados de modelo do Gerenciamento de API do Azure
Este tópico descreve as representações de entidade e tipo para itens comuns usados nos modelos de dados para os modelos de portal do desenvolvedor no Gerenciamento de API do Azure.  
  
 Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

O portal do desenvolvedor não está disponível na camada de consumo.

## <a name="reference"></a>Referência

-   [API](#API)  
-   [Resumo da API](#APISummary)  
-   [Aplicativo](#Application)  
-   [Anexo](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Comentário](#Comment)  
-   [Filtragem](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Solicitação HTTP](#HTTPRequest)  
-   [Resposta HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [Operação](#Operation)  
-   [Menu de operação](#Menu)  
-   [Item de menu de operação](#MenuItem)  
-   [Paginação](#Paging)  
-   [Parâmetro](#Parameter)  
-   [Produto](#Product)  
-   [Provedor](#Provider)  
-   [Representação](#Representation)  
-   [Assinatura](#Subscription)  
-   [Resumo da assinatura](#SubscriptionSummary)  
-   [Informações de conta de usuário](#UserAccountInfo)  
-   [Entrada do usuário](#UseSignIn)  
-   [Inscrição do usuário](#UserSignUp)  
  
##  <a name="API"></a> API  
 A entidade `API` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a API na instância atual do serviço de Gerenciamento de API. O valor é uma URL relativa válida no formato de `apis/{id}` em que `{id}` é um identificador de API. Essa propriedade é somente leitura.|  
|`name`|string|O nome da API. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|A descrição da API. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`serviceUrl`|string|A URL absoluta do serviço de back-end implementado esta API.|  
|`path`|string|A URL relativa que identifica exclusivamente esta API e todos os seus caminhos de recurso dentro da instância do serviço de Gerenciamento de API. Ele é acrescentado à URL base do Ponto de Extremidade de API especificada durante a criação da instância de serviço para formar uma URL pública para essa API.|  
|`protocols`|matriz de números|Descreve em quais protocolos as operações nessa API podem ser invocadas. Os valores permitidos são `1 - http` e `2 - https` ou ambos.|  
|`authenticationSettings`|[Authorization server authentication settings](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings) (Configurações de autenticação do servidor de autorização)|Coleção de configurações de autenticação incluídas nessa API.|  
|`subscriptionKeyParameterNames`|objeto|Propriedade opcional que pode ser usada para especificar nomes personalizados para parâmetros de consulta e/ou cabeçalho que contêm a chave de assinatura. Quando essa propriedade estiver presente, ela deverá conter pelo menos uma das duas propriedades a seguir.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Resumo da API  
 A entidade `API summary` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a API na instância atual do serviço de Gerenciamento de API. O valor é uma URL relativa válida no formato de `apis/{id}` em que `{id}` é um identificador de API. Essa propriedade é somente leitura.|  
|`name`|string|O nome da API. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|A descrição da API. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
  
##  <a name="Application"></a> Aplicativo  
 A entidade `application` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador exclusivo do aplicativo.|  
|`Title`|string|O título do aplicativo.|  
|`Description`|string|A descrição do aplicativo.|  
|`Url`|URI|O URI do aplicativo.|  
|`Version`|string|As informações de versão do aplicativo.|  
|`Requirements`|string|Uma descrição dos requisitos do aplicativo.|  
|`State`|número|O estado atual do aplicativo.<br /><br /> – 0 – Registrado<br /><br /> – 1 – Enviado<br /><br /> – 2 – Publicado<br /><br /> – 3 – Rejeitado<br /><br /> – 4 – Não publicado|  
|`RegistrationDate`|DateTime|A data e hora em que o aplicativo foi registrado.|  
|`CategoryId`|número|A categoria do aplicativo (finanças, entretenimento etc.)|  
|`DeveloperId`|string|O identificador exclusivo do desenvolvedor que enviou o aplicativo.|  
|`Attachments`|Coleção de entidades de [Anexo](#Attachment).|Todos os anexos do aplicativo, como capturas de tela ou ícones.|  
|`Icon`|[Anexo](#Attachment)|O ícone do aplicativo.|  
  
##  <a name="Attachment"></a> Anexo  
 A entidade `attachment` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`UniqueId`|string|O identificador exclusivo do anexo.|  
|`Url`|string|A URL do recurso.|  
|`Type`|string|O tipo de anexo.|  
|`ContentType`|string|O tipo de mídia do anexo.|  
  
##  <a name="Sample"></a> Exemplo de código  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`title`|string|O nome da operação.|  
|`snippet`|string|Essa propriedade foi preterida e não deve ser usada.|  
|`brush`|string|Qual modelo de coloração da sintaxe do código a ser usado ao exibir o exemplo de código. Os valores permitidos são `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` e `csharp`.|  
|`template`|string|O nome desse modelo de exemplo de código.|  
|`body`|string|Um espaço reservado para a parte do exemplo de código do snippet de código.|  
|`method`|string|O método HTTP da operação.|  
|`scheme`|string|O protocolo a ser usado para a solicitação de operação.|  
|`path`|string|O caminho da operação.|  
|`query`|string|Exemplo de cadeia de caracteres de consulta com parâmetros definidos.|  
|`host`|string|A URL do gateway de serviço de Gerenciamento de API para a API que contém esta operação.|  
|`headers`|Coleção de entidades de [Cabeçalho](#Header).|Cabeçalhos para esta operação.|  
|`parameters`|Coleção de entidade de [Parâmetro](#Parameter).|Parâmetros que são definidos para essa operação.|  
  
##  <a name="Comment"></a> Comentário  
 A entidade `API` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|número|A ID do comentário.|  
|`CommentText`|string|O corpo do comentário. Pode incluir HTML.|  
|`DeveloperCompany`|string|O nome da empresa do desenvolvedor.|  
|`PostedOn`|DateTime|A data e hora em que o comentário foi postado.|  
  
##  <a name="Issue"></a> Problema  
 A entidade `issue` tem as seguintes propriedades.  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador exclusivo do problema.|  
|`ApiID`|string|A ID da API para a qual esse problema foi relatado.|  
|`Title`|string|Título do problema.|  
|`Description`|string|Descrição do problema.|  
|`SubscriptionDeveloperName`|string|Nome do desenvolvedor que relatou o problema.|  
|`IssueState`|string|O estado atual do problema. Os valores possíveis são Proposto, Aberto e Fechado.|  
|`ReportedOn`|DateTime|A data e hora em que o problema foi relatado.|  
|`Comments`|Coleção de entidades de [Comentário](#Comment).|Comentários sobre este problema.|  
|`Attachments`|Coleção de entidades de [Anexo](api-management-template-data-model-reference.md#Attachment).|Quaisquer anexos para o problema.|  
|`Services`|Coleção de entidades de [API](#API).|As APIs assinadas pelo usuário que arquivou o problema.|  
  
##  <a name="Filtering"></a> Filtragem  
 A entidade `filtering` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Pattern`|string|O termo de pesquisa atual ou `null` se não houver nenhum termo de pesquisa.|  
|`Placeholder`|string|O texto a ser exibido na caixa de pesquisa quando não há nenhum termo de pesquisa especificado.|  
  
##  <a name="Header"></a> Cabeçalho  
 Esta seção descreve a representação de `parameter`.  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do cabeçalho.|  
|`typeName`|string|Tipo de dados do valor do cabeçalho.|  
|`options`|string|Opções.|  
|`required`|boolean|Se o cabeçalho é necessário.|  
|`readOnly`|boolean|Se o cabeçalho é somente leitura.|  
  
##  <a name="HTTPRequest"></a> Solicitação HTTP  
 Esta seção descreve a representação de `request`.  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`description`|string|Descrição da solicitação de operação.|  
|`headers`|matriz de entidades de [Cabeçalho](#Header).|Cabeçalhos de solicitação.|  
|`parameters`|matriz de [Parâmetro](#Parameter)|Coleção de parâmetros de solicitação da operação.|  
|`representations`|matriz de [Representação](#Representation)|Coleção de representações de solicitação da operação.|  
  
##  <a name="HTTPResponse"></a> Resposta HTTP  
 Esta seção descreve a representação de `response`.  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`statusCode`|número inteiro positivo|Código de status de resposta da operação.|  
|`description`|string|Descrição da resposta da operação.|  
|`representations`|matriz de [Representação](#Representation)|Coleção de representações de resposta da operação.|  
  
##  <a name="Operation"></a> Operação  
 A entidade `operation` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a operação na instância atual do serviço de Gerenciamento de API. O valor é uma URL relativa válida no formato de `apis/{aid}/operations/{id}` em que `{aid}` é um identificador de API e `{id}` é um identificador de API. Essa propriedade é somente leitura.|  
|`name`|string|Nome da operação. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da operação. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`scheme`|string|Descreve em quais protocolos as operações nessa API podem ser invocadas. Os valores permitidos são `http` e `https` ou `http` e `https`.|  
|`uriTemplate`|string|Modelo de URL relativa identificando o recurso de destino para esta operação. Pode incluir parâmetros. Exemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|A URL de gateway de Gerenciamento de API que hospeda a API.|  
|`httpMethod`|string|Método HTTP da operação.|  
|`request`|[Solicitação HTTP](#HTTPRequest)|Uma entidade que contém detalhes da solicitação.|  
|`responses`|matriz de [Resposta HTTP](#HTTPResponse)|Matriz de entidades de [Resposta HTTP](#HTTPResponse) da operação.|  
  
##  <a name="Menu"></a> Menu de operação  
 A entidade `operation menu` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`ApiId`|string|A ID da API atual.|  
|`CurrentOperationId`|string|A ID da operação atual.|  
|`Action`|string|O tipo de menu.|  
|`MenuItems`|Coleção de entidades de [Item de menu de operação](#MenuItem).|As operações para a API do atual.|  
  
##  <a name="MenuItem"></a> Item de menu de operação  
 A entidade `operation menu item` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|string|A ID da operação.|  
|`Title`|string|A descrição da operação.|  
|`HttpMethod`|string|O método HTTP da operação.|  
  
##  <a name="Paging"></a> Paginação  
 A entidade `paging` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Page`|número|O número da página atual.|  
|`PageSize`|número|O número máximo de resultados a serem exibidos em uma única página.|  
|`TotalItemCount`|número|O número de itens a serem exibidos.|  
|`ShowAll`|boolean|Se todos os resultados devem ser mostrados em uma única página.|  
|`PageCount`|número|O número de páginas de resultados.|  
  
##  <a name="Parameter"></a> Parâmetro  
 Esta seção descreve a representação de `parameter`.  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor de parâmetro.|  
|`options`|matriz de cadeias de caracteres|Valores definidos para os valores de parâmetro de consulta.|  
|`required`|boolean|Especifica se o parâmetro é necessário ou não.|  
|`kind`|número|Se esse parâmetro for um parâmetro de caminho (1) ou um parâmetro de cadeia de caracteres de consulta (2).|  
|`typeName`|string|Tipo de parâmetro.|  
  
##  <a name="Product"></a> Produto  
 A entidade `product` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente o produto na instância atual do serviço de Gerenciamento de API. O valor é uma URL relativa válida no formato de `products/{pid}` em que `{pid}` é um identificador de produto. Essa propriedade é somente leitura.|  
|`Title`|string|O nome do produto. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`Description`|string|Descrição do produto. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`Terms`|string|Termos de uso do produto. Os desenvolvedores que tentarem assinar o produto verão e deverão aceitar esses termos para concluir o processo de assinatura.|  
|`ProductState`|número|Especifica se o produto está publicado ou não. Os produtos publicados são detectáveis por desenvolvedores no portal do desenvolvedor. Os produtos não publicados são visíveis apenas para administradores.<br /><br /> Os valores permitidos para o estado do produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica se um usuário pode ter várias assinaturas para este produto ao mesmo tempo.|  
|`MultipleSubscriptionsCount`|número|Número máximo de assinaturas para esse produto que um usuário pode ter ao mesmo tempo.|  
  
##  <a name="Provider"></a> Provedor  
 A entidade `provider` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Properties`|dicionário de cadeia de caracteres|Propriedades desse provedor de autenticação.|  
|`AuthenticationType`|string|O tipo de provedor. (Azure Active Directory, logon do Facebook, Conta do Google, Conta da Microsoft, Twitter).|  
|`Caption`|string|Nome de exibição do provedor.|  
  
##  <a name="Representation"></a> Representação  
 Esta seção descreve uma `representation`.  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`contentType`|string|Especifica um tipo de conteúdo registrado ou personalizado para essa representação, por exemplo, `application/xml`.|  
|`sample`|string|Um exemplo da representação.|  
  
##  <a name="Subscription"></a> Assinatura  
 A entidade `subscription` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente a assinatura na instância atual do serviço de Gerenciamento de API. O valor é uma URL relativa válida no formato de `subscriptions/{sid}` em que `{sid}` é um identificador de assinatura. Essa propriedade é somente leitura.|  
|`ProductId`|string|O identificador de recurso do produto assinado. O valor é uma URL relativa válida no formato de `products/{pid}` em que `{pid}` é um identificador de produto.|  
|`ProductTitle`|string|O nome do produto. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`ProductDescription`|string|Descrição do produto. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`ProductDetailsUrl`|string|A URL relativa para os detalhes do produto.|  
|`state`|string|O estado da assinatura. Os possíveis estados são:<br /><br /> - `0 - suspended` – a assinatura está bloqueada e o assinante não pode chamar APIs do produto.<br /><br /> - `1 - active` – a assinatura está ativa.<br /><br /> - `2 - expired` – a assinatura atingiu sua data de validade e foi desativada.<br /><br /> - `3 - submitted` – a solicitação de assinatura foi feita pelo desenvolvedor, mas ainda não foi aprovada ou rejeitada.<br /><br /> - `4 - rejected` – a solicitação de assinatura foi negada por um administrador.<br /><br /> - `5 - cancelled` – a assinatura foi cancelada pelo desenvolvedor ou administrador.|  
|`DisplayName`|string|Nome de exibição da assinatura.|  
|`CreatedDate`|dateTime|A data em que a assinatura foi criada, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Se a assinatura puder ser cancelada pelo usuário atual.|  
|`IsAwaitingApproval`|boolean|Se a assinatura está aguardando aprovação.|  
|`StartDate`|dateTime|A data de início da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|dateTime|A data de validade da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|dateTime|A data de notificação da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|A chave de assinatura primária. O comprimento máximo é de 256 caracteres.|  
|`secondaryKey`|string|A chave de assinatura secundária. O comprimento máximo é de 256 caracteres.|  
|`CanBeRenewed`|boolean|Se a assinatura pode ser renovada pelo usuário atual.|  
|`HasExpired`|boolean|Se a assinatura expirou.|  
|`IsRejected`|boolean|Se a solicitação de assinatura foi negada.|  
|`CancelUrl`|string|A URL relativa para cancelar a assinatura.|  
|`RenewUrl`|string|A URL relativa para renovar a assinatura.|  
  
##  <a name="SubscriptionSummary"></a> Resumo da assinatura  
 A entidade `subscription summary` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente a assinatura na instância atual do serviço de Gerenciamento de API. O valor é uma URL relativa válida no formato de `subscriptions/{sid}` em que `{sid}` é um identificador de assinatura. Essa propriedade é somente leitura.|  
|`DisplayName`|string|O nome de exibição da assinatura|  
  
##  <a name="UserAccountInfo"></a> Informações de conta de usuário  
 A entidade `user account info` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`FirstName`|string|Nome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`Email`|string|Endereço de email. Não deve estar vazio e deve ser exclusivo dentro da instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha da conta de usuário.|  
|`NameIdentifier`|string|Identificador de conta, igual ao email do usuário.|  
|`ProviderName`|string|Nome do provedor de autenticação.|  
|`IsBasicAccount`|boolean|True se essa conta foi registrada usando o email e a senha, false se a conta foi registrada usando um provedor.|  
  
##  <a name="UseSignIn"></a> Entrada do usuário  
 A entidade `user sign in` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`Email`|string|Endereço de email. Não deve estar vazio e deve ser exclusivo dentro da instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha da conta de usuário.|  
|`ReturnUrl`|string|A URL da página em que o usuário clicou para entrar.|  
|`RememberMe`|boolean|Se deve salvar as informações do usuário atual.|  
|`RegistrationEnabled`|boolean|Se o registro está habilitado.|  
|`DelegationEnabled`|boolean|Se a entrada delegada está habilitada.|  
|`DelegationUrl`|string|A URL de entrada delegada, se estiver habilitada.|  
|`SsoSignUpUrl`|string|O logon único na URL para o usuário, se estiver presente.|  
|`AuxServiceUrl`|string|Se o usuário atual for um administrador, isso será um link para a instância de serviço no portal do Azure.|  
|`Providers`|Coleção de entidades de [Provedor](#Provider)|Os provedores de autenticação para este usuário.|  
|`UserRegistrationTerms`|string|Termos que um usuário deve aceitar antes de entrar.|  
|`UserRegistrationTermsEnabled`|boolean|Se os termos estão habilitados.|  
  
##  <a name="UserSignUp"></a> Inscrição do usuário  
 A entidade `user sign up` tem as seguintes propriedades:  
  
|Propriedade|Type|DESCRIÇÃO|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor usado pelo controle [sign-up](api-management-page-controls.md#sign-up).|  
|`Password`|string|Senha da conta de usuário.|  
|`PasswordVerdictLevel`|número|Valor usado pelo controle [sign-up](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|string|Termos que um usuário deve aceitar antes de entrar.|  
|`UserRegistrationTermsOptions`|número|Valor usado pelo controle [sign-up](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|boolean|Valor usado pelo controle [sign-up](api-management-page-controls.md#sign-up).|  
|`Email`|string|Endereço de email. Não deve estar vazio e deve ser exclusivo dentro da instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`FirstName`|string|Nome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`UserData`|string|Valor usado pelo controle [sign-up](api-management-page-controls.md#sign-up).|  
|`NameIdentifier`|string|Valor usado pelo controle [sign-up](api-management-page-controls.md#sign-up).|  
|`ProviderName`|string|Nome do provedor de autenticação.|

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
