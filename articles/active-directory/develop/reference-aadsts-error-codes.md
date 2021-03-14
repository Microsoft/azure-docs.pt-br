---
title: Autenticação do Azure AD & códigos de erro de autorização
description: Saiba mais sobre os códigos de erro do AADSTS que são retornados do serviço de token de segurança (STS) do Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 02/01/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b82d3963ed12e0d5dc6acd75555a3a7e8f20eeb0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175338"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Códigos de erro de autorização e autenticação do Azure AD

Procurando informações sobre os códigos de erro AADSTS que são retornados do serviço de token de segurança (STS) do Azure Active Directory (Azure AD)? Leia este documento para encontrar descrições de erros, correções e algumas sugestões sugeridas do AADSTS.

> [!NOTE]
> Esta informação é preliminar e está sujeita a alterações. Tem alguma dúvida ou não consegue encontrar o que está procurando? Crie um problema no GitHub ou consulte [Suporte e opções de ajuda para desenvolvedores](./developer-support-help-options.md) para aprender sobre outras maneiras de obter ajuda e suporte.
>
> Esta documentação é fornecida para diretrizes de desenvolvedor e administrador, mas nunca deve ser usada pelo próprio cliente. Os códigos de erro estão sujeitos a alterações a qualquer momento para fornecer mensagens de erro mais granulares que se destinam a ajudar o desenvolvedor durante a criação de seu aplicativo. Os aplicativos que usam uma dependência de texto ou números de código de erro serão quebrados ao longo do tempo.

## <a name="handling-error-codes-in-your-application"></a>Manipulando códigos de erro em seu aplicativo

A [especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-5.2) fornece orientação sobre como lidar com erros durante a autenticação usando a `error` parte da resposta de erro. 

Aqui está um exemplo de resposta de erro:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parâmetro         | Descrição    |
|-------------------|----------------|
| `error`       | Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e deve ser usada para reagir a erros. |
| `error_description` | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. Nunca use este campo para reagir a um erro em seu código. |
| `error_codes` | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico.  |
| `timestamp`   | A hora na qual o erro ocorreu. |
| `trace_id`    | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| `correlation_id` | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |
| `error_uri` |  Um link para a página de pesquisa de erro com informações adicionais sobre o erro.  Isso é apenas para uso do desenvolvedor, não a apresenta para os usuários.  Presente somente quando o sistema de pesquisa de erros tiver informações adicionais sobre o erro – nem todos os erros têm informações adicionais fornecidas.|

O `error` campo tem vários valores possíveis: examine os links de documentação do protocolo e as especificações do OAuth 2,0 para saber mais sobre erros específicos (por exemplo, `authorization_pending` no [fluxo de código do dispositivo](v2-oauth2-device-code.md)) e como reagir a eles.  Alguns outros comuns são listados aqui:

| Código do Erro         | Descrição        | Ação do Cliente    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Erro de protocolo, como um parâmetro obrigatório ausente. | Corrija e reenvie a solicitação.|
| `invalid_grant`    | Parte do material de autenticação (código de autenticação, token de atualização, token de acesso, desafio PKCE) era inválido, não analisável, ausente ou, de outra forma, inutilizável | Tente uma nova solicitação para o `/authorize` ponto de extremidade para obter um novo código de autorização.  Considere revisar e validar o uso do aplicativo dos protocolos. |
| `unauthorized_client` | O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. | Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure Active Directory ou não é adicionado ao locatário do Azure Active Directory do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| `invalid_client` | Falha na autenticação de cliente.  | As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais.   |
| `unsupported_grant_type` | O servidor de autorização não dá suporte ao tipo de concessão de autorização. | Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| `invalid_resource` | O recurso de destino é inválido porque não existe, o Azure Active Directory não consegue encontrá-lo ou ele não está configurado corretamente. | Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD.  Durante o desenvolvimento, isso geralmente indica um locatário de teste de instalação incorreta ou um erro de digitação no nome do escopo que está sendo solicitado. |
| `interaction_required` | A solicitação requer interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicional. | Repita a solicitação com o mesmo recurso, interativamente, para que o usuário possa concluir os desafios necessários.  |
| `temporarily_unavailable` | O servidor está temporariamente muito ocupado para tratar da solicitação. | Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |

## <a name="lookup-current-error-code-information"></a>Pesquisar informações do código de erro atual
Os códigos de erro e as mensagens estão sujeitos a alterações.  Para obter as informações mais atuais, dê uma olhada na [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) página para encontrar descrições de erro AADSTS, correções e algumas soluções alternativas sugeridas.  

Por exemplo, se você recebeu o código de erro "AADSTS50058", faça uma pesquisa em [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) para "50058".  Você também pode vincular diretamente a um erro específico adicionando o número de código de erro à URL: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>Códigos de erro AADSTS

| Erro | Descrição |
|---|---|
| AADSTS16000 | SelectUserAccount - Esta é uma interrupção lançada pelo Azure AD, que resulta na interface do usuário que permite ao usuário selecionar entre várias sessões válidas do SSO. Esse erro é bastante comum e pode ser retornado ao aplicativo se `prompt=none` for especificado. |
| AADSTS16001 | UserAccountSelectionInvalid - Você verá esse erro se o usuário clicar em um bloco que a lógica de seleção da sessão rejeitou. Quando acionado, esse erro permite que o usuário recupere escolhendo de uma lista atualizada de blocos / sessões ou escolhendo outra conta. Este erro pode ocorrer devido a um defeito de código ou condição de corrida. |
| AADSTS16002 | AppSessionSelectionInvalid - O requisito de SID especificado pelo aplicativo não foi atendido.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant - indica que o usuário não foi explicitamente adicionado ao inquilino. |
| AADSTS17003 | CredentialKeyProvisioningFailed - O Azure Active Directory não pode provisionar a chave do usuário. |
| AADSTS20001 | WsFedSignInResponseError - Há um problema com seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS20012 | WsFedMessageInvalid - Há um problema com seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS20033 | FedMetadataInvalidTenantName - Há um problema com seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError - há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError - há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - Existe um problema com o seu provedor de identidade federado. Entre em contato com seu IDP para resolver esse problema. |
| AADSTS50000 | TokenIssuanceError - há um problema com o serviço de logon. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para resolver esse problema. |
| AADSTS50001 | InvalidResource - O recurso está desabilitado ou não existe. Verifique o código do seu aplicativo para garantir que você especificou o URL exato do recurso que você está tentando acessar.  |
| AADSTS50002 | NotAllowedTenant - O login falhou devido a um acesso de proxy restrito no locatário. Se essa for a própria política de locatário, você poderá alterar suas configurações de locatário restrito para corrigir esse problema. |
| AADSTS500021 | Acesso negado ao locatário ' {Tenant} '. AADSTS500021 indica que o recurso de restrição de locatário está configurado e que o usuário está tentando acessar um locatário que não está na lista de locatários permitidos especificados no cabeçalho `Restrict-Access-To-Tenant` . Para obter mais informações, consulte [usar restrições de locatário para gerenciar o acesso a aplicativos de nuvem SaaS](../manage-apps/tenant-restrictions.md).|
| AADSTS50003 | MissingSigningKey - O logon falhou devido a uma chave de assinatura ou certificado ausente. Isso pode ter ocorrido porque não havia uma chave de assinatura configurada no aplicativo. Confira as resoluções descritas em [.. /Manage-apps/application-Sign-in-problem-Federated-SSO-Gallery.MD # certificado-ou-chave não configurado](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#certificate-or-key-not-configured). Se você ainda encontrar problemas, entre em contato com o proprietário do aplicativo ou com um administrador do aplicativo. |
| AADSTS50005 | DevicePolicyError-o usuário tentou fazer logon em um dispositivo de uma plataforma que atualmente não tem suporte por meio da política de acesso condicional. |
| AADSTS50006 | InvalidSignature - Falha na verificação da assinatura devido a uma assinatura inválida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing - O certificado de criptografia de parceiro não foi encontrado para este aplicativo. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a Microsoft para obter esse fixo. |
| AADSTS50008 | InvalidSamlToken - A asserção SAML está ausente ou está mal configurada no token. Entre em contato com seu provedor de federação. |
| AADSTS50010 | AudienceUriValidationFailed - A validação do URI de público para o aplicativo falhou, pois nenhum público-alvo de token foi configurado. |
| AADSTS50011 | InvalidReplyTo - O endereço de resposta está ausente, está mal configurado ou não corresponde aos endereços de resposta configurados para o aplicativo.  Como uma resolução, certifique-se de adicionar esse endereço de resposta ausente ao aplicativo Azure Active Directory ou a alguém com as permissões para gerenciar seu aplicativo no Active Directory fazer isso para você.|
| AADSTS50012 | AuthenticationFailed - A autenticação falhou por um dos seguintes motivos:<ul><li>O nome da entidade do certificado de assinatura não está autorizado</li><li>Uma política de autoridade confiável correspondente não foi encontrada para o nome do assunto autorizado</li><li>A cadeia de certificados não é válida</li><li>O certificado de assinatura não é válido</li><li>A política não está configurada no inquilino</li><li>A impressão digital do certificado de assinatura não é autorizada</li><li>A declaração do cliente contém uma assinatura inválida</li></ul> |
| AADSTS50013 | InvalidAssertion - A declaração é inválida devido a várias razões - O emissor do token não corresponde à versão da API dentro de seu intervalo de tempo válido - expirado - mal formado - O token de atualização na asserção não é um token de atualização principal. |
| AADSTS50014 | GuestUserInPendingState - O resgate do usuário está em um estado pendente. A conta de usuário convidado ainda não está totalmente criada. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState - O usuário requer o consentimento legal da faixa etária. |
| AADSTS50017 | Falha na validação do certificado - Falha na validação do certificado, pelos seguintes motivos:<ul><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Não é possível localizar o CrlSegment esperado</li><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Ponto de distribuição de CRL delta é configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível recuperar segmentos de CRL válidos devido a um problema de tempo limite</li><li>Não é possível baixar o CRL</li></ul>Entre em contato com o administrador de locatário. |
| AADSTS50020 | UserUnauthorized - Os usuários não são autorizados a chamar este endpoint. |
| AADSTS50027 | InvalidJwtToken - Token JWT inválido devido aos seguintes motivos:<ul><li>não contém declaração nonce, subdeclaração</li><li>incompatibilidade do identificador do assunto</li><li>declaração duplicada nas declarações de idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro de seu intervalo de tempo válido </li><li>o formato do token não é apropriado</li><li>falha na verificação de assinatura do token de ID externa do emissor.</li></ul> |
| AADSTS50029 | URI inválida – o nome de domínio contém caracteres inválidos. Entre em contato com o administrador de locatário. |
| AADSTS50032 | WeakRsaKey - Indica a tentativa incorreta do usuário de usar uma chave RSA fraca. |
| AADSTS50033 | RetryableError - Indica um erro temporário não relacionado às operações do banco de dados. |
| AADSTS50034 | UserAccountNotFound - Para entrar neste aplicativo, a conta deve ser adicionada ao diretório. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-o Salt necessário para gerar um identificador de par está ausente em princípio. Entre em contato com o administrador de locatário. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - incompatibilidades de assunto emissor de declaração na asserção de cliente. Entre em contato com o administrador de locatário. |
| AADSTS50049 | NoSuchInstanceForDiscovery - instância desconhecida ou inválida. |
| AADSTS50050 | MalformedDiscoveryRequest - A solicitação está malformada. |
| AADSTS50053 | IdsLocked - A conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta. |
| AADSTS50055 | InvalidPasswordExpiredPassword - A senha expirou. |
| AADSTS50056 | Senha inválida ou nula - A senha não existe na loja para esse usuário. |
| AADSTS50057 | UserDisabled - A conta de usuário está desativada. A conta foi desabilitada por um administrador. |
| AADSTS50058 | UserInformationNotProvided - Isso significa que um usuário não está conectado. Esse é um erro comum que é esperado quando um usuário não é autenticado e ainda não fez login.</br>Se esse erro for incentivado em um contexto de SSO em que o usuário fez login anteriormente, isso significa que a sessão de SSO não foi encontrada ou é inválida.</br>Esse erro pode ser retornado para o aplicativo se prompt = none for especificado. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - Informações de identificação de inquilino não foram encontradas na solicitação ou implícitas por nenhuma credencial fornecida. O usuário pode entrar em contato com o administrador do locatário para ajudar a resolver o problema. |
| AADSTS50061 | SignoutInvalidRequest - A solicitação de saída é inválida. |
| AADSTS50064 | CredentialAuthenticationError: a validação de credenciais quanto ao nome de usuário ou senha falhou. |
| AADSTS50068 | SignoutInitiatorNotParticipant-falha ao sair. O aplicativo que iniciou a saída não é um participante da sessão atual. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-falha ao sair. A solicitação de saída especificou um identificador de nome que não correspondeu às sessões existentes. |
| AADSTS50071 | SignoutMessageExpired - A solicitação de logout expirou. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - O usuário precisa se inscrever para autenticação de segundo fator (interativo). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - É necessária uma autenticação forte e o usuário não passou no desafio do MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - Devido a uma alteração de configuração feita pelo administrador, ou porque você foi movido para um novo local, o usuário deve usar a autenticação de vários fatores para acessar o recurso. Tente novamente com uma nova solicitação de autorização para o recurso. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - Devido a uma alteração de configuração feita pelo administrador ou porque o usuário foi movido para um novo local, o usuário é solicitado a usar a autenticação multifator. |
| AADSTS50085 | O token de atualização precisa de logon do IDP social. Faça com que o usuário tente entrar novamente com o nome de usuário-senha |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError - O serviço está temporariamente indisponível. Tente novamente. |
| AADSTS50089 | O token de fluxo expirou – Falha na autenticação. Peça que o usuário tente fazer login novamente com username -password. |
| AADSTS50097 | DeviceAuthenticationRequired - A autenticação do dispositivo é necessária. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - A assinatura do JWT é inválida. |
| AADSTS50105 | EntitlementGrantsNotFound - O usuário conectado não está atribuído a uma função para o aplicativo conectado. Assign the user  to the app. Para obter mais informações:[.. /Manage-apps/application-Sign-in-problem-Federated-SSO-Gallery.MD # usuário-não atribuído-a-função](../manage-apps/application-sign-in-problem-federated-sso-gallery.md#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri - O objeto de região de federação solicitado não existe. Entre em contato com o administrador de locatário. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - problema com cabeçalho JWT. Entre em contato com o administrador de locatário. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - Claims Transformation contém um parâmetro de entrada inválido. Entre em contato com o administrador de locatário para atualizar a política. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - O login foi interrompido devido a uma redefinição de senha ou entrada de registro de senha. |
| AADSTS50126 | InvalidUserNameOrPassword - Erro ao validar credenciais devido a nome de usuário ou senha inválidos. |
| AADSTS50127 | BrokerAppNotInstalled - O usuário precisa instalar um aplicativo do broker para obter acesso a esse conteúdo. |
| AADSTS50128 | Nome de domínio inválido - Nenhuma informação de identificação de inquilino encontrada na solicitação ou implícita por qualquer credencial fornecida. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined - A junção no local de trabalho é necessária para registrar o dispositivo. |
| AADSTS50131 | ConditionalAccessFailed-indica vários erros de acesso condicional, como estado de dispositivo defeituoso do Windows, solicitação bloqueada devido a atividades suspeitas, política de acesso ou decisões de política de segurança. |
| AADSTS50132 | SsoArtifactInvalidOrExpired - A sessão não é válida devido à expiração da senha ou alteração recente de senha. |
| AADSTS50133 | SsoArtifactRevoked - A sessão não é válida devido à expiração da senha ou alteração recente de senha. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter - Centro de dados errado. Para autorizar uma solicitação que foi iniciada por um aplicativo no fluxo de dispositivos do OAuth 2.0, a parte de autorização deve estar no mesmo datacenter no qual a solicitação original reside. |
| AADSTS50135 | PasswordChangeCompromisedPassword - A alteração da senha é necessária devido ao risco da conta. |
| AADSTS50136 | RedirectMsaSessionToApp - sessão única do MSA detectada. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken - A sessão é inválida devido a um token de atualização externo ausente. |
| AADSTS50140 | KmsiInterrupt - Este erro ocorreu devido à interrupção "Mantenha-me conectado" quando o usuário estava fazendo login. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes. |
| AADSTS50143 | Incompatibilidade de sessão – A sessão é inválida porque o locatário do usuário não corresponde a dica de domínio devido a diferentes recursos. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - A senha do Active Directory do usuário expirou. Gere uma nova senha para o usuário ou peça para o usuário usar a ferramenta de redefinição de autoatendimento para redefinir sua senha. |
| AADSTS50146 | MissingCustomSigningKey - esse aplicativo precisa ser configurado com uma chave de assinatura específica do aplicativo. Ele não está configurado com uma, ou a chave expirou ou ainda não é válida. |
| AADSTS50147 | MissingCodeChallenge - O tamanho do parâmetro de desafio de código não é válido. |
| AADSTS50155 | DeviceAuthenticationFailed - Falha na autenticação do dispositivo para este usuário. |
| AADSTS50158 | ExternalSecurityChallenge - O desafio de segurança externa não foi satisfeito. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - As reclamações enviadas por um provedor externo não são suficientes ou a Declaração em falta é solicitada ao provedor externo. |
| AADSTS50166 | ExternalClaimsProviderThrottled - Falha ao enviar a solicitação ao provedor de declarações. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - O cliente é capaz de obter um token SSO por meio da extensão de contas do Windows 10, mas o token não foi encontrado na solicitação ou o token fornecido expirou. |
| AADSTS50169 | InvalidRequestBadRealm - O território não é um território configurado do namespace de serviço atual. |
| AADSTS50170 | MissingExternalClaimsProviderMapping - O mapeamento de controles externos está ausente. |
| AADSTS50173 | FreshTokenNeeded-a concessão fornecida expirou porque ela está sendo revogada e um novo token de autenticação é necessário. Um administrador ou um usuário revogou os tokens para esse usuário, fazendo com que as atualizações de token subsequentes falhem e requeiram a reautenticação. Faça com que o usuário entre novamente. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - O desafio externo não é suportado para usuários passantes. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - O controle de sessão não é suportado para usuários de passagem. |
| AADSTS50180 | WindowsIntegratedAuthMissing - É necessária uma autenticação integrada do Windows. Habilite o locatário para um SSO contínuo. |
| AADSTS50187 | DeviceInformationNotProvided - O serviço não conseguiu executar a autenticação do dispositivo. |
| AADSTS50196 | LoopDetected-um loop de cliente foi detectado. Verifique a lógica do aplicativo para garantir que o cache de token seja implementado e que as condições de erro sejam tratadas corretamente.  O aplicativo fez muitas da mesma solicitação em um período muito curto, indicando que ela está em um estado de falha ou está solicitando os tokens de forma abusiva. |
| AADSTS50197 | ConflictingIdentities-o usuário não pôde ser encontrado. Tente entrar novamente. |
| AADSTS50199 | CmsiInterrupt – por motivos de segurança, a confirmação do usuário é necessária para esta solicitação.  Como esse é um erro "interaction_required", o cliente deve fazer a autenticação interativa.  Isso ocorre porque uma WebView do sistema foi usada para solicitar um token para um aplicativo nativo-o usuário deve ser solicitado a perguntar se isso foi, na verdade, o aplicativo para o qual pretendia entrar. Para evitar esse prompt, o URI de redirecionamento deve fazer parte da seguinte lista segura: <br />http://<br />https://<br />msauth://(somente iOS)<br />msauthv2://(somente iOS)<br />Chrome-ramal://(somente navegador Chrome Desktop) |
| AADSTS51000 | RequiredFeatureNotEnabled - O recurso está desativado. |
| AADSTS51001 | DomainHintMustbePresent - A dica de domínio deve estar presente com o identificador de segurança local ou o UPN local. |
| AADSTS51004 | UserAccountNotInDirectory - a conta de usuário não existe no diretório. |
| AADSTS51005 | TemporaryRedirect - Equivalente ao status HTTP 307, que indica que as informações solicitadas estão localizadas no URI especificado no cabeçalho do local. Quando você receber esse status, siga o cabeçalho de local associado à resposta. Quando o método de solicitação original era POST, a solicitação redirecionada também usará o método POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - a autenticação integrada do Windows é necessária. Usuário que efetuou login usando um token de sessão que não possui a declaração de autenticação integrada do Windows. Solicite ao usuário que efetue login novamente. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - O usuário não forneceu consentimento para acessar recursos do LinkedIn. |
| AADSTS53000 | DeviceNotCompliant-a política de acesso condicional requer um dispositivo em conformidade e o dispositivo não é compatível. O usuário deve inscrever seu dispositivo em um provedor de MDM aprovado, como o Intune. |
| AADSTS53001 | DeviceNotDomainJoined-a política de acesso condicional requer um dispositivo ingressado no domínio e o dispositivo não está ingressado no domínio. O usuário use um domínio tenha ingressado no dispositivo. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp-o aplicativo usado não é um aplicativo aprovado para acesso condicional. O usuário precisa usar um dos aplicativos da lista de aplicativos aprovados para usar a fim de obter acesso. |
| AADSTS53003 | BlockedByConditionalAccess-o acesso foi bloqueado pelas políticas de acesso condicional. A política de acesso não permite a emissão de token. |
| AADSTS53004 | ProofUpBlockedDueToRisk - O usuário precisa concluir o processo de registro de autenticação multifator antes de acessar este conteúdo. O usuário deve se registrar para a autenticação multifator. |
| AADSTS53011 | Usuário bloqueado devido ao risco no locatário inicial. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - O usuário ou administrador não consentiu em usar o aplicativo com o ID X. Envie uma solicitação de autorização interativa para esse usuário e recurso. |
| AADSTS65004 | UserDeclinedConsent - O usuário recusou o consentimento para acessar o aplicativo. Peça que o usuário tente entrar novamente e dê consentimento ao aplicativo|
| AADSTS65005 | Configuração incorreta - A lista de acesso de recursos necessária ao aplicativo não contém aplicativos detectáveis pelo recurso ou O aplicativo cliente solicitou acesso ao recurso, que não foi especificado em sua lista de acesso a recursos necessária ou Serviço de gráfico retornou solicitação incorreta ou recurso não encontrado. Se o aplicativo dá suporte a SAML, você pode ter configurado o aplicativo com o identificador incorreto (entidade). Experimente a resolução listada para SAML usando o link abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](../manage-apps/application-sign-in-problem-federated-sso-gallery.md?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS650052 | O aplicativo precisa de acesso a um serviço `(\"{name}\")` que sua organização `\"{organization}\"` não assinou nem ativou. Entre em contato com seu administrador de ti para examinar a configuração de suas assinaturas de serviço. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - Falha na autenticação. O token de atualização não é válido. O erro pode ser devido aos seguintes motivos:<ul><li>O cabeçalho de vinculação de token está vazio</li><li>Token binding hash não corresponde</li></ul> |
| AADSTS70001 | UnauthorizedClient - o aplicativo está desabilitado. |
| AADSTS70002 | InvalidClient - Erro ao validar as credenciais. O client_secret especificado não corresponde ao valor esperado para este cliente. Corrija o client_secret e tente novamente. Para mais informações, consulte [Use o código de autorização para solicitar um token de acesso](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType - O aplicativo retornou um tipo de concessão não suportado. |
| AADSTS70004 | InvalidRedirectUri - o aplicativo retornou um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum endereço configurado ou a quaisquer endereços na lista de aprovação OIDC. |
| AADSTS70005 | UnsupportedResponseType - O aplicativo retornou um tipo de resposta não suportado devido aos seguintes motivos:<ul><li>tipo de resposta 'token' não está habilitado para o aplicativo</li><li>o tipo de resposta “id_token” requer escopo “OpenID” – contém um valor de parâmetro OAuth sem suporte no wctx codificado</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - O aplicativo retornou um valor não suportado de `response_mode` ao solicitar um token.  |
| AADSTS70008 | ExpiredOrRevokedGrant - O token de atualização expirou devido à inatividade. O token foi emitido no XXX e ficou inativo por um determinado período. |
| AADSTS70011 | InvalidScope - O escopo solicitado pelo aplicativo é inválido. |
| AADSTS70012 | MsaServerError - Ocorreu um erro no servidor durante a autenticação de um usuário MSA (consumidor). Tente novamente. Se continuar a falhar, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - Erro no fluxo do dispositivo OAuth 2.0. A autorização está pendente. O dispositivo tentará novamente a consulta. |
| AADSTS70018 | BadVerificationCode - Código de verificação inválido devido ao usuário digitar o código do usuário errado para o fluxo do código do dispositivo. Autorização não é aprovada. |
| AADSTS70019 | CodeExpired - O código de verificação expirou. Solicite ao usuário que tente se conectar novamente. |
| AADSTS75001 | BindingSerializationError - Ocorreu um erro durante a ligação da mensagem SAML. |
| AADSTS75003 | UnsupportedBindingError - O aplicativo retornou um erro relacionado à ligação não suportada (a resposta do protocolo SAML não pode ser enviada por meio de ligações que não sejam HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid - o Azure AD não suporta a solicitação SAML enviada pelo aplicativo para SSO. |
| AADSTS7500514 | Um tipo de resposta de SAML com suporte não foi encontrado. Os tipos de resposta com suporte são ' Response ' (no namespace XML ' urn: Oasis: names: TC: SAML: 2.0: Protocol ') ou ' Assertion ' (no namespace XML ' urn: Oasis: names: TC: SAML: 2.0: Assertion '). Erro de aplicativo-o desenvolvedor tratará esse erro.|
| AADSTS75008 | RequestDeniedError - a solicitação do aplicativo foi negada, pois a solicitação SAML tinha um destino inesperado. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - O método de autenticação pelo qual o usuário autenticado com o serviço não corresponde ao método de autenticação solicitado. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - A Solicitação de Autenticação SAML2 possui NameIdPolicy inválido. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - O Agente de Autenticação não consegue se conectar ao Active Directory. Certifique-se de que os servidores de agentes sejam membros da mesma floresta do AD que os usuários cujas senhas precisam ser validadas e possam se conectar ao Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout-a solicitação de validação de senha atingiu o tempo limite. Verifique se Active Directory está disponível e respondendo a solicitações dos agentes. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - Ocorreu um erro desconhecido ao processar a resposta do Agente de Autenticação. Tente novamente a solicitação. Se continuar a falhar, [abra um ticket de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - O Agente de Autenticação não pode validar a senha do usuário. Verifique os logs do agente para obter mais informações e verifique se o Active Directory está operando conforme o esperado. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - O Agente de Autenticação não pode descriptografar a senha. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - Os usuários tentaram efetuar logon fora dos horários permitidos (isso é especificado no AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - A tentativa de autenticação não pôde ser concluída devido à distorção de tempo entre a máquina que está executando o agente de autenticação e o AD. Corrigir problemas de sincronização de horário. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Falha na tentativa de autenticação do Kerberos. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - O pacote de autenticação não é suportado. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - Nenhum cabeçalho de autorização foi encontrado. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - O locatário não está habilitado para SSO Contínuo. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - Não é possível validar o ticket Kerberos do usuário. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - Falha no SSO Contínuo porque o ticket Kerberos do usuário expirou ou é inválido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - Não é possível localizar o objeto do usuário com base nas informações do ticket Kerberos do usuário. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - O usuário que está tentando entrar no Azure AD é diferente do usuário conectado ao dispositivo. |
| AADSTS90002 | InvalidTenantName - O nome do inquilino não foi encontrado no armazenamento de dados. Check to make sure you have the correct tenant ID. |
| AADSTS90004 | InvalidRequestFormat - a solicitação não está formatado corretamente. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements - não é possível concluir a solicitação. A solicitação não é válida porque o identificador e a dica de login não podem ser usados juntos.  |
| AADSTS90006 | ExternalServerRetryableError - O serviço está temporariamente indisponível.|
| AADSTS90007 | InvalidSessionId - solicitação inválida. A ID da sessão passada não pode ser analisada. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission - O usuário ou administrador não consentiu em usar o aplicativo. No mínimo, o aplicativo requer acesso ao Azure AD especificando o login e lendo a permissão de perfil do usuário. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier - O aplicativo está solicitando um token para si mesmo. Esse cenário é suportado apenas se o recurso especificado estiver usando o ID do aplicativo baseado em GUID. |
| AADSTS90010 | NotSupported - Não é possível criar o algoritmo. |
| AADSTS90012 | RequestTimeout - O solicitado expirou. |
| AADSTS90013 | InvalidUserInput - A entrada do usuário não é válida. |
| AADSTS90014 | MissingRequiredField - Esse código de erro pode aparecer em vários casos quando um campo esperado não está presente na credencial. |
| AADSTS90015 | QueryStringTooLong - A cadeia de consulta é muito longa. |
| AADSTS90016 | MissingRequiredClaim - O token de acesso não é válido. A declaração necessária está ausente. |
| AADSTS90019 | MissingTenantRealm - O Azure AD não pôde determinar o identificador de inquilino da solicitação. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - O formato do nome principal não é válido ou não atende ao formato `name[/host][@realm]` esperado. O nome principal é obrigatório, o host e o domínio são opcionais e podem ser definidos como nulos. |
| AADSTS90023 | InvalidRequest - a solicitação de serviço de autenticação não é válida. |
| AADSTS9002313 | InvalidRequest-a solicitação está malformada ou é inválida. -O problema aqui é porque houve algo errado com a solicitação para um determinado ponto de extremidade. A sugestão para esse problema é obter um rastreamento Fiddler do erro que está ocorrendo e procurando ver se a solicitação está realmente formatada ou não corretamente. |
| AADSTS90024 | RequestBudgetExceededError - Ocorreu um erro transitório. Tente novamente. |
| AADSTS90033 | MsodsServiceUnavailable - O serviço de diretório online da Microsoft (MSODS) não está disponível. |
| AADSTS90036 | MsodsServiceUnretryableFailure - Ocorreu um erro inesperado e não repetível do serviço WCF hospedado pelo MSODS. [Abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro. |
| AADSTS90038 | NationalCloudTenantRedirection - O inquilino especificado 'Y' pertence ao National Cloud 'X'. A instância atual da nuvem 'Z' não se associa com o X. Um erro de redirecionamento da nuvem é retornado. |
| AADSTS90043 | NationalCloudAuthCodeRedirection - O recurso está desativado. |
| AADSTS90051 | InvalidNationalCloudId - O identificador nacional da nuvem contém um identificador de nuvem inválido. |
| AADSTS90055 | TenantThrottlingError - Há muitas solicitações recebidas. Esta exceção é lançada para inquilinos bloqueados. |
| AADSTS90056 | BadResourceRequest - Para resgatar o código de um token de acesso, o aplicativo deve enviar uma solicitação POST para o endpoint `/token`. Além disso, antes disso, você deve fornecer um código de autorização e enviá-lo na solicitação POST para o nó de extremidade `/token`. Consulte este artigo para obter uma visão geral do fluxo de código de autorização do OAuth 2,0: [.. /azuread-dev/v1-Protocols-OAuth-Code.MD](../azuread-dev/v1-protocols-oauth-code.md). Direcione o usuário para o terminal `/authorize`, que retornará um código_de_ativação. Ao postar uma solicitação para o endpoint `/token`, o usuário obtém o token de acesso. Faça logon no portal do Azure e verifique **Registros do aplicativo> Endpoints** para confirmar se os dois pontos de extremidade foram configurados corretamente. |
| AADSTS90072 | PassThroughUserMfaError - A conta externa com a qual o usuário se conecta não existe no locatário em que eles se conectaram; Assim, o usuário não pode satisfazer os requisitos de MFA para o locatário. Esse erro também poderá ocorrer se os usuários forem sincronizados, mas houver uma incompatibilidade no atributo imutável (sourceAnchor) entre Active Directory e o Azure AD. A conta deve ser adicionada como um usuário externo no inquilino primeiro. Saia e faça login com uma conta de usuário diferente do AD do Azure. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid - Ocorreu um erro quando o serviço tentou processar uma mensagem do WS-Federation. A mensagem não é válida. |
| AADSTS90082 | OrgIdWsFederationNotSupported - A política de autenticação selecionada para a solicitação não é atualmente suportada. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed - Contas de convidado não são permitidas para este site. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed - O serviço não pode emitir um token porque o objeto da empresa ainda não foi provisionado. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired - O token DA do usuário expirou. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed - Ocorreu um erro ao criar a mensagem do WS-Federation a partir do URI. |
| AADSTS90090 | GraphRetryableError - O serviço está temporariamente indisponível. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Gráfico retornado com um código de erro proibido para a solicitação. |
| AADSTS90094 | AdminConsentRequired - É necessário o consentimento do administrador. |
| AADSTS900382 | Não há suporte para o cliente confidencial na solicitação de nuvem cruzada. |
| AADSTS90099 | O aplicativo ' {appId} ' ({appName}) não foi autorizado no locatário ' {Tenant} '. Os aplicativos devem estar autorizados a acessar o locatário do cliente antes que os administradores delegados do parceiro possam usá-los. Forneça o consentimento prévio ou execute a API apropriada do Partner Center para autorizar o aplicativo. |
| AADSTS90100 | InvalidRequestParameter - O parâmetro está vazio ou não é válido. |
| AADSTS901002 | AADSTS901002: não há suporte para o parâmetro de solicitação ' Resource '. |
| AADSTS90101 | InvalidEmailAddress - Os dados fornecidos não são um endereço de e-mail válido. O endereço de e-mail deve estar no formato `someone@example.com`. |
| AADSTS90102 | InvalidUriParameter - O valor deve ser um URI absoluto válido. |
| AADSTS90107 | InvalidXml - A solicitação não é válida. Certifique-se de que seus dados não tenham caracteres inválidos.|
| AADSTS90114 | InvalidExpiryDate - O carimbo de data e hora de expiração do token em massa fará com que um token expirado seja emitido. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode - O código do usuário é nulo ou vazio.|
| AADSTS90120 | InvalidDeviceFlowRequest - A solicitação já foi autorizada ou recusada. |
| AADSTS90121 | InvalidEmptyRequest - solicitação vazia inválida.|
| AADSTS90123 | IdentityProviderAccessDenied - O token não pode ser emitido porque o provedor de identidade ou reclamação de reivindicação negou a solicitação. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported - O recurso não é suportado nos pontos finais `/common` ou `/consumers`. Use o ponto de extremidade específico de `/organizations` ou inquilino. |
| AADSTS90125 | DebugModeEnrollTenantNotFound - O usuário não está no sistema. Certifique-se de ter inserido o nome do usuário corretamente. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred - O tipo de usuário não é suportado neste terminal. O sistema não pode inferir o inquilino do usuário a partir do nome do usuário. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported - O aplicativo não é suportado nos pontos de extremidades `/common` ou `/consumers`. Use o ponto de extremidade específico de `/organizations` ou inquilino. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated - Ocorreu um erro não repetível.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound - O usuário principal não possui a chave de ID do NGC configurada. |
| AADSTS130005 | NgcInvalidSignature - Falha na assinatura da chave do NGC.|
| AADSTS130006 | NgcTransportKeyNotFound - A chave de transporte do NGC não está configurada no dispositivo. |
| AADSTS130007 | NgcDeviceIsDisabled - O dispositivo está desativado. |
| AADSTS130008 | NgcDeviceIsNotFound - O dispositivo referenciado pela chave NGC não foi encontrado. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce - solicitação nonce não é fornecida. |
| AADSTS140001 | InvalidSessionKey - A chave da sessão não é válida.|
| AADSTS165900 | InvalidApiRequest - Pedido inválido. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion - A versão do Chrome WebView não é suportada. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - O recurso não está configurado para aceitar tokens somente de dispositivo. |
| AADSTS240001 | BulkAADJTokenUnauthorized - O usuário não está autorizado a registrar dispositivos no Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing - O id_token não pode ser usado como concessão `urn:ietf:params:oauth:grant-type:jwt-bearer`.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy-o administrador de locatários configurou uma política de segurança que bloqueia essa solicitação. Verifique as políticas de segurança definidas no nível de locatário para determinar se a solicitação atende aos requisitos das políticas. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest: o aplicativo não foi encontrado no diretório/locatário. Isso poderá acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou aceito por qualquer usuário no locatário. Você pode ter configurado incorretamente o valor do identificador para o aplicativo ou ter enviado a solicitação de autenticação para o locatário errado. |
| AADSTS700020 | InteractionRequired - A concessão de acesso requer interação. |
| AADSTS700022 | InvalidMultipleResourcesScope - O valor fornecido para o escopo do parâmetro de entrada não é válido porque contém mais de um recurso. |
| AADSTS700023 | InvalidResourcelessScope - O valor fornecido para o escopo do parâmetro de entrada não é válido ao solicitar um token de acesso. |
| AADSTS7000215 | Segredo do cliente inválido é fornecido. Erro do desenvolvedor-o aplicativo está tentando entrar sem os parâmetros de autenticação necessários ou corretos.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided-as chaves secretas do cliente fornecidas expiraram. Visite a portal do Azure para criar novas chaves para seu aplicativo ou considere o uso de credenciais de certificado para segurança adicional: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | O código de autorização fornecido pelo InvalidGrantRedeemAgainstWrongTenant deve ser usado em relação a outro locatário, portanto rejeitado. O código de autorização OAuth2 deve ser resgatado no mesmo locatário em que foi adquirido (/Common ou/{tenant-ID} conforme apropriado) |
| AADSTS1000000 | UserNotBoundError - A API de vinculação exige que o usuário do AD do Azure também se autentique com um IDP externo, o que ainda não aconteceu. |
| AADSTS1000002 | BindCompleteInterruptError - A ligação foi concluída com êxito, mas o usuário deve ser informado. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-o aplicativo está desabilitado. |
| AADSTS7000114| O aplicativo ' appIdentifier ' não tem permissão para fazer chamadas em nome de aplicativo.|
| AADSTS7500529 | O valor ' SAMLid-GUID ' não é uma ID SAML válida-o AD do Azure usa esse atributo para popular o atributo inresponseto da resposta retornada. A ID não deve começar com um número. Uma estratégia comum é anexar uma cadeia de caracteres como "id" à representação de cadeia de caracteres de um GUID. Por exemplo, id6c1c178c166d486687be4aaf5e482730 é uma ID válida. |

## <a name="next-steps"></a>Próximas etapas

* Tem alguma dúvida ou não consegue encontrar o que está procurando? Crie um problema no GitHub ou consulte [Suporte e opções de ajuda para desenvolvedores](./developer-support-help-options.md) para aprender sobre outras maneiras de obter ajuda e suporte.
