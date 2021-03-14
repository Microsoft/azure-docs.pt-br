---
title: Atributos de perfil de usuário no Azure Active Directory B2C
description: Saiba mais sobre os atributos do tipo de recurso de usuário que são suportados pelo perfil de usuário do diretório do Azure AD B2C. Saiba mais sobre atributos internos, extensões e como os atributos são mapeados no Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500522"
---
# <a name="user-profile-attributes"></a>Atributos de perfil do usuário

Seu perfil do usuário do diretório do Azure Active Directory B2C é fornecido com um conjunto interno de atributos, como nome, sobrenome, cidade, CEP e número de telefone. Você pode estender o perfil do usuário com seus próprios dados de aplicativo sem a necessidade de um armazenamento de dados externo. 

A maioria dos atributos que podem ser usados com perfis de usuário do Azure AD B2C também tem suporte do Microsoft Graph. Este artigo descreve atributos de perfil de usuário do Azure AD B2C com suporte. Ele também anota os atributos que não têm suporte do Microsoft Graph, bem como os atributos do Microsoft Graph que não devem ser usados com Azure AD B2C.

> [!IMPORTANT]
> Você não deve usar atributos internos ou de extensão para armazenar dados pessoais confidenciais, como credenciais de conta, números de identificação governamental, dados de titulares de cartão, dados de contas financeiras, informações de saúde ou informações de histórico confidenciais.

Você também pode integrar com sistemas externos. Por exemplo, você pode usar o Azure AD B2C para autenticação, mas delegar para um CRM (gerenciamento de relacionamento com o cliente) externo ou a um banco de dados de fidelidade do cliente como a fonte de verdade dos dados do cliente. Para obter mais informações, consulte a solução de [perfil remoto](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

## <a name="azure-ad-user-resource-type"></a>Tipo de recurso de usuário do Azure AD

A tabela a seguir lista os atributos do [tipo de recurso de usuário](/graph/api/resources/user) que são suportados pelo perfil de usuário do diretório do Azure AD B2C. Ela contém as seguintes informações sobre cada atributo:

- Nome do atributo usado pelo Azure AD B2C (seguido pelo nome do Microsoft Graph em parênteses, se for diferente)
- Tipo de dados de atributo
- Descrição do atributo
- Se o atributo estiver disponível no portal do Azure
- Se o atributo pode ser usado em um fluxo de usuário
- Se o atributo pode ser usado em uma política personalizada de [perfil técnico do Azure Active Directory](active-directory-technical-profile.md) e em qual seção (&lt;InputClaims&gt;, &lt;OutputClaims&gt; ou &lt;PersistedClaims&gt;)

|Nome     |Tipo     |Descrição|Portal do Azure|Fluxos de usuário|Política personalizada|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|Se a conta de usuário está habilitada ou desabilitada: **true** se a conta estiver habilitada; caso contrário, **false**.|Sim|Não|Persistente, Saída|
|ageGroup        |String|O grupo de idade do usuário. Valores possíveis: null, Undefined, Minor, Adult, NotAdult.|Sim|Não|Persistente, Saída|
|alternativeSecurityId ([Identidades](#identities-attribute))|String|Uma identidade de usuário único do provedor de identidade externo.|Não|Não|Entrada, Persistente, Saída|
|alternativeSecurityIds ([Identidades](#identities-attribute))|coleção securityid alternativa|Uma coleção de identidades de usuário de provedores de identidade externos.|Não|Não|Persistente, Saída|
|city            |String|A cidade em que o usuário está localizado. Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|consentProvidedForMinor|String|Se o consentimento foi fornecido a um menor. Valores permitidos: null, granted, denied ou notRequired.|Sim|Não|Persistente, Saída|
|country         |String|O país/região em que o usuário está localizado. Exemplo: "EUA" ou "Reino Unido". Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|createdDateTime|Datetime|A data em que o objeto do usuário foi criado. Somente leitura.|Não|Não|Persistente, Saída|
|creationType    |String|Se a conta de usuário foi criada como uma conta local para um locatário do Azure Active Directory B2C, o valor será LocalAccount ou nameCoexistence. Somente leitura.|Não|Não|Persistente, Saída|
|dateOfBirth     |Data|Data de nascimento.|Não|Não|Persistente, Saída|
|department      |String|O nome do departamento no qual o usuário trabalha. Comprimento máximo de 64.|Sim|Não|Persistente, Saída|
|displayName     |String|O nome de exibição do usuário. Comprimento máximo de 256.|Sim|Sim|Persistente, Saída|
|facsimileTelephoneNumber<sup>1</sup>|String|O número de telefone do aparelho de fax comercial do usuário.|Sim|Não|Persistente, Saída|
|givenName       |String|O nome (primeiro nome) do usuário. Comprimento máximo de 64.|Sim|Sim|Persistente, Saída|
|jobTitle        |String|O cargo do usuário. Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|immutableId     |String|Um identificador que é normalmente usado para usuários migrados do Active Directory local.|Não|Não|Persistente, Saída|
|legalAgeGroupClassification|String|Classificação de grupo de faixa etária. Somente leitura e calculado com base nas propriedades ageGroup e consentProvidedForMinor. Valores permitidos: NULL, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, não adulto e adulto.|Sim|Não|Persistente, Saída|
|legalCountry<sup>1</sup>  |String|País/Região para fins legais.|Não|Não|Persistente, Saída|
|mail            |String|O endereço SMTP para o usuário, por exemplo "bob@contoso.com". Somente leitura.|Não|Não|Persistente, Saída|
|mailNickName    |String|O alias de email para o usuário. Comprimento máximo de 64.|Não|Não|Persistente, Saída|
|mobile (mobilePhone) |String|O número do telefone celular principal do usuário. Comprimento máximo de 64.|Sim|Não|Persistente, Saída|
|netId           |String|ID do Net.|Não|Não|Persistente, Saída|
|objectId        |String|Um GUID (identificador global exclusivo) que é o identificador exclusivo do usuário. Exemplo: 12345678-9abc-def0-1234-56789abcde. Somente leitura, Imutável.|Somente leitura|Sim|Entrada, Persistente, Saída|
|otherMails      |Coleção de Cadeias de Caracteres|Uma lista de outros endereços de email para o usuário. Exemplo: ["bob@contoso.com", "Robert@fabrikam.com"].|Sim (E-mail alternativo)|Não|Persistente, Saída|
|password        |String|A senha da conta local durante a criação do usuário.|Não|Não|Persistente|
|passwordPolicies     |String|Política da senha. É uma cadeia de caracteres que consiste em um nome de política diferente separado por vírgula. Por exemplo, "DisablePasswordExpiration, DisableStrongPassword".|Não|Não|Persistente, Saída|
|physicalDeliveryOfficeName (officeLocation)|String|A localização do escritório na sede da empresa do usuário. Comprimento máximo de 128.|Sim|Não|Persistente, Saída|
|postalCode      |String|O CEP do endereço postal do usuário. O CEP é específico do país/região do usuário. No Estados Unidos da América, esse atributo contém o código ZIP. Comprimento máximo de 40.|Sim|Não|Persistente, Saída|
|preferredLanguage    |String|O idioma preferencial do usuário. O formato de idioma preferencial é baseado em RFC 4646. O nome é uma combinação de um código de cultura de letra minúscula ISO 639 2, associado à linguagem, e um código de subcultura em maiúsculas ISO 3166 2, associado ao país ou à região. Exemplo: "en-US" ou "es-ES".|Não|Não|Persistente, Saída|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|Datetime|Todos os tokens de atualização emitidos antes desse horário são inválidos e os aplicativos receberão um erro ao usar um token de atualização inválido para adquirir um novo token de acesso. Se isso acontecer, o aplicativo precisará adquirir um novo token de atualização fazendo uma solicitação para o ponto de extremidade de autorização. Somente leitura.|Não|Não|Saída|
|signInNames ([Identidades](#identities-attribute)) |String|O nome de entrada exclusivo do usuário da conta local de qualquer tipo no diretório. Use esse atributo para obter um usuário com valor de entrada sem especificar o tipo de conta local.|Não|Não|Entrada|
|signInNames.userName ([Identidades](#identities-attribute)) |String|O nome de usuário exclusivo do usuário da conta local no diretório. Use esse atributo para criar ou obter um usuário com um nome de logon específico. Especificar isso só no PersistedClaims durante a operação do Patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, Persistente, Saída|
|signInNames.phoneNumber ([Identidades](#identities-attribute)) |String|O número de telefone exclusivo do usuário da conta local no diretório. Use esse atributo para criar ou obter um usuário com um número de telefone de entrada específico. A especificação desse atributo no PersistedClaims sozinho durante a operação do patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, Persistente, Saída|
|signInNames.emailAddress ([Identidades](#identities-attribute))|String|O endereço de e-mail exclusivo do usuário da conta local no diretório. Use-o para criar ou obter um usuário com um e-mail específico para entrada. A especificação desse atributo no PersistedClaims sozinho durante a operação do patch removerá outros tipos de signInNames. Se quiser adicionar um novo tipo de signInNames, também precisará manter o signInNames existente.|Não|Não|Entrada, Persistente, Saída|
|state           |String|O estado ou a província no endereço do usuário. Comprimento máximo de 128.|Sim|Sim|Persistente, Saída|
|streetAddress   |String|O endereço da sede da empresa do usuário. Comprimento máximo de 1024.|Sim|Sim|Persistente, Saída|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|Um número de telefone secundário do usuário, que é usado para autenticação multifator.|Sim|Não|Persistente, Saída|
|strongAuthenticationEmailAddress<sup>1</sup>|String|O endereço SMTP do usuário. Exemplo: "bob@contoso.com". Esse atributo é usado para entrar com a política de nome de usuário, para armazenar o endereço de e-mail do usuário. O endereço de e-mail é usado em um fluxo de redefinição de senha.|Sim|Não|Persistente, Saída|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|O número de telefone principal do usuário, usado para autenticação multifator.|Sim|Não|Persistente, Saída|
|sobrenome         |String|O sobrenome do usuário (nome de família ou último nome). Comprimento máximo de 64.|Sim|Sim|Persistente, Saída|
|telephoneNumber (primeira entrada de businessPhones)|String|O número de telefone principal do sede da empresa do usuário.|Sim|Não|Persistente, Saída|
|userPrincipalName    |String|O UPN (nome principal do usuário) do usuário. O UPN é um nome de entrada de um usuário, no estilo da Internet, para o usuário com base no padrão RFC 822 da Internet. O domínio deve estar presente na coleção de domínios verificados do locatário. Essa propriedade é necessária ao criar uma conta. Imutável.|Não|Não|Entrada, Persistente, Saída|
|usageLocation   |String|Necessário para usuários que receberão licenças devido ao requisito legal de verificar a disponibilidade de serviços em países/regiões. Não permite valor nulo. Um código de país/região de duas letras (padrão ISO 3166). Exemplos: "US", "JP" e "GB".|Sim|Não|Persistente, Saída|
|userType        |String|Um valor de cadeia de caracteres que pode ser usado para classificar os tipos de usuário em seu diretório. O valor deve ser Member. Somente leitura.|Somente leitura|Não|Persistente, Saída|
|userState (externalUserState)<sup>3</sup>|String|Exclusivamente para a conta Azure AD B2B, indica se o convite é PendingAcceptance ou Accepted.|Não|Não|Persistente, Saída|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|Mostra o carimbo de data/hora para a alteração mais recente da propriedade UserState.|Não|Não|Persistente, Saída|

<sup>1 </sup>Não tem suporte do Microsoft Graph<br><sup>2 </sup> Para obter mais informações, consulte [atributo de número de telefone MFA](#mfa-phone-number-attribute)<br><sup>3 </sup> Não deve ser usado com Azure AD B2C

## <a name="display-name-attribute"></a>Atributo de nome de exibição

O `displayName` é o nome a ser exibido em portal do Azure gerenciamento de usuário para o usuário e no token de acesso Azure ad B2C retorna ao aplicativo. Esta propriedade é necessária.

## <a name="identities-attribute"></a>Atributo Identities

Uma conta de cliente, que pode ser um consumidor, parceiro ou cidadão, pode ser associada a esses tipos de identidade:

- Identidade **local** -o nome de usuário e a senha são armazenados localmente no diretório Azure ad B2C. Geralmente, nos referimos a essas identidades como "contas locais".
- Identidade **federada** – também conhecida como contas *corporativas* ou *sociais* , a identidade do usuário é gerenciada por um provedor de identidade federado, como Facebook, Microsoft, ADFS ou Salesforce.

Um usuário com uma conta de cliente pode entrar com várias identidades. Por exemplo, nome de usuário, email, ID do funcionário, ID do governo e outros. Uma única conta pode ter várias identidades, locais e sociais, com a mesma senha. 

Na API Microsoft Graph, as identidades local e federada são armazenadas no atributo User `identities` , que é do tipo [objectidentity](/graph/api/resources/objectidentity). A `identities` coleção representa um conjunto de identidades usadas para entrar em uma conta de usuário. Essa coleção permite que o usuário entre na conta de usuário com qualquer uma de suas identidades associadas. O atributo Identities pode conter até dez objetos [objectidentity](/graph/api/resources/objectidentity) . Cada objeto contém as seguintes propriedades:

| Nome   | Tipo |Descrição|
|:---------------|:--------|:----------|
|signInType|string| Especifica os tipos de entrada do usuário em seu diretório. Para conta local:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` ou qualquer outro tipo que você desejar. A conta social deve ser definida como  `federated` .|
|emissor|string|Especifica o emissor da identidade. Para contas locais (em que **signInType** não é `federated` ), essa propriedade é o nome de domínio padrão do locatário B2C local, por exemplo `contoso.onmicrosoft.com` . Para a identidade social (em que **signInType** é  `federated` ), o valor é o nome do emissor, por exemplo `facebook.com`|
|issuerAssignedId|string|Especifica o identificador exclusivo atribuído ao usuário pelo emissor. A combinação de **emissor** e **issuerAssignedId** deve ser exclusiva em seu locatário. Para conta local, quando **signInType** é definido como `emailAddress` ou `userName` , ele representa o nome de entrada para o usuário.<br>Quando **signInType** é definido como: <ul><li>`emailAddress` (ou começa com `emailAddress` like `emailAddress1` ) **issuerAssignedId** deve ser um endereço de email válido</li><li>`userName` (ou qualquer outro valor), **issuerAssignedId** deve ser uma [parte local válida de um endereço de email](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** representa o identificador exclusivo da conta federada</li></ul>|

O atributo de **identidades** a seguir, com uma identidade de conta local com um nome de entrada, um endereço de email como entrada e com uma identidade social. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Para identidades federadas, dependendo do provedor de identidade, o **issuerAssignedId** é um valor exclusivo para um determinado usuário por aplicativo ou conta de desenvolvimento. Configure a política de Azure AD B2C com a mesma ID de aplicativo que foi atribuída anteriormente pelo provedor social ou outro aplicativo dentro da mesma conta de desenvolvimento. 

## <a name="password-profile-property"></a>Propriedade de perfil de senha

Para uma identidade local, o atributo **passwordProfile** é necessário e contém a senha do usuário. O `forceChangePasswordNextSignIn` atributo indica se um usuário deve redefinir a senha na próxima entrada. Para lidar com uma redefinição de senha forçada, [Configure o fluxo de redefinição de senha forçada](force-password-reset.md).

Para uma identidade federada (social), o atributo **passwordProfile** não é necessário.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Atributo de política de senha

A política de senha de Azure AD B2C (para contas locais) é baseada na Azure Active Directory política de [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) . As políticas de inscrição ou entrada e redefinição de senha do Azure AD B2C exigem essa força de senha forte e não expiram as senhas.

Em cenários de migração de usuário, se as contas que você deseja migrar tiverem uma intensidade de senha mais fraca do que a [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) imposta por Azure ad B2C, você poderá desabilitar o requisito de senha forte. Para alterar a política de senha padrão, defina o `passwordPolicies` atributo como `DisableStrongPassword` . Por exemplo, você pode modificar a solicitação de criação de usuário da seguinte maneira:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Atributo de número de telefone MFA

Ao usar um telefone para a MFA (autenticação multifator), o telefone celular é usado para verificar a identidade do usuário. Para [Adicionar](/graph/api/authentication-post-phonemethods) um novo número de telefone programaticamente, [Atualizar](/graph/api/b2cauthenticationmethodspolicy-update), [obter](/graph/api/b2cauthenticationmethodspolicy-get)ou [excluir](/graph/api/phoneauthenticationmethod-delete) o número de telefone, use o [método de autenticação por telefone](/graph/api/resources/phoneauthenticationmethod)MS API do Graph.

Em Azure AD B2C [políticas personalizadas](custom-policy-overview.md), o número de telefone está disponível por meio do `strongAuthenticationPhoneNumber` tipo de declaração.

## <a name="extension-attributes"></a>Atributos de extensão

Cada aplicativo voltado para o cliente tem requisitos exclusivos para que as informações sejam coletadas. Seu locatário de Azure AD B2C vem com um conjunto interno de informações armazenadas em Propriedades, como nome, sobrenome e CEP fornecidos. Com Azure AD B2C, você pode estender o conjunto de propriedades armazenadas em cada conta de cliente. Para obter mais informações, consulte [adicionar atributos de usuário e personalizar entrada do usuário no Azure Active Directory B2C](configure-user-input.md)

Os atributos de extensão [estender o esquema](/graph/extensibility-overview#schema-extensions) dos objetos de usuário no diretório. Os atributos de extensão só podem ser registrados em um objeto de aplicativo, mesmo que possam conter dados para um usuário. O atributo de extensão é anexado ao aplicativo chamado `b2c-extensions-app` . Não modifique esse aplicativo, pois ele é usado pelo Azure AD B2C para armazenar dados do usuário. Você pode encontrar esse aplicativo em Registros de aplicativo do Azure Active Directory.

> [!NOTE]
> - Até 100 atributos de extensão podem ser gravados em qualquer conta de usuário.
> - Se o aplicativo b2c-extensions-app for excluído, esses atributos de extensão serão removidos de todos os usuários junto com os dados que eles contêm.
> - Se um atributo de extensão for excluído pelo aplicativo, ele será removido de todas as contas de usuário e os valores serão excluídos.

Os atributos de extensão no API do Graph são nomeados usando a Convenção `extension_ApplicationClientID_AttributeName` , em que o `ApplicationClientID` é a **ID do aplicativo (cliente)** do `b2c-extensions-app` aplicativo (encontrado em **registros de aplicativo**  >  **todos os aplicativos** no portal do Azure). Observe que a **ID do aplicativo (cliente)** , como é representada no nome do atributo de extensão, não inclui hifens. Por exemplo:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Os tipos de dados a seguir têm suporte ao definir um atributo em uma extensão de esquema:

|Tipo |Comentários  |
|--------------|---------|
|Boolean    | Os valores possíveis: **true** ou **false**. |
|Datetime   | Deve ser especificado no formato ISO 8601. Será armazenado em UTC.   |
|Integer    | Valor de 32 bits.               |
|String     | Máximo de 256 caracteres.     |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os atributos de extensão:

- [Extensões de esquema](/graph/extensibility-overview#schema-extensions)
- [Definir atributos personalizados](user-flow-custom-attributes.md)
