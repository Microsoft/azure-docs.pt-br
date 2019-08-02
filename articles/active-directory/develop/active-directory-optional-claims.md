---
title: Saiba como fornecer declarações opcionais para o aplicativo Azure AD | Microsoft Docs
description: Um guia para adicionar declarações adicionais ou personalizadas aos tokens SAML 2.0 e JSON Web Tokens (JWT) emitidos pelo Azure Active Directory.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b0ec2e1defc4701bff798b2fa93900ec8a9a64
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595152"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Como: Fornecer declarações opcionais para seu aplicativo do AD do Azure

Os desenvolvedores de aplicativos podem usar declarações opcionais em seus aplicativos do Azure AD para especificar quais declarações que eles querem em tokens enviados para o aplicativo. 

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para o aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Para as listas de declarações padrão, consulte a [token de acesso](access-tokens.md) e [id_token](id-tokens.md) documentação de declarações. 

Embora declarações opcionais tenham suporte na v1.0 e v2.0 tokens de formato, bem como os tokens SAML, eles fornecem a maioria de seu valor ao passar da versão 1.0 para v 2.0. Uma das metas do [ponto de extremidade do Microsoft identity platform v2.0](active-directory-appmodel-v2-overview.md) é tamanhos menores de token para garantir um ótimo desempenho pelos clientes. Como resultado, várias declarações anteriormente incluídas em tokens de acesso e ID não estão mais presentes nos tokens v2.0 e devem ser solicitadas especificamente, por aplicativo.

**Tabela 1: Aplicabilidade**

| Tipo de Conta | Tokens v1.0 | Tokens v2.0  |
|--------------|---------------|----------------|
| Conta pessoal da Microsoft  | N/D  | Com suporte |
| Conta do AD do Azure      | Com suporte | Com suporte |

## <a name="v10-and-v20-optional-claims-set"></a>conjunto de declarações opcional v1.0 e v2.0

O conjunto de declarações opcionais disponíveis por padrão para uso pelos aplicativos é listado abaixo. Para adicionar declarações opcionais personalizadas para o aplicativo, confira [Extensões de Diretório](#configuring-directory-extension-optional-claims), abaixo. Ao adicionar declarações para o **token de acesso**, isso se aplicará aos tokens de acesso solicitados *para* o aplicativo (uma API da web), não os *pelo* o aplicativo. Isso garante que, independentemente do cliente acessar a API, os dados corretos estejam presentes no token de acesso que utilizam para autenticarem-se na API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída em JWTs para tokens v1.0 e v2.0, mas não para tokens SAML, exceto quando indicado na coluna Tipo de Token. Contas de consumidor dão suporte a um subconjunto dessas declarações marcadas na coluna "Tipo de usuário".  Muitas das declarações listadas não se aplicam aos usuários do consumidor (eles não têm nenhum locatário, portanto, `tenant_ctry` não tem nenhum valor).  

**Tabela 2: conjunto de declarações de v1.0 e v2.0 opcionais**

| NOME                       |  DESCRIÇÃO   | Tipo de token | Tipo de Usuário | Observações  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Confira especificações de OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `home_oid`                 | Para usuários convidados, a ID de objeto do usuário no locatário inicial do usuário.| JWT        |           | |
| `sid`                      | ID de sessão usada para entrada do usuário por sessão out. | JWT        |  Pessoal e contas do Azure AD.   |         |
| `platf`                    | Plataforma de dispositivos    | JWT        |           | Restrito aos dispositivos gerenciados que podem verificar o tipo de dispositivo.|
| `verified_primary_email`   | Originado de PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado de SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `enfpolids`                | IDs de política aplicada. Uma lista de IDs de política que foram avaliadas para o usuário atual. | JWT |  |  |
| `vnet`                     | Informações de especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | O país do usuário | JWT |  | O Azure AD retorna a declaração opcional `ctry` se ela estiver presente e o valor da declaração é um código de país padrão de duas letras, como FR, JP, SZ e assim por diante. |
| `tenant_ctry`              | País do locatário de recursos | JWT | | |
| `xms_pdl`          | Local dos dados preferido   | JWT | | Para locatários de várias áreas geográficas, este é o código de 3 letras que mostra a região geográfica que o usuário está. Para obter mais informações, consulte o [documentação do Azure AD Connect sobre o local de dados preferencial](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferido do usuário  | JWT ||O idioma preferido do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Tem o formato II-PP ("en-us"). |
| `xms_tpl`                  | Idioma preferido do locatário| JWT | | O idioma preferido do locatário do recurso, se definido. Com o formato II (“en”). |
| `ztdid`                    | ID de implantação de zero toque | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para este usuário, se o usuário tiver um.  | JWT, SAML | MSA, Azure AD | Esse valor é incluído por padrão, se o usuário é um convidado no locatário.  Para usuários gerenciados (aqueles dentro do Locatário), ele deve ser solicitado por meio dessa declaração opcional ou, na versão 2.0 apenas, com o escopo da OpenID.  Para usuários gerenciados, o endereço de email deve ser definido [portal de administração do Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Opcionais de formatação para declarações de grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims na [manifesto do aplicativo](reference-app-manifest.md), que deve ser definido também. Para obter detalhes, consulte [declarações de grupo](#Configuring-group-optional claims) abaixo. Para obter mais informações sobre declarações de grupo consulte [como configurar as declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Status da conta de usuários no locatário. | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se eles forem convidado, o valor é `1`. |
| `upn`                      | Declaração UserPrincipalName. | JWT, SAML  |           | Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais a fim de modificar seu comportamento, no caso do usuário convidado.  |

### <a name="v20-optional-claims"></a>Declarações opcionais v2.0

Essas declarações são sempre incluídas nos tokens do Azure AD v 1.0, mas não incluídas em tokens da v2.0, a menos que solicitado. Essas declarações só são aplicáveis a JWTs (tokens de ID e Tokens de acesso). 

**Tabela 3: somente v2.0 declarações opcionais**

| Declaração JWT     | Nome                            | DESCRIÇÃO                                | Observações |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP com o qual o cliente se conectou.   |       |
| `onprem_sid`  | Identificador de Segurança Local |                                             |       |
| `pwd_exp`     | Hora da Expiração da Senha        | A data e a hora em que a senha expira. |       |
| `pwd_url`     | Alterar URL da Senha             | Uma URL que o usuário pode acessar para alterar sua senha.   |   |
| `in_corp`     | Dentro da Rede Corporativa        | Indica se o cliente está se conectando da rede corporativa. Se não estiver, a declaração não está incluída.   |  Baseado nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) na Autenticação Multifator.    |
| `nickname`    | Apelido                        | Um nome adicional para o usuário, separado do nome ou sobrenome. | 
| `family_name` | Sobrenome                       | Fornece o último nome, sobrenome ou nome da família do usuário conforme definido no objeto de usuário. <br>"family_name":"Barros" | Com suporte no MSA e Azure AD   |
| `given_name`  | Nome                      | Fornece o nome ou o nome "determinado" do usuário, conforme definido no objeto do usuário.<br>"given_name": "Davi"                   | Com suporte no MSA e Azure AD  |
| `upn`         | Nome UPN | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para dados de chave. | Ver [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para a configuração da declaração. |

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar o modo como a declaração é retornada. Essas propriedades adicionais são usadas principalmente para ajudar a migração de aplicativos locais com expectativas de dados diferentes (por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não podem manipular marcas (`#`) no UPN)

**Tabela 4: Valores de configuração para declarações opcionais**

| Nome da propriedade  | Nome de Propriedade Adicional | DESCRIÇÃO |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usada para respostas SAML e JWT e para tokens v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN de convidado conforme armazenado no locatário do recurso. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Igual ao que é indicado acima, exceto que as marcas de hash (`#`) são substituídas por sublinhados (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua outro UPN com o locatário de início adicional e as informações do locatário do recurso. Isso altera apenas a declaração `upn` no token, se o usuário é um convidado no locatário (que usa um IDP diferente para autenticação). 

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Você pode configurar declarações opcionais para o aplicativo modificando o manifesto do aplicativo (veja o exemplo abaixo). Para obter mais informações, consulte o [Noções básicas sobre o artigo de manifesto de aplicativo do Azure AD](reference-app-manifest.md).

> [!IMPORTANT]
> Tokens de acesso **sempre** gerado usando o manifesto do recurso, não no cliente.  Portanto, na solicitação `...scope=https://graph.microsoft.com/user.read...` o recurso é o gráfico.  Portanto, o token de acesso é criado usando o manifesto do gráfico, não o manifesto do cliente.  Alterar o manifesto do aplicativo nunca fará com que tokens para o gráfico para uma aparência diferente.  Para validar que seu `accessToken` alterações estão em vigor, solicitar um token para seu aplicativo, não em outro aplicativo.  

**Esquema de exemplo:**

```json
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": false
             }
      ],
      "accessToken": [
             {
                    "name": "ipaddr", 
                    "essential": false
              }
      ],
      "saml2Token": [
              {
                    "name": "upn", 
                    "essential": false
               },
               {
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar as declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto de declarações opcionais a serem retornadas em cada tipo de token diferente. A propriedade OptionalClaims da entidade do aplicativo é um objeto OptionalClaims.

**Tabela 5: Propriedades do tipo OptionalClaims**

| Nome        | Tipo                       | DESCRIÇÃO                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT. |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.   |

### <a name="optionalclaim-type"></a>Tipo OptionalClaim

Contém uma declaração opcional associada a um aplicativo ou uma entidade de segurança. As propriedades idToken, accessToken e saml2Token do tipo [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) são uma coleção de OptionalClaim.
Caso haja suporte por uma declaração específica, você também poderá modificar o comportamento de OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: Propriedades do tipo OptionalClaim**

| NOME                 | Tipo                    | DESCRIÇÃO                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e definidas pelo usuário de propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for um usuário, o valor na propriedade name será a propriedade de extensão do objeto de usuário. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização sem problemas para a tarefa específica solicitada pelo usuário final. O valor padrão é false.                                                                                                             |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da declaração. Se uma propriedade existir na coleção, ela modificará o comportamento da declaração opcional especificado na propriedade name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configurar declarações opcionais de extensão de diretório

Além do conjunto de declarações opcional padrão, você também pode configurar tokens para incluir extensões de esquema de diretório. Para obter mais informações, consulte [extensões de esquema de diretório](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Esse recurso é útil para anexar informações adicionais do usuário que o aplicativo pode usar; por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário configurou. 

> [!Note]
> - Extensões de esquema de diretório são um recurso somente do AD do Azure, portanto, se o manifesto do seu aplicativo solicitar uma extensão personalizada e um usuário MSA fizer logon no seu aplicativo, essas extensões não serão retornadas.
> - Declarações opcionais de AD do Azure só funcionam com a extensão do AD do Azure e não funciona com a extensão de diretório do Microsoft Graph. Ambas as APIs exigem o `Directory.ReadWriteAll` permissão, que só pode ser consentida por administradores.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Para atributos de extensão, use o nome completo da extensão (no formato: `extension_<appid>_<attributename>`) no manifesto do aplicativo. O `<appid>` deve coincidir com a ID do aplicativo está solicitando a declaração. 

No JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Em tokens SAML, essas declarações serão emitidas com o seguinte formato de URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>Configurar declarações opcionais de grupo

   > [!NOTE]
   > A capacidade de emitir nomes de grupo de usuários e grupos sincronizados do local é a visualização pública.

Esta seção aborda as opções de configuração em declarações opcionais para alterar os atributos de grupo usados em declarações de grupo do objectID do grupo padrão para atributos sincronizados do Active Directory do Windows local.

> [!IMPORTANT]
> Ver [configurar as declarações de grupo para aplicativos com o Azure AD](../hybrid/how-to-connect-fed-group-claims.md) para obter mais detalhes, incluindo avisos importantes para a visualização pública de declarações de grupo de atributos no local.

1. No portal -> Azure Active Directory -> aplicativo registros -> selecione aplicativo -> manifesto

2. Habilitar declarações de associação de grupo, alterando o groupMembershipClaim

   Os valores válidos são:

   - "Tudo"
   - "SecurityGroup"
   - "DistributionList"
   - "DirectoryRole"

   Por exemplo:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Por padrão, de que será emitido ObjectIDs de grupo no grupo valor de declaração.  Para modificar o valor da declaração para conter atributos de grupo local ou para alterar o tipo de declaração para função, use a configuração de OptionalClaims da seguinte maneira:

3. Conjunto de declarações opcionais de configuração de nome de grupo.

   Se você quiser grupos no token para conter os atributos de grupo do AD na seção de declarações opcionais especificam qual declaração opcional de tipo de token deve ser aplicada ao local, o nome da declaração opcional solicitado e outras propriedades desejadas.  Vários tipos de token podem ser listados:

   - Token para o token de ID de OIDC
   - accessToken para o token de acesso OAuth/OIDC
   - Saml2Token para tokens SAML.

   > [!NOTE]
   > O tipo de Saml2Token se aplica a SAML1.1 e SAML 2.0, tokens de formato

   Para cada tipo de token relevante, modifique a declaração de grupos para usar a seção OptionalClaims no manifesto. O esquema de OptionalClaims é da seguinte maneira:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Esquema de declarações opcionais | Valor |
   |----------|-------------|
   | **name:** | Deve ser "grupos" |
   | **Fonte:** | Não usado. Omitir ou especifique null |
   | **essential:** | Não usado. Omitir ou especifique false |
   | **additionalProperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em additionalProperties apenas um dos "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" são necessárias.  Se mais de um estiver presente, o primeiro é usado e quaisquer outros ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de declaração de um grupo de declaração para uma declaração de função, adicione "emit_as_roles" às propriedades adicionais.  Os valores do grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for usado quaisquer funções de aplicativo configurado que o usuário é atribuído será não aparecem na declaração de função

**Exemplos:** Emissão de grupos como nomes de grupo em tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Emitir nomes de grupo a ser retornado em formato netbiosDomain\sAMAccountName como as funções de declaração SAML e Tokens de ID de OIDC:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>Exemplo de declarações opcional

Nesta seção, você pode examinar um cenário para ver como usar o recurso opcional de declarações para o aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade do aplicativo para habilitar e configurar declarações opcionais:
-   Você pode modificar o manifesto do aplicativo. O exemplo a seguir usará esse método para fazer a configuração. Primeiro leia o [documento Noções básicas sobre o manifesto do aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest) para obter uma introdução ao manifesto.
-   Também é possível escrever um aplicativo que usa a [API do Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) para atualizar o aplicativo. A [Referência de tipo complexo e de entidade](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) ne guia de referência da API do Graph pode ajudar você a configurar as declarações opcionais.

**Exemplo:** no exemplo a seguir, você modificará o manifesto do aplicativo para adicionar declarações a tokens de ID, acesso e SAML destinados ao aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Registros de Aplicativo** no lado esquerdo.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e clique nele.
1. Na página do aplicativo, clique em **Manifesto** para abrir o editor de manifesto embutido. 
1. Você pode editar diretamente o manifesto usando esse editor. O manifesto segue o esquema para [Entidade de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)e formata automaticamente o manifesto quando é salvo. Novos elementos serão adicionados para o `OptionalClaims` propriedade.

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
            "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
            "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }

    ```

    Nesse caso, diferentes declarações opcionais foram adicionadas a cada tipo de token que o aplicativo pode receber. Os tokens de ID agora contêm o UPN para usuários federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Os tokens de acesso que outros clientes solicitam para esse aplicativo agora incluirão a declaração auth_time. Agora os tokens SAML contêm a extensão do esquema de diretório skypeId (neste exemplo, a ID de aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor a ID do Skype como `extension_skypeId`.

1. Quando terminar de atualizar o manifesto, clique em **Salvar** para salvar o manifesto

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as declarações padrão fornecidas pelo Azure AD.

- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
