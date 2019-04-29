---
title: Recursos de modelo no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre os tipos de recursos disponíveis para uso em modelos de portal do desenvolvedor no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 673dcbeb630899eebc328cd4fae16f7fe8f47a55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557878"
---
# <a name="azure-api-management-template-resources"></a>Recursos de modelo no Gerenciamento de API do Azure
O Gerenciamento de API do Azure fornece os tipos de recursos a seguir para uso em modelos de portal do desenvolvedor.  
  
-   [Recursos de cadeia de caracteres](#strings)  
  
-   [Recursos de glifo](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Recursos de cadeia de caracteres  
 O Gerenciamento de API fornece um conjunto abrangente de recursos de cadeia de caracteres para uso no portal do desenvolvedor. Esses recursos estão localizados em todos os idiomas aos quais o Gerenciamento de API dá suporte. O conjunto de modelos padrão usa esses recursos para cabeçalhos de página, rótulos e quaisquer cadeias de caracteres constantes que sejam exibidas no portal do desenvolvedor. Para usar um recurso de cadeia de caracteres em seus modelos, forneça o prefixo de cadeia de caracteres do recurso seguido do nome da cadeia de caracteres, conforme mostrado no exemplo a seguir.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 O exemplo a seguir é com base no modelo Lista de produtos e exibe **Produtos** na parte superior da página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Há suporte para as seguintes opções de localização:

| Local    | Linguagem               |
|-----------|------------------------|
| “pt-br”      | "Inglês"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "it"      | "Italiano"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 Consulte as tabelas a seguir para ver os recursos de cadeia de caracteres disponíveis para uso em seus modelos de portal do desenvolvedor. Use o nome da tabela como o prefixo para os recursos de cadeia de caracteres nessa tabela.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentação](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|NOME|Text|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|NOME|Text|  
|----------|----------|  
|WebApplicationsDetailsTitle|Visualização de aplicativo|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|Captura de tela|  
|WebApplicationsScreenshotsHeader|Capturas de tela|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|NOME|Text|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Tem certeza de que deseja remover o aplicativo?|  
|WebDevelopersAppNotPublished|Não publicado|  
|WebDevelopersAppNotSubmitted|Não enviado|  
|WebDevelopersAppTableCategoryHeader|Category|  
|WebDevelopersAppTableNameHeader|NOME|  
|WebDevelopersAppTableStateHeader|Estado|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registrar aplicativo|  
|WebDevelopersRemoveLink|Remover|  
|WebDevelopersSubmitLink|Enviar|  
|WebDevelopersYourApplicationsHeader|Seus aplicativos|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|NOME|Text|  
|----------|----------|  
|WebApplicationsHeader|Aplicativos|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|NOME|Text|  
|----------|----------|  
|NoItemsToDisplay|Nenhum resultado encontrado.|  
|GeneralExceptionMessage|Algo não está correto. Pode ser uma falha temporária ou um bug. Tente novamente.|  
|GeneralJsonExceptionMessage|Algo não está correto. Pode ser uma falha temporária ou um bug. Recarregue a página e tente novamente.|  
|ConfirmationMessageUnsavedChanges|Há algumas alterações não salvas. Tem certeza de que deseja descartar as alterações?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|O corpo da solicitação HTTP é grande demais.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|NOME|Text|  
|----------|----------|  
|ButtonLabelCancel|Cancelar|  
|ButtonLabelSave|Salvar|  
|GeneralExceptionMessage|Algo não está correto. Pode ser uma falha temporária ou um bug. Tente novamente.|  
|NoItemsToDisplay|Não existem itens a apresentar.|  
|PagerButtonLabelFirst|Primeiro|  
|PagerButtonLabelLast|Último|  
|PagerButtonLabelNext|Avançar|  
|PagerButtonLabelPrevious|Prev|  
|PagerLabelPageNOfM|Página {0} de {1}|  
|PasswordTooShort|A senha é muito curta|  
|EmailAsPassword|Não use seu email como sua senha|  
|PasswordSameAsUserName|Sua senha não pode conter seu nome de usuário|  
|PasswordTwoCharacterClasses|Use classes de caracteres diferentes|  
|PasswordTooManyRepetitions|Repetições demais|  
|PasswordSequenceFound|Sua senha contém sequências|  
|PagerLabelPageSize|Tamanho da página|  
|CurtainLabelLoading|Carregando...|  
|TablePlaceholderNothingToDisplay|Não há dados para o período e escopo selecionados|  
|ButtonLabelClose|Feche|  
  
###  <a name="Documentation"></a> Documentação  
  
|NOME|Text|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Cabeçalho inválido '{0}'|  
|WebDocumentationInvalidRequestErrorMessage|URL da Solicitação Inválida|  
|TextboxLabelAccessToken|Token de acesso*|  
|DropdownOptionPrimaryKeyFormat|Primário –{0}|  
|DropdownOptionSecondaryKeyFormat|Secundário –{0}|  
|WebDocumentationSubscriptionKeyText|Sua chave de assinatura|  
|WebDocumentationTemplatesAddHeaders|Adicionar cabeçalhos HTTP necessários|  
|WebDocumentationTemplatesBasicAuthSample|Amostra de Autorização Básica|  
|WebDocumentationTemplatesCurlForBasicAuth|para uso de Autorização Básica: --user {nome de usuário}:{senha}|  
|WebDocumentationTemplatesCurlValuesForPath|Especifique valores para parâmetros de caminho (mostrados como {...}), sua chave de assinatura e valores para parâmetros de consulta|  
|WebDocumentationTemplatesDeveloperKey|Especifique sua chave de assinatura|  
|WebDocumentationTemplatesJavaApache|Este exemplo usa o cliente HTTP Apache de Componentes HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Especificar valores para parâmetros opcionais, conforme necessário|  
|WebDocumentationTemplatesPhpPackage|Esta amostra usa o pacote HTTP_Request2. (para mais informações: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Especificar valores para parâmetros de caminho (mostrados como {...}) e o corpo da solicitação se necessário|  
|WebDocumentationTemplatesRequestBody|Especificar corpo da solicitação|  
|WebDocumentationTemplatesRequiredParams|Especificar valores para os seguintes parâmetros obrigatórios|  
|WebDocumentationTemplatesValuesForPath|Especificar valores para parâmetros de caminho (mostrados como {...})|  
|OAuth2AuthorizationEndpointDescription|O ponto de extremidade de autorização é usado para interagir com o proprietário do recurso e obter uma concessão de autorização.|  
|OAuth2AuthorizationEndpointName|Ponto de extremidade de autorização|  
|OAuth2TokenEndpointDescription|O ponto de extremidade de token é usado pelo cliente para obter um token de acesso por meio da apresentação de sua concessão de autorização ou token de atualização.|  
|OAuth2TokenEndpointName|Ponto de extremidade de token|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\>         O cliente inicia o fluxo, direcionando agente do usuário do proprietário do recurso para o ponto de extremidade de autorização.  O cliente inclui seu identificador de cliente, escopo solicitado, estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente do usuário novamente quando o acesso for concedido (ou negado).     </p\>     <p\>        O servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega a solicitação de acesso do cliente.     </p\>     <p\>         Supondo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente do usuário para o cliente usando o URI de redirecionamento fornecido anteriormente (na solicitação ou durante o registro de cliente).  O URI de redirecionamento inclui um código de autorização e qualquer estado local fornecido anteriormente pelo cliente.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\>     Se o usuário negar a solicitação de acesso ou se a solicitação for inválida, o cliente será informado usando os seguintes parâmetros adicionados para o redirecionamento: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Solicitação de autorização|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\>         O aplicativo cliente deve enviar o usuário para o ponto de extremidade de autorização para iniciar o processo de OAuth.          No ponto de extremidade de autorização, o usuário autentica e, em seguida, concede ou nega acesso ao aplicativo.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\>     Supondo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente do usuário para o cliente usando o URI de redirecionamento fornecido anteriormente (na solicitação ou durante o registro de cliente).  O URI de redirecionamento inclui um código de autorização e qualquer estado local fornecido anteriormente pelo cliente. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\>  O cliente solicita um token de acesso do ponto de extremidade do servidor de autorização por meio da inclusão do código de autorização recebido na etapa anterior.  Ao fazer a solicitação, o cliente autentica com o servidor de autorização.  O cliente inclui o URI de redirecionamento usado para obter o código de autorização para verificação. </p\> <p\>     O servidor de autorização autentica o cliente, valida o código de autorização e garante que o URI de redirecionamento recebido corresponda ao URI usado para redirecionar o cliente na etapa (C).  Se for válido, o servidor de autorização responderá com um token de acesso e, opcionalmente, um token de atualização. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\>     Se a autenticação de cliente da solicitação falhar ou for inválida, o servidor de autorização responderá com um código de status HTTP 400 (solicitação incorreta) a menos que especificado de outra forma, além de incluir os parâmetros a seguir com a resposta. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p\>   O cliente faz uma solicitação para o ponto de extremidade de token enviando os seguintes parâmetros usando o formato "application/x-www-form-urlencoded" com uma codificação de caractere UTF-8 no corpo de entidade da solicitação HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\>  O servidor de autorização emite um token de acesso e token de atualização opcional e constrói a resposta adicionando os parâmetros a seguir ao corpo da entidade de resposta HTTP com um código de status 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\>  O cliente autentica com o servidor de autorização e solicita um token de acesso do ponto de extremidade de token. </p\> <p\>  O servidor de autorização autentica o cliente e, se ele é válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>     Se a autenticação de cliente da solicitação falhar ou for inválida, o servidor de autorização responderá com um código de status HTTP 400 (solicitação incorreta) a menos que especificado de outra forma, além de incluir os parâmetros a seguir com a resposta. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>   O cliente faz uma solicitação para o ponto de extremidade de token adicionando os seguintes parâmetros usando o formato "application/x-www-form-urlencoded"     com uma codificação de caractere UTF-8 no corpo de entidade da solicitação HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>  Se a solicitação de token de acesso é válida e autorizada, o servidor de autorização emite um token de acesso e token de atualização opcional e constrói a resposta adicionando os parâmetros a seguir ao corpo da entidade de resposta HTTP com um código de status 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\>   O cliente inicia o fluxo, direcionando agente do usuário do proprietário do recurso para o ponto de extremidade de autorização.  O cliente inclui seu identificador de cliente, escopo solicitado, estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente do usuário novamente quando o acesso for concedido (ou negado). </p\> <p\>        O servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega a solicitação de acesso do cliente. </p\> <p\>        Supondo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente do usuário para o cliente usando o URI de redirecionamento fornecido anteriormente.  O URI de redirecionamento inclui o token de acesso no fragmento de URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\>     Se o proprietário do recurso negar a solicitação de acesso ou se a solicitação falhar por razões diferentes de um URI de redirecionamento ausente ou inválido, o servidor de autorização informará o cliente, adicionando os seguintes parâmetros ao componente de fragmento do URI de redirecionamento usando o formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\>     O aplicativo cliente deve enviar o usuário para o ponto de extremidade de autorização para iniciar o processo de OAuth.      No ponto de extremidade de autorização, o usuário autentica e, em seguida, concede ou nega acesso ao aplicativo. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\>     Se o proprietário do recurso conceder o acesso solicitado, o servidor de autorização emitirá um token de acesso e o entregará ao cliente, adicionando os seguintes parâmetros ao componente de fragmento do URI de redirecionamento usando o formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|O fluxo de código de autorização é otimizado para clientes capazes de manter a confidencialidade de suas credenciais (por exemplo, aplicativos de servidor Web implementados usando o PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concessão de código de autorização|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|O fluxo de credenciais de cliente é adequado nos casos em que o cliente (seu aplicativo) está solicitando acesso aos recursos protegidos sob seu controle. O cliente é considerado como o proprietário do recurso, portanto, nenhuma interação do usuário final é necessária.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concessão de credenciais de cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Fluxo implícito é otimizado para clientes sem capacidade de manter a confidencialidade de suas credenciais e que sabidamente operam um determinado URI de redirecionamento. Esses clientes são normalmente implementados em um navegador usando uma linguagem de script como JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Concessão implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|O fluxo de credenciais de senha de proprietário do recurso é adequado em casos em que o proprietário do recurso tem uma relação de confiança com o cliente (seu aplicativo), assim como o sistema operacional do dispositivo ou um aplicativo de privilégio elevado. Esse fluxo é adequado para clientes capazes de obter as credenciais do proprietário do recurso (nome de usuário e senha, normalmente usando um formulário interativo).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Concessão de credenciais de senha de proprietário do recurso|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\>   O proprietário do recurso fornece ao cliente seu nome de usuário e senha. </p\> <p\> O cliente solicita um token de acesso do ponto de extremidade do servidor de autorização por meio da inclusão das credenciais recebidas do proprietário do recurso.  Ao fazer a solicitação, o cliente autentica com o servidor de autorização. </p\> <p\>     O servidor de autorização autentica o cliente e valida as credenciais do proprietário do recurso e, se elas forem válidas, emite um token de acesso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\>  Se a autenticação de cliente da solicitação falhar ou for inválida, o servidor de autorização responderá com um código de status HTTP 400 (solicitação incorreta) a menos que especificado de outra forma, além de incluir os parâmetros a seguir com a resposta. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p\>    O cliente faz uma solicitação para o ponto de extremidade de token adicionando os seguintes parâmetros usando o formato "application/x-www-form-urlencoded"     com uma codificação de caractere UTF-8 no corpo de entidade da solicitação HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\>   Se a solicitação de token de acesso é válida e autorizada, o servidor de autorização emite um token de acesso e token de atualização opcional e constrói a resposta adicionando os parâmetros a seguir ao corpo da entidade de resposta HTTP com um código de status 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Solicitação de token de acesso|  
|OAuth2Step_AuthorizationRequest_Name|Solicitação de autorização|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|OBRIGATÓRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|OBRIGATÓRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|OBRIGATÓRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OBRIGATÓRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|OBRIGATÓRIO. Identificador de cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO se o cliente não estiver se autenticando com o servidor de autorização.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|OBRIGATÓRIO. O identificador de cliente.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|OBRIGATÓRIO. O código de autorização gerado pelo servidor de autorização.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO. O código de autorização recebido do servidor de autorização.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Texto ASCII legível fornecendo informações adicionais.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Texto ASCII legível fornecendo informações adicionais.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto ASCII legível fornecendo informações adicionais.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Texto ASCII legível fornecendo informações adicionais.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto ASCII legível fornecendo informações adicionais.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|OBRIGATÓRIO. Um único código de erro ASCII entre os seguintes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error e temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|OBRIGATÓRIO. Um único código de erro ASCII entre os seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|OBRIGATÓRIO. Um único código de erro ASCII entre os seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|OBRIGATÓRIO. Um único código de erro ASCII entre os seguintes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error e temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OBRIGATÓRIO. Um único código de erro ASCII entre os seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMENDADO. O tempo de vida, em segundos, do token de acesso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMENDADO. O tempo de vida, em segundos, do token de acesso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMENDADO. O tempo de vida, em segundos, do token de acesso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMENDADO. O tempo de vida, em segundos, do token de acesso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO. O valor DEVE ser definido como "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|OBRIGATÓRIO. O valor DEVE ser definido como "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBRIGATÓRIO. O valor DEVE ser definido como "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBRIGATÓRIO. A senha de proprietário do recurso.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O URI do ponto de extremidade de redirecionamento deve ser um URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO se o parâmetro "redirect_uri" foi incluído na solicitação de autorização e seus valores DEVEM ser idênticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCIONAL. O URI do ponto de extremidade de redirecionamento deve ser um URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|OBRIGATÓRIO. O valor DEVE ser definido como "code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|OBRIGATÓRIO. O valor DEVE ser definido como "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O escopo de solicitação de acesso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL se idêntico ao escopo solicitado pelo cliente; caso contrário, é OBRIGATÓRIO.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCIONAL. O escopo de solicitação de acesso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL se idêntico ao escopo solicitado pelo cliente; caso contrário, é OBRIGATÓRIO.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCIONAL. O escopo de solicitação de acesso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL se idêntico ao escopo solicitado pelo cliente; caso contrário, é OBRIGATÓRIO.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCIONAL. O escopo de solicitação de acesso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL se idêntico ao escopo solicitado pelo cliente; caso contrário, é OBRIGATÓRIO.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|OBRIGATÓRIO se o parâmetro "state" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco usado pelo cliente para manter o estado entre a solicitação e o retorno de chamada.  O servidor de autorização inclui esse valor ao redirecionar o agente do usuário de volta para o cliente.  O parâmetro DEVE ser usado para impedir a falsificação de solicitação entre sites.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|OBRIGATÓRIO se o parâmetro "state" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|OBRIGATÓRIO se o parâmetro "state" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco usado pelo cliente para manter o estado entre a solicitação e o retorno de chamada.  O servidor de autorização inclui esse valor ao redirecionar o agente do usuário de volta para o cliente.  O parâmetro DEVE ser usado para impedir a falsificação de solicitação entre sites.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|OBRIGATÓRIO se o parâmetro "state" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|OBRIGATÓRIO. O tipo do token emitido.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|OBRIGATÓRIO. O tipo do token emitido.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|OBRIGATÓRIO. O tipo do token emitido.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OBRIGATÓRIO. O tipo do token emitido.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OBRIGATÓRIO. O nome de usuário do proprietário do recurso.|  
|OAuth2UnsupportedTokenType|Não há suporte para o tipo de token '{0}'.|  
|OAuth2InvalidState|Resposta inválida do servidor de autorização|  
|OAuth2GrantType_AuthorizationCode|Código de autorização|  
|OAuth2GrantType_Implicit|Implícito|  
|OAuth2GrantType_ClientCredentials|Credenciais do cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Senha de proprietário do recurso|  
|WebDocumentation302Code|302 Encontrado|  
|WebDocumentation400Code|400 (Solicitação incorreta)|  
|OAuth2SendingMethod_AuthHeader|Cabeçalho de autorização|  
|OAuth2SendingMethod_QueryParam|Parâmetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Ocorreu um erro ao autorizar o acesso via {0}|  
|OAuth2AuthorizationServerCommunicationException|Não foi possível estabelecer uma conexão HTTP com o servidor de autorização ou ele foi fechado inesperadamente.|  
|WebDocumentationOAuth2GeneralErrorMessage|Ocorreu um erro inesperado.|  
|AuthorizationServerCommunicationException|Ocorreu uma exceção de comunicação do servidor de autorização. Entre em contato com o administrador.|  
|TextblockSubscriptionKeyHeaderDescription|Chave de assinatura que fornece acesso a esta API. Encontrada em seu <a href='/developer'\>Perfil</a\>.|  
|TextblockOAuthHeaderDescription|O token de acesso OAuth 2.0 obtido de < eu\>{0}</i\>. Tipos de concessão com suporte: < i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Tipo de mídia do corpo enviado para a API.|  
|ErrorMessageApiNotAccessible|A API que você está tentando chamar não está acessível no momento. Contate o distribuidor da API <a href="/issues"\>aqui</a\>.|  
|ErrorMessageApiTimedout|A API que você está tentando chamar está demorando mais que o normal obter uma resposta. Contate o distribuidor da API <a href="/issues"\>aqui</a\>.|  
|BadRequestParameterExpected|"'{0}' é o parâmetro esperado"|  
|TooltipTextDoubleClickToSelectAll|Clique duas vezes para selecionar tudo.|  
|TooltipTextHideRevealSecret|Mostrar/Ocultar|  
|ButtonLinkOpenConsole|Experimentar|  
|SectionHeadingRequestBody|Corpo da solicitação|  
|SectionHeadingRequestParameters|Parâmetros da solicitação|  
|SectionHeadingRequestUrl|URL de Solicitação|  
|SectionHeadingResponse|Response|  
|SectionHeadingRequestHeaders|Cabeçalhos da solicitação|  
|FormLabelSubtextOptional|opcional|  
|SectionHeadingCodeSamples|Exemplos de código|  
|TextblockOpenidConnectHeaderDescription|Token de id de OpenID Connect obtido de < eu\>{0}</i\>. Tipos de concessão com suporte: < i\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|NOME|Text|  
|----------|----------|  
|LinkLabelBack|voltar|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Envie-nos um email|  
|PageTitleError|Ocorreu um problema ao apresentar a página solicitada|  
|TextblockPotentialCauseIntermittentIssue|Isso pode ser um problema de acesso a dados intermitente que já não existe mais.|  
|TextblockPotentialCauseOldLink|O link em que você clicou em pode ser antigo e não aponta mais para o local correto.|  
|TextblockPotentialCauseTechnicalProblem|Pode haver um problema técnico em nossa extremidade.|  
|TextblockPotentialSolutionRefresh|Tente atualizar a página.|  
|TextblockPotentialSolutionStartOver|Começar de novo a partir de {0}.|  
|TextblockPotentialSolutionTryAgain|Vá {0} e tente a ação que você realizou novamente.|  
|TextReportProblem|{0} descrevendo o que deu errado e vamos analisá-lo assim que pudermos.|  
|TitlePotentialCause|Possível causa|  
|TitlePotentialSolution|Possivelmente é apenas um problema temporário, algumas opções para tentar|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|NOME|Text|  
|----------|----------|  
|WebIssuesIndexTitle|Problemas|  
|WebIssuesNoActiveSubscriptions|Você não tem nenhuma assinatura ativa. Você precisa assinar um produto para relatar um problema.|  
|WebIssuesNotSignin|Você não está conectado. Por favor {0} para relatar um problema ou postar um comentário.|  
|WebIssuesReportIssueButton|Relatar Problema|  
|WebIssuesSignIn|entrar|  
|WebIssuesStatusReportedBy|Status: {0} &#124; relatado por {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|NOME|Text|  
|----------|----------|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|envie-nos um email|  
|PageTitleNotFound|Infelizmente não foi possível localizar a página que você está procurando|  
|TextblockPotentialCauseMisspelledUrl|Se você digitou a URL, talvez tenha feito isso de maneira incorreta.|  
|TextblockPotentialCauseOldLink|O link em que você clicou em pode ser antigo e não aponta mais para o local correto.|  
|TextblockPotentialSolutionRetype|Tente digitar novamente a URL.|  
|TextblockPotentialSolutionStartOver|Começar de novo a partir de {0}.|  
|TextReportProblem|{0} descrevendo o que deu errado e vamos analisá-lo assim que pudermos.|  
|TitlePotentialCause|Possível causa|  
|TitlePotentialSolution|Solução em potencial|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|NOME|Text|  
|----------|----------|  
|WebProductsAgreement|Ao assinar o {0} Produto, concordo com o `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Termos de Uso|  
|WebProductsSubscribeButton|Assinar|  
|WebProductsUsageLimitsHeader|Limites de uso|  
|WebProductsYouAreNotSubscribed|Você assinou este produto.|  
|WebProductsYouRequestedSubscription|Você solicitou a assinatura para este produto.|  
|ErrorYouNeedToAgreeWithLegalTerms|Você deve concordar com os Termos de Uso antes de continuar.|  
|ButtonLabelAddSubscription|Adicionar assinatura|  
|LinkLabelChangeSubscriptionName|alterar|  
|ButtonLabelConfirm|Confirmar|  
|TextblockMultipleSubscriptionsCount|Você tem {0} assinaturas para este produto:|  
|TextblockSingleSubscriptionsCount|Você tem {0} assinatura para este produto:|  
|TextblockSingleApisCount|Este produto contém {0} API:|  
|TextblockMultipleApisCount|Este produto contém {0} APIs:|  
|TextblockHeaderSubscribe|Assinar o produto|  
|TextblockSubscriptionDescription|Uma nova assinatura será criada da seguinte maneira:|  
|TextblockSubscriptionLimitReached|Limite de assinaturas atingido.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|NOME|Text|  
|----------|----------|  
|PageTitleProducts|Produtos|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|NOME|Text|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|A entrada está desabilitada pelos administradores no momento.|  
|TextboxExternalIdentitiesSigninInvitation|Como alternativa, entre com|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Entre com:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|NOME|Text|  
|----------|----------|  
|PrincipalNotFound|A entidade de segurança não foi encontrada ou a assinatura é inválida|  
|ErrorSsoAuthenticationFailed|Falha na autenticação de SSO|  
|ErrorSsoAuthenticationFailedDetailed|Não é possível verificar a assinatura ou token inválido fornecido.|  
|ErrorSsoTokenInvalid|O token de SSO é inválido|  
|ValidationErrorSpecificEmailAlreadyExists|Email '{0}' já registrado|  
|ValidationErrorSpecificEmailInvalid|Email '{0}' é inválido|  
|ValidationErrorPasswordInvalid|A senha é inválida. Corrija os erros e tente novamente.|  
|PropertyTooShort|{0} é muito curto|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Endereço de email inválido.|  
|ValidationMessageNewPasswordConfirmationRequired|Confirmar nova senha|  
|ValidationErrorPasswordConfirmationRequired|Confirme a senha está vazio|  
|WebAuthenticationEmailChangeNotice|O e-mail de confirmação de alteração está a caminho de {0}. Siga as instruções nele para confirmar seu novo endereço de email. Se o email não chegar em sua caixa de entrada dentro de alguns minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationEmailChangeNoticeHeader|Sua solicitação de alteração de email foi processada com êxito|  
|WebAuthenticationEmailChangeNoticeTitle|Alteração de email solicitada|  
|WebAuthenticationEmailHasBeenRevertedNotice|O seu email já existe. A solicitação foi revertida|  
|ValidationErrorEmailAlreadyExists|O email já existe|  
|ValidationErrorEmailInvalid|Endereço de email inválido|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|O email é obrigatório.|  
|WebAuthenticationErrorNoticeHeader|Erro|  
|WebAuthenticationFieldLengthErrorMessage|{0} deve ser um comprimento máximo de {1}|  
|TextboxLabelEmailFirstName|Nome|  
|ValidationErrorFirstNameRequired|O nome é obrigatório.|  
|ValidationErrorFirstNameInvalid|Nome inválido|  
|NoticeInvalidInvitationToken|Observe que os links de confirmação são válidos por apenas 48 horas. Se você estiver ainda dentro deste período, certifique-se de que o link está correto. Se o link tiver expirado, repita a ação que você está tentando confirmar.|  
|NoticeHeaderInvalidInvitationToken|Token de convite inválido|  
|NoticeTitleInvalidInvitationToken|Erro de confirmação|  
|WebAuthenticationLastNameInvalidErrorMessage|Sobrenome inválido|  
|TextboxLabelEmailLastName|Sobrenome|  
|ValidationErrorLastNameRequired|O sobrenome é obrigatório.|  
|WebAuthenticationLinkExpiredNotice|O link de confirmação enviado a você expirou. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Seu link de redefinição de senha é inválido ou expirou.|  
|WebAuthenticationLinkExpiredNoticeTitle|Link enviado|  
|WebAuthenticationNewPasswordLabel|Nova senha|  
|ValidationMessageNewPasswordRequired|A nova senha é obrigatória.|  
|TextboxLabelNotificationsSenderEmail|Email do remetente das notificações|  
|TextboxLabelOrganizationName|Nome da Organização|  
|WebAuthenticationOrganizationRequiredErrorMessage|O nome da organização está vazio|  
|WebAuthenticationPasswordChangedNotice|Sua senha foi atualizada com êxito|  
|WebAuthenticationPasswordChangedNoticeTitle|Senha atualizada|  
|WebAuthenticationPasswordCompareErrorMessage|As senhas não correspondem|  
|WebAuthenticationPasswordConfirmLabel|Confirmar senha|  
|ValidationErrorPasswordInvalidDetailed|A senha é muito fraca.|  
|WebAuthenticationPasswordLabel|Senha|  
|ValidationErrorPasswordRequired|A senha é obrigatória.|  
|WebAuthenticationPasswordResetSendNotice|O e-mail de confirmação de alteração de senha está sendo enviado para {0}. Siga as instruções no email para continuar com o seu processo de alteração de senha.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Sua solicitação de redefinição de senha foi processada com êxito|  
|WebAuthenticationPasswordResetSendNoticeTitle|Redefinição de senha solicitada|  
|WebAuthenticationRequestNotFoundNotice|Solicitação não encontrada|  
|WebAuthenticationSenderEmailRequiredErrorMessage|O email do remetente de notificações está vazio|  
|WebAuthenticationSigninPasswordLabel|Confirme a alteração inserindo uma senha|  
|WebAuthenticationSignupConfirmNotice|O e-mail de confirmação do registro foi enviado a {0}<br /\> Siga as instruções no e-mail para ativar sua conta.<br /\> Se o e-mail não chegar em sua caixa de entrada dentro de alguns minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationSignupConfirmNoticeHeader|Sua conta foi criada com êxito|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|O email de confirmação do registro foi enviado novamente|  
|WebAuthenticationSignupConfirmNoticeTitle|Conta criada|  
|WebAuthenticationTokenRequiredErrorMessage|O token está vazio|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que um usuário com este email já está registrado no sistema. Se você esquecer sua senha, tente restaurá-la ou entrar em contato com a nossa equipe de suporte.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Usuário já registrado|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Já registrado|  
|ButtonLabelChangePassword|Alterar senha|  
|ButtonLabelChangeAccountInfo|Alterar informações da conta|  
|ButtonLabelCloseAccount|Fechar conta|  
|WebAuthenticationInvalidCaptchaErrorMessage|O texto digitado não corresponde ao texto na imagem. Tente novamente.|  
|ValidationErrorCredentialsInvalid|Email ou senha é inválida. Corrija os erros e tente novamente.|  
|WebAuthenticationRequestIsNotValid|A solicitação não é válida|  
|WebAuthenticationUserIsNotConfirm|Confirme o seu registro antes de tentar entrar.|  
|WebAuthenticationInvalidEmailFormated|O e-mail é inválido: {0}|  
|WebAuthenticationUserNotFound|Usuário não encontrado|  
|WebAuthenticationTenantNotRegistered|Sua conta pertence a um locatário do Azure Active Directory que não está autorizado a acessar este portal.|  
|WebAuthenticationAuthenticationFailed|Falha na autenticação.|  
|WebAuthenticationGooglePlusNotEnabled|Falha na autenticação. Se você autorizou o aplicativo, entre em contato com o administrador para certificar-se de que a autenticação do Google está configurada corretamente.|  
|ValidationErrorAllowedTenantIsRequired|Locatário Permitido é obrigatório|  
|ValidationErrorTenantIsNotValid|O locatário '{0}' do Azure Active Directory Domain Services não é válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Faça logon usando sua conta {0}|  
|WebAuthenticationUserLimitNotice|Este serviço atingiu o número máximo de usuários permitidos. `<a href="mailto:{0}"\>contact the administrator</a\>` para atualizar o serviço deles e habilitar novamente o registro de usuário.|  
|WebAuthenticationUserLimitNoticeHeader|Registro de usuário desabilitado|  
|WebAuthenticationUserLimitNoticeTitle|Registro de usuário desabilitado|  
|WebAuthenticationUserRegistrationDisabledNotice|O registro de usuários foi desabilitado pelo administrador. Faça logon com o provedor de identidade externo.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registro de usuário desabilitado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registro de usuário desabilitado|  
|WebAuthenticationSignupPendingConfirmationNotice|Antes que seja possível concluir a criação da sua conta, precisamos verificar seu endereço de email. Um e-mail foi enviado para {0}. Siga as instruções no email para ativar sua conta. Se o email não chegar dentro de alguns minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Encontramos uma conta não confirmada para o endereço de email {0}. Para concluir a criação da sua conta, precisamos verificar seu endereço de email. Um e-mail foi enviado para {0}. Siga as instruções no email para ativar sua conta. Se o email não chegar dentro de alguns minutos, verifique sua pasta de lixo eletrônico|  
|WebAuthenticationSignupConfirmationAlmostDone|Já está quase pronto!|  
|WebAuthenticationSignupConfirmationEmailSent|Um e-mail foi enviado para {0}. Siga as instruções no email para ativar sua conta. Se o email não chegar dentro de alguns minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail enviado com êxito para {0}|  
|WebAuthenticationNoAadTenantConfigured|Nenhum locatário do Azure Active Directory configurado para o serviço.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Eu concordo com o `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Examine `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Termos de Uso|  
|ValidationMessageConsentNotAccepted|Você deve concordar com os Termos de Uso antes de continuar.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|NOME|Text|  
|----------|----------|  
|WebAuthenticationForgotPassword|Esqueceu sua senha?|  
|WebAuthenticationIfAdministrator|Se você é um administrador, você deve entrar em `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Você ainda não é membro? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Lembrar-me neste computador|  
|WebAuthenticationSigininWithPassword|Entrar com seu nome de usuário e senha|  
|WebAuthenticationSigninTitle|Entrar|  
|WebAuthenticationSignUpNow|Inscreva-se agora mesmo|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|NOME|Text|  
|----------|----------|  
|PageTitleSignup|Inscrição|  
|WebAuthenticationAlreadyAMember|Já é membro?|  
|WebAuthenticationCreateNewAccount|Criar uma nova conta de Gerenciamento de API|  
|WebAuthenticationSigninNow|Entrar agora|  
|ButtonLabelSignup|Inscrição|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|NOME|Text|  
|----------|----------|  
|SubscriptionCancelConfirmation|Tem certeza de que deseja cancelar esta assinatura?|  
|SubscriptionRenewConfirmation|Tem certeza de que deseja renovar esta assinatura?|  
|WebDevelopersManageSubscriptions|Gerenciar Assinaturas|  
|WebDevelopersPrimaryKey|Chave primária|  
|WebDevelopersRegenerateLink|Regenerar|  
|WebDevelopersSecondaryKey|Chave secundária|  
|ButtonLabelShowKey|Mostrar|  
|ButtonLabelRenewSubscription|Renew|  
|WebDevelopersSubscriptionRequested|Solicitado em {0}|  
|WebDevelopersSubscriptionRequestedState|Solicitado|  
|WebDevelopersSubscriptionTableNameHeader|NOME|  
|WebDevelopersSubscriptionTableStateHeader|Estado|  
|WebDevelopersUsageStatisticsLink|Relatórios de análise|  
|WebDevelopersYourSubscriptions|Suas assinaturas|  
|SubscriptionPropertyLabelRequestedDate|Solicitado em|  
|SubscriptionPropertyLabelStartedDate|Iniciado em|  
|PageTitleRenameSubscription|Renomear assinatura|  
|SubscriptionPropertyLabelName|Nome da assinatura|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|NOME|Text|  
|----------|----------|  
|SectionHeadingCloseAccount|Deseja para fechar sua conta?|  
|PageTitleDeveloperProfile|Perfil|  
|ButtonLabelHideKey|Ocultar|  
|ButtonLabelRegenerateKey|Regenerar|  
|InformationMessageKeyWasRegenerated|Tem a certeza de que deseja regenerar esta chave?|  
|ButtonLabelShowKey|Mostrar|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|NOME|Text|  
|----------|----------|  
|ButtonLabelUpdateProfile|Atualizar perfil|  
|PageTitleUpdateProfile|Atualizar informações da conta|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|NOME|Text|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Alterar informações da conta|  
|ButtonLabelChangePassword|Alterar senha|  
|ButtonLabelCloseAccount|Fechar conta|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Nome|  
|TextboxLabelEmailLastName|Sobrenome|  
|TextboxLabelNotificationsSenderEmail|Email do remetente das notificações|  
|TextboxLabelOrganizationName|Nome da Organização|  
|SubscriptionStateActive|Ativo|  
|SubscriptionStateCancelled|Cancelado|  
|SubscriptionStateExpired|Expirado|  
|SubscriptionStateRejected|Rejeitado|  
|SubscriptionStateRequested|Solicitado|  
|SubscriptionStateSuspended|Suspenso|  
|DefaultSubscriptionNameTemplate|{0} (padrão)|  
|SubscriptionNameTemplate|Acesso do desenvolvedor #{0}|  
|TextboxLabelSubscriptionName|Nome da assinatura|  
|ValidationMessageSubscriptionNameRequired|Não é possível deixar em branco o nome da assinatura.|  
|ApiManagementUserLimitReached|Este serviço atingiu o número máximo de usuários permitidos. Atualize para um tipo de preço mais alto.|  
  
##  <a name="glyphs"></a> Recursos de glifo  
 Modelos de portal do desenvolvedor do Gerenciamento de API podem usar os glifos do [Glyphicons do Bootstrap](https://getbootstrap.com/components/#glyphicons). Este conjunto de glifos inclui mais de 250 glifos no formato de fonte do conjunto [Glyphicon](https://glyphicons.com/) Halflings. Para usar um glifo desse conjunto, use a sintaxe a seguir.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obter a lista completa de glifos, consulte [Glyphicons do Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como trabalhar com modelos, consulte [Como personalizar o portal de desenvolvedor de Gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
