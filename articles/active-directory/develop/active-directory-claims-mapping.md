---
title: Personalizar declarações para um aplicativo em um locatário do Azure AD (visualização pública)
description: Esta página descreve o mapeamento de declarações no Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97de45ef94afa9da8a5e928a3d4a8911db052107
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381057"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Como: personalizar declarações emitidas em tokens para um aplicativo específico em um locatário (versão prévia)

> [!NOTE]
> Esse recurso substitui a [personalização de declarações](active-directory-saml-claims-customization.md) oferecida pelo portal atualmente. No mesmo aplicativo, se você personalizar declarações usando o portal, além de usar o método Graph/PowerShell detalhado neste documento, os tokens emitidos para o aplicativo ignorarão a configuração no portal. Configurações feitas por meio dos métodos detalhados neste documento não serão refletidas no portal.

Esse recurso é usado por administradores de locatários para personalizar as declarações emitidas em tokens para um aplicativo específico em seu locatário. Você pode usar as políticas de mapeamento de declarações para:

- Selecionar quais declarações são incluídas nos tokens.
- Crie tipos de declaração que ainda não existem.
- Escolher ou alterar a fonte dos dados emitidos em declarações específicas.

> [!NOTE]
> Atualmente, essa capacidade está em visualização pública. Esteja preparado para reverter ou remover quaisquer alterações. O recurso está disponível em qualquer assinatura do Azure AD (Azure Active Directory) durante a visualização pública. No entanto, quando o recurso for disponibilizado para todos, alguns aspectos dele poderão exigir uma assinatura do Azure AD Premium. Esse recurso é compatível com a configuração das políticas de mapeamento de declarações para os protocolos WS-Fed, SAML, OAuth e OpenID Connect.

## <a name="claims-mapping-policy-type"></a>Tipo de política de mapeamento de declarações

No Azure AD, um objeto de **Política** representa um conjunto de regras empregadas em aplicativos individuais ou em todos os aplicativos de uma organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que são aplicadas aos objetos aos quais são atribuídas.

Uma política de mapeamento de declarações é um tipo de objeto de **Política** que modifica as declarações emitidas em tokens emitidos para aplicativos específicos.

## <a name="claim-sets"></a>Conjuntos de declarações

Há determinados conjuntos de declarações que definem como e quando elas são usadas em tokens.

| Conjunto de declarações | Descrição |
|---|---|
| Conjunto de declarações de núcleo | Estão presentes em todos os tokens, independentemente da política. Essas declarações também são consideradas restritas e não podem ser modificadas. |
| Conjunto de declarações básicas | Inclui as declarações que são emitidas por padrão para os tokens (além do conjunto de declarações principais). Você pode omitir ou modificar as declarações básicas usando as políticas de mapeamento de declarações. |
| Conjunto de declarações restritas | Não pode ser modificado usando a política. A fonte de dados não pode ser alterada e nenhuma transformação é aplicada ao gerar essas declarações. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: conjunto de declarações restritas do JWT (Token Web JSON)

| Tipo de declaração (nome) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| platf |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| roles |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>Tabela 2: conjunto de declarações restritas do SAML

| Tipo de declaração (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>Propriedades da política de mapeamento de declarações

Para controlar quais declarações são emitidas e o local em que os dados são originados, use as propriedades de uma política de mapeamento de declarações. Se uma política não for definida, o sistema emitirá tokens que incluam o conjunto de declarações principais, o conjunto de declarações básicas e quaisquer [declarações adicionais](active-directory-optional-claims.md) que o aplicativo optou por receber.

### <a name="include-basic-claim-set"></a>Incluir um conjunto de declarações básicas

**Cadeia de caracteres:** IncludeBasicClaimSet

**Tipo de dados:** booliano (true ou false)

**Resumo:** essa propriedade determina se o conjunto de declarações básicas está incluído nos tokens afetados por essa política.

- Se definido como True, todas as declarações no conjunto de declarações básicas serão emitidas nos tokens afetados pela política. 
- Se definido como False, as declarações no conjunto de declarações básicas não estarão nos tokens, a menos sejam adicionadas individualmente na propriedade de esquema de declarações da mesma política.

> [!NOTE] 
> As declarações no conjunto de declarações de núcleo estão presentes em todos os tokens, independentemente de como essa propriedade estiver definida. 

### <a name="claims-schema"></a>Esquema de declarações

**Cadeia de caracteres:** ClaimsSchema

**Tipo de dados:** blob JSON com uma ou mais entradas de esquema de declaração

**Resumo:** essa propriedade define quais declarações estão presentes nos tokens afetados pela política, além do conjunto de declarações básicas e do conjunto de declarações principais.
Para cada entrada de esquema de declaração definida nesta propriedade, certas informações são necessárias. Especifique o local em que os dados são originados (**Valor** ou o **par Origem/ID**) e com qual declaração eles são emitidos (**Tipo de declaração**).

### <a name="claim-schema-entry-elements"></a>Elementos de entrada do esquema de declaração

**Valor:** o elemento de valor define um valor estático como os dados a serem emitidos na declaração.

**Par ID/Origem:** os elementos de Origem e ID definem de onde os dados na declaração foram originados. 

Defina o elemento Origem para um dos seguintes valores: 

- "user": os dados na declaração são uma propriedade no objeto User. 
- "application": os dados na declaração são uma propriedade na entidade de serviço de aplicativo (cliente). 
- "resource": os dados na declaração são uma propriedade na entidade de serviço de recurso.
- "audience": os dados na declaração são uma propriedade da entidade de serviço que é o público-alvo do token (o cliente ou a entidade de serviço do recurso).
- "company": os dados na declaração são uma propriedade do objeto Company do locatário do recurso.
- "transformation": os dados na declaração são de uma transformação de declarações (consulte a seção "Transformação de declarações" mais adiante neste artigo).

Se a fonte for uma transformação, o elemento **TransformationID** deverá ser incluído na definição de declaração.

O elemento ID identifica qual propriedade na origem fornece o valor da declaração. A tabela a seguir lista os valores de ID válida para cada valor de Origem.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: valores de ID válidos por fonte

| Origem | ID | Descrição |
|-----|-----|-----|
| User | sobrenome | Nome da família |
| User | givenname | Nome |
| User | displayname | Nome para exibição |
| User | objectid | ObjectID |
| User | mail | Endereço de Email |
| User | userprincipalname | Nome UPN |
| User | department|department|
| User | onpremisessamaccountname | Nome da conta SAM local |
| User | netbiosname| Nome NetBios |
| User | dnsdomainname | Nome de Domínio DNS |
| User | onpremisesecurityidentifier | Identificador de segurança local |
| User | companyname| Nome da Organização |
| User | streetaddress | Endereço |
| User | postalcode | Código postal |
| User | preferredlanguange | Idioma preferencial |
| User | onpremisesuserprincipalname | UPN local |
| User | mailNickname | Apelido de email |
| User | extensionattribute1 | Atributo de extensão 1 |
| User | extensionattribute2 | Atributo de extensão 2 |
| User | extensionattribute3 | Atributo de extensão 3 |
| User | extensionattribute4 | Atributo de extensão 4 |
| User | extensionattribute5 | Atributo de extensão 5 |
| User | extensionattribute6 | Atributo de extensão 6 |
| Usuário | extensionattribute7 | Atributo de extensão 7 |
| User | extensionattribute8 | Atributo de extensão 8 |
| User | extensionattribute9 | Atributo de extensão 9 |
| User | extensionattribute10 | Atributo de extensão 10 |
| User | extensionattribute11 | Atributo de extensão 11 |
| User | extensionattribute12 | Atributo de extensão 12 |
| User | extensionattribute13 | Atributo de extensão 13 |
| User | extensionattribute14 | Atributo de extensão 14 |
| User | extensionattribute15 | Atributo de extensão 15 |
| User | othermail | Outro email |
| User | country | País |
| User | city | City |
| User | state | Estado |
| User | jobtitle | Cargo |
| User | employeeid | ID do funcionário |
| User | facsimiletelephonenumber | Número de telefone de fax |
| aplicativo, recurso, público-alvo | displayName | Nome para exibição |
| aplicativo, recurso, público-alvo | objected | ObjectID |
| aplicativo, recurso, público-alvo | marcações | Marcação da entidade de serviço |
| Empresa | tenantcountry | País/Região do locatário |

**TransformationID:** o elemento TransformationID deverá ser fornecido apenas se o elemento Source estiver definido como "transformation".

- Esse elemento deve corresponder ao elemento de ID da entrada de transformação na propriedade **ClaimsTransformation** que define como os dados dessa declaração são gerados.

**Tipo de declaração:** os elementos **JwtClaimType** e **SamlClaimType** definem a quais declarações essa entrada de esquema de declaração se refere.

- O JwtClaimType deve conter o nome da declaração a ser emitida em JWTs.
- O SamlClaimType deve conter o URI da declaração a ser emitida em tokens SAML.

> [!NOTE]
> Nomes e URIs de declarações no conjunto de declaração restritas não podem ser usados para os elementos de tipo de declaração. Para obter mais informações, consulte a seção "Restrições e exceções" mais adiante neste artigo.

### <a name="claims-transformation"></a>Transformação de declarações

**Cadeia de caracteres:** ClaimsTransformation

**Tipo de dados:** blob JSON com uma ou mais entradas de transformação 

**Resumo:** use esta propriedade para aplicar transformações comuns a dados de origem para gerar os dados de saída para declarações especificadas no esquema de declarações.

**ID:** use o elemento de ID para fazer referência a esta entrada de transformação na entrada de esquema de declarações TransformationID. Esse valor deve ser exclusivo para cada entrada de transformação nesta política.

**TransformationMethod:** o elemento TransformationMethod identifica qual operação é executada para gerar os dados para a declaração.

Com base no método escolhido, um conjunto de entradas e saídas é esperado. Defina as entradas e as saídas usando os elementos **InputClaims**, **InputParameters** e **OutputClaims**.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: métodos de transformação e entradas e saídas esperadas

|TransformationMethod|Entrada esperada|Saída esperada|Descrição|
|-----|-----|-----|-----|
|Ingressar|cadeia1, cadeia2, separador|outputClaim|Une cadeias de entrada usando um separador entre elas. Por exemplo: cadeia1: "foo@bar.com", cadeia2: "sandbox", separador: "." resulta no outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|mail|outputClaim|Extrai a parte local do endereço de email. Por exemplo: email: "foo@bar.com" resulta no outputClaim: "foo". Se não houver nenhum sinal \@ presente, a cadeia de caracteres de entrada original será retornada sem alterações.|

**InputClaims:** use um elemento InputClaims para passar os dados de uma entrada de esquema de declaração para uma transformação. Ele tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é unido ao elemento de ID da entrada de esquema de declaração para encontrar a declaração de entrada apropriada. 
- **TransformationClaimType** é usado para fornecer um nome exclusivo a essa entrada. Esse nome deve corresponder a uma das entradas esperadas para o método de transformação.

**InputParameters:** use um elemento InputParameters para passar um valor constante para uma transformação. Ele tem dois atributos: **Value** e **ID**.

- **Value** é o valor constante real a ser passado.
- O elemento **ID** é usado para fornecer um nome exclusivo a essa entrada. O nome deve corresponder a uma das entradas esperadas para o método de transformação.

**OutputClaims:** use um elemento OutputClaims para reter os dados gerados por uma transformação e associá-lo a uma entrada de esquema de declaração. Ele tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** é unido à ID da entrada de esquema de declaração para encontrar a declaração de saída apropriada.
- **TransformationClaimType** é usado para fornecer um nome exclusivo à saída. O nome deve corresponder a uma das saídas esperadas para o método de transformação.

### <a name="exceptions-and-restrictions"></a>Exceções e restrições

**NameID e UPN SAML:** os atributos dos quais você obtém os valores de source NameID e UPN, bem como as transformações de declarações que são permitidas, são limitados. Consulte as tabelas 5 e 6 para ver os valores permitidos.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: atributos permitidos como fonte de dados para NameID SAML

|Origem|ID|Descrição|
|-----|-----|-----|
| User | mail|Endereço de Email|
| User | userprincipalname|Nome UPN|
| User | onpremisessamaccountname|Nome da conta SAM local|
| User | employeeid|ID do funcionário|
| User | extensionattribute1 | Atributo de extensão 1 |
| User | extensionattribute2 | Atributo de extensão 2 |
| User | extensionattribute3 | Atributo de extensão 3 |
| User | extensionattribute4 | Atributo de extensão 4 |
| User | extensionattribute5 | Atributo de extensão 5 |
| User | extensionattribute6 | Atributo de extensão 6 |
| Usuário | extensionattribute7 | Atributo de extensão 7 |
| User | extensionattribute8 | Atributo de extensão 8 |
| User | extensionattribute9 | Atributo de extensão 9 |
| User | extensionattribute10 | Atributo de extensão 10 |
| User | extensionattribute11 | Atributo de extensão 11 |
| User | extensionattribute12 | Atributo de extensão 12 |
| User | extensionattribute13 | Atributo de extensão 13 |
| User | extensionattribute14 | Atributo de extensão 14 |
| User | extensionattribute15 | Atributo de extensão 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: métodos de transformação permitidos para o NameID SAML

| TransformationMethod | Restrições |
| ----- | ----- |
| ExtractMailPrefix | Nenhum |
| Ingressar | O sufixo que está sendo acrescentado deve ser um domínio verificado do locatário do recurso. |

### <a name="custom-signing-key"></a>Chave de assinatura personalizada

Uma chave de assinatura personalizada deve ser atribuída ao objeto de entidade de serviço para que uma política de mapeamento de declarações entre em vigor. Isso garante a confirmação de que os tokens foram modificados pelo criador da política de mapeamento de declarações e protege os aplicativos contra as políticas de mapeamento de declarações criadas por atores mal-intencionados.  Os aplicativos que têm o mapeamento de declarações habilitado devem verificar um URI especial para suas chaves de assinatura `appid={client_id}` de token anexando a suas [solicitações de metadados do OpenID Connect](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document).  

### <a name="cross-tenant-scenarios"></a>Cenários entre locatários

As políticas de mapeamento de declarações não se aplicam a usuários convidados. Se um usuário convidado tentar acessar um aplicativo com uma política de mapeamento de declarações atribuída à sua entidade de serviço, o token padrão será emitido (a política não terá efeito).

## <a name="claims-mapping-policy-assignment"></a>Atribuição de política de mapeamento de declarações

Políticas de mapeamento de declarações podem ser atribuídas somente a objetos de entidade de serviço.

### <a name="example-claims-mapping-policies"></a>Exemplos de políticas de mapeamento de declarações

No Azure AD, muitos cenários são possíveis quando você pode personalizar as declarações emitidas em tokens para entidades de serviço específicas. Nesta seção, percorremos alguns cenários comuns que podem ajudá-lo a entender como usar o tipo de política de mapeamento de declarações.

#### <a name="prerequisites"></a>Pré-requisitos

Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas de entidades de serviço. Se você é um novo usuário do Azure AD, recomendamos que [aprenda como obter um locatário do Azure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.

Para começar, execute uma destas etapas:

1. Baixe a versão mais recente da [Visualização pública do módulo do Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Execute o comando Connect para entrar em sua conta do administrador do Azure AD. Execute esse comando sempre que você iniciar uma nova sessão.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Para ver todas as políticas criadas em sua organização, execute o comando a seguir. É recomendável que você execute esse comando após a maioria das operações nos cenários a seguir, a fim de verificar se as políticas estão sendo criadas conforme o esperado.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para omitir as declarações básicas de tokens emitidos para uma entidade de serviço

Neste exemplo, você cria uma política que remove o conjunto de declarações básicas de tokens emitidos para entidades de serviço vinculadas.

1. Crie uma política de mapeamento de declarações. Essa política, vinculada a entidades de serviço específicas, remove o conjunto de declarações básicas do token.
   1. Para criar a política, execute este comando: 
    
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Para ver a nova política e obter a ObjectId da política, execute o comando a seguir:
    
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribuir a política à entidade de serviço. Você também precisará da ObjectId de sua entidade de serviço.
   1. Para ver todas as entidades de serviço de sua organização, consulte o Microsoft Graph. Ou, no Explorador do Graph do Azure AD, entre em sua conta do Azure AD.
   2. Quando você tiver a ObjectId de sua entidade de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemplo: criar e atribuir uma política para incluir EmployeeID e TenantCountry como declarações em tokens emitidos para uma entidade de serviço

Neste exemplo, você cria uma política que adiciona EmployeeID e TenantCountry a tokens emitidos para entidades de serviço vinculadas. EmployeeID é emitido como o tipo de declaração de nome em tokens SAML e JWTs. TenantCountry é emitido como o tipo de declaração de país em tokens SAML e JWTs. Neste exemplo, continuamos incluindo o conjunto de declarações básicas nos tokens.

1. Crie uma política de mapeamento de declarações. Essa política, vinculada a entidades de serviço específicas, adiciona as declarações de EmployeeID e TenantCountry em tokens.
   1. Para criar a política, execute o seguinte comando:  
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver a nova política e obter a ObjectId da política, execute o comando a seguir:
     
      ``` powershell  
      Get-AzureADPolicy
      ```
1. Atribuir a política à entidade de serviço. Você também precisará da ObjectId de sua entidade de serviço. 
   1. Para ver todas as entidades de serviço de sua organização, consulte o Microsoft Graph. Ou, no Explorador do Graph do Azure AD, entre em sua conta do Azure AD.
   2. Quando você tiver a ObjectId de sua entidade de serviço, execute o seguinte comando:  
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemplo: criar e atribuir uma política que usa uma transformação de declarações em tokens emitidos para uma entidade de serviço

Neste exemplo, você cria uma política que emite uma declaração personalizada “JoinedData” para JWTs emitidos para entidades de serviço vinculadas. Esta declaração contém um valor criado unindo os dados armazenados no atributo extensionattribute1 no objeto do usuário com ".sandbox". Neste exemplo, excluímos o conjunto de declarações básicas nos tokens.

1. Crie uma política de mapeamento de declarações. Essa política, vinculada a entidades de serviço específicas, adiciona as declarações de EmployeeID e TenantCountry em tokens.
   1. Para criar a política, execute o seguinte comando:
     
      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```
    
   2. Para ver a nova política e obter a ObjectId da política, execute o comando a seguir: 
     
      ``` powershell
      Get-AzureADPolicy
      ```
1. Atribuir a política à entidade de serviço. Você também precisará da ObjectId de sua entidade de serviço. 
   1. Para ver todas as entidades de serviço de sua organização, consulte o Microsoft Graph. Ou, no Explorador do Graph do Azure AD, entre em sua conta do Azure AD.
   2. Quando você tiver a ObjectId de sua entidade de serviço, execute o seguinte comando: 
     
      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>Consulte também

Para saber como personalizar as declarações emitidas no token SAML por meio do portal do Azure, [consulte Como: Personalizar declarações emitidas no token SAML para aplicativos empresariais](active-directory-saml-claims-customization.md)