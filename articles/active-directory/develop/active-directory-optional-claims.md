---
title: Fornecer declarações opcionais a aplicativos Azure AD
titleSuffix: Microsoft identity platform
description: Como adicionar declarações personalizadas ou adicionais aos tokens SAML 2,0 e JSON Web tokens (JWT) emitidos pela plataforma de identidade da Microsoft.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 3fd91bb86b32a958344dc366cfcd142bff369c1f
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108137"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Como: fornecer declarações opcionais para seu aplicativo

Os desenvolvedores de aplicativos podem usar declarações opcionais em seus aplicativos Azure AD para especificar quais declarações eles desejam nos tokens enviados ao aplicativo.

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para o aplicativo.
- Altere o comportamento de determinadas declarações que a plataforma de identidade da Microsoft retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Para obter as listas de declarações padrão, confira a documentação de declarações de [token de acesso](access-tokens.md) e [id_token](id-tokens.md).

Embora as declarações opcionais tenham suporte nos tokens de formato v 1.0 e v 2.0, bem como nos tokens SAML, elas têm mais valor quando mudam de v 1.0 para v 2.0. Uma das metas do [ponto de extremidade v2.0 da plataforma de identidade da Microsoft](./v2-overview.md) é obter tamanhos menores de token para garantir um ótimo desempenho dos clientes. Como resultado, várias declarações anteriormente incluídas em tokens de acesso e ID não estão mais presentes nos tokens v2.0 e devem ser solicitadas especificamente, por aplicativo.

**Tabela 1: Aplicabilidade**

| Tipo de Conta               | Tokens v1.0 | Tokens v2.0 |
|----------------------------|-------------|-------------|
| Conta pessoal da Microsoft | N/D         | Com suporte   |
| Conta do AD do Azure           | Com suporte   | Com suporte   |

## <a name="v10-and-v20-optional-claims-set"></a>Conjunto de declarações opcionais v 1.0 e v 2.0

O conjunto de declarações opcionais disponíveis por padrão para uso pelos aplicativos é listado abaixo. Para adicionar declarações opcionais personalizadas para o aplicativo, confira [Extensões de Diretório](#configuring-directory-extension-optional-claims), abaixo. Quando você adicionar declarações ao **token de acesso**, as declarações se aplicarão aos tokens de acesso solicitados *para* o aplicativo (uma API Web) e não as declarações solicitadas *pelo* aplicativo. Não importa como o cliente acessa sua API, os dados corretos estão presentes no token de acesso que é usado para autenticar na sua API.

> [!NOTE]
> A maioria dessas declarações pode ser incluída em JWTs para tokens v1.0 e v2.0, mas não para tokens SAML, exceto quando indicado na coluna Tipo de Token. As contas de consumidor dão suporte a um subconjunto dessas declarações, marcadas na coluna "Tipo de Usuário".  Muitas das declarações listadas não se aplicam aos usuários do consumidor (não têm locatários e, portanto, `tenant_ctry` não tem valor).

**Tabela 2: conjunto de declarações opcionais v 1.0 e v 2.0**

| Nome                       |  Descrição   | Tipo de token | Tipo de Usuário | Observações  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Hora em que o usuário foi autenticado pela última vez. Confira especificações de OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Região do locatário do recurso | JWT        |           | |
| `sid`                      | ID da sessão, usada para saída do usuário por sessão. | JWT        |  Contas pessoais e do Azure AD.   |         |
| `verified_primary_email`   | Originado de PrimaryAuthoritativeEmail do usuário      | JWT        |           |         |
| `verified_secondary_email` | Originado de SecondaryAuthoritativeEmail do usuário   | JWT        |           |        |
| `vnet`                     | Informações de especificador de VNET. | JWT        |           |      |
| `fwd`                      | Endereço IP.| JWT    |   | Adiciona o endereço IPv4 original do cliente solicitante (quando dentro de uma VNET) |
| `ctry`                     | País/região do usuário | JWT |  | O Azure AD retorna a `ctry` declaração opcional se ela estiver presente e o valor do campo for um código de país/região padrão de duas letras, como fr, JP, sz e assim por diante. |
| `tenant_ctry`              | País do locatário do recurso | JWT | | O mesmo que `ctry` Except definido em um nível de locatário por um administrador.  Também deve ser um valor padrão de duas letras. |
| `xms_pdl`             | Local dos dados preferido   | JWT | | Para locatários de várias regiões geográficas, o local de dados preferencial é o código de três letras que mostra a região geográfica em que o usuário está. Para obter mais informações, confira a [documentação do Azure AD Connect sobre o local de dados preferencial](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md).<br/>Por exemplo: `APC` para Pacífico Asiático. |
| `xms_pl`                   | Idioma preferido do usuário  | JWT ||O idioma preferido do usuário, se definido. Originado de seu locatário inicial, em cenários de acesso de convidado. Tem o formato II-PP ("en-us"). |
| `xms_tpl`                  | Idioma preferido do locatário| JWT | | O idioma preferido do locatário do recurso, se definido. Com o formato II (“en”). |
| `ztdid`                    | ID de implantação de zero toque | JWT | | A identidade do dispositivo usada para o [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | O email endereçável para este usuário, se o usuário tiver um.  | JWT, SAML | MSA, Azure AD | Esse valor é incluído por padrão, se o usuário é um convidado no locatário.  Para usuários gerenciados (os usuários dentro do locatário), ele deve ser solicitado por meio dessa declaração opcional ou, somente na versão 2.0, com o escopo do OpenID.  Para usuários gerenciados, o endereço de email deve ser definido [portal de administração do Office](https://portal.office.com/adminportal/home#/users).|
| `acct`                | Status da conta de usuários no locatário | JWT, SAML | | Se o usuário for um membro do locatário, o valor será `0`. Se eles forem convidado, o valor é `1`. |
| `groups`| Formatação opcional para declarações de grupo |JWT, SAML| |Usado em conjunto com a configuração GroupMembershipClaims no [manifesto do aplicativo](reference-app-manifest.md), que também deve ser definido. Para obter detalhes, confira [Declarações de grupo](#configuring-groups-optional-claims) abaixo. Para obter mais informações sobre declarações de grupo, confira [Como configurar declarações de grupo](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT, SAML  |           | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para identificar exclusivamente as informações do usuário (por exemplo, como uma chave de banco de dados). Em vez disso, use a ID de objeto de usuário ( `oid` ) como uma chave de banco de dados. Os usuários que se conectam com uma [ID de logon alternativa](../authentication/howto-authentication-use-email-signin.md) não devem ser mostrados no nome UPN. Em vez disso, use as seguintes declarações de token de ID para exibir o estado de entrada para o usuário: `preferred_username` ou `unique_name` para tokens v1 e `preferred_username` para tokens v2. Embora essa declaração seja incluída automaticamente, você pode especificá-la como uma declaração opcional para anexar propriedades adicionais a fim de modificar seu comportamento, no caso do usuário convidado.  |
| `idtyp`                    | Tipo de token   | Tokens de acesso JWT | Especial: somente em tokens de acesso somente de aplicativo |  Valor é `app` quando o token é um token somente de aplicativo. Essa é a maneira mais precisa para uma API determinar se um token é um token de aplicativo ou um token de aplicativo + usuário.|

## <a name="v20-specific-optional-claims-set"></a>Conjunto de declarações opcionais específicas v2.0

Essas declarações são sempre incluídas em tokens do Azure AD v1.0, mas não em tokens da v2.0, a menos que solicitado. Essas declarações só são aplicáveis a JWTs (tokens de ID e Tokens de Acesso).

**Tabela 3: somente declarações V2.0 opcionais**

| Declaração JWT     | Nome                            | Descrição                                | Observações |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Endereço IP                      | O endereço IP com o qual o cliente se conectou.   |       |
| `onprem_sid`  | Identificador de Segurança Local |                                             |       |
| `pwd_exp`     | Hora da Expiração da Senha        | A data e a hora em que a senha expira. |       |
| `pwd_url`     | Alterar URL da Senha             | Uma URL que o usuário pode acessar para alterar sua senha.   |   |
| `in_corp`     | Dentro da Rede Corporativa        | Indica se o cliente está se conectando da rede corporativa. Se não estiver, a declaração não será incluída.   |  Baseado nas configurações de [IPs confiáveis](../authentication/howto-mfa-mfasettings.md#trusted-ips) na Autenticação Multifator.    |
| `family_name` | Sobrenome                       | Fornece o sobrenome do usuário, conforme definido no objeto do usuário. <br>"family_name":"Barros" | Com suporte na MSA e no Azure AD. Requer o escopo de `profile`.   |
| `given_name`  | Nome                      | Fornece o nome ou nome “especificado” do usuário, conforme definido no objeto de usuário.<br>"given_name": "Davi"                   | Com suporte na MSA e no Azure AD.  Requer o escopo de `profile`. |
| `upn`         | Nome UPN | Um identificador para o usuário que pode ser usado com o parâmetro username_hint.  Não é um identificador durável para o usuário e não deve ser usado para identificar exclusivamente as informações do usuário (por exemplo, como uma chave de banco de dados). Em vez disso, use a ID de objeto de usuário ( `oid` ) como uma chave de banco de dados. Os usuários que se conectam com uma [ID de logon alternativa](../authentication/howto-authentication-use-email-signin.md) não devem ser mostrados no nome UPN. Em vez disso, use as seguintes declarações de token de ID para exibir o estado de entrada para o usuário: `preferred_username` ou `unique_name` para tokens v1 e `preferred_username` para tokens v2. | Ver [propriedades adicionais](#additional-properties-of-optional-claims) abaixo para a configuração da declaração. Requer o escopo de `profile`.|

### <a name="additional-properties-of-optional-claims"></a>Propriedades adicionais de declarações opcionais

Algumas declarações opcionais podem ser configuradas para alterar o modo como a declaração é retornada. Essas propriedades adicionais são usadas principalmente para ajudar na migração de aplicativos locais com expectativas de dados diferentes. Por exemplo, `include_externally_authenticated_upn_without_hash` ajuda com clientes que não podem manipular marcas de hash ( `#` ) no UPN.

**Tabela 4: Valores de configuração para declarações opcionais**

| Nome da propriedade  | Nome de Propriedade Adicional | Descrição |
|----------------|--------------------------|-------------|
| `upn`          |                          | Pode ser usada para respostas SAML e JWT e para tokens v1.0 e v2.0. |
|                | `include_externally_authenticated_upn`  | Inclui o UPN de convidado conforme armazenado no locatário do recurso. Por exemplo, `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Igual ao que é indicado acima, exceto que as marcas de hash (`#`) são substituídas por sublinhados (`_`), por exemplo `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Exemplo de propriedades adicionais

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Esse objeto OptionalClaims faz com que o token de ID retornado ao cliente inclua uma `upn` declaração com as informações adicionais de locatário de recurso e locatário doméstico. A declaração de `upn` somente é alterada no token se o usuário é um convidado no locatário (que usa um IDP diferente para autenticação).

## <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

> [!IMPORTANT]
> Os tokens de acesso são **sempre** gerados com o manifesto do recurso, não do cliente.  Portanto, na solicitação `...scope=https://graph.microsoft.com/user.read...` o recurso é a API do Microsoft Graph.  Assim, o token de acesso é criado com o manifesto da API do Microsoft Graph, não com o manifesto do cliente.  A alteração do manifesto do seu aplicativo nunca fará com que os tokens da API do Microsoft Graph fiquem diferentes.  Para validar que as alterações de `accessToken` estão em vigor, solicite um token para seu aplicativo, não para outro.

Você pode configurar declarações opcionais para seu aplicativo por meio da interface do usuário ou do manifesto do aplicativo.

1. Vá para o [Portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar**, escolha **Registros de aplicativo**.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.

**Configurando declarações opcionais por meio da interface do usuário:**

[![Configurar declarações opcionais na interface do usuário](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. Na seção **Gerenciar**, selecione **Configuração do token**.
1. Escolha **Adicionar declaração opcional**.
1. Escolha o tipo de token que você deseja configurar.
1. Escolha as declarações opcionais a serem adicionadas.
1. Selecione **Adicionar**.

> [!NOTE]
> A folha de **configuração do token** de opção de interface do usuário não está disponível para aplicativos registrados em um locatário Azure ad B2C no momento. Para aplicativos registrados em um locatário B2C, as declarações opcionais podem ser configuradas modificando o manifesto do aplicativo. Para obter mais informações [, consulte Adicionar declarações e personalizar a entrada do usuário usando políticas personalizadas no Azure Active Directory B2C](../../active-directory-b2c/configure-user-input.md) 

**Configurando declarações opcionais por meio do manifesto do aplicativo:**

[![mostra como configurar declarações opcionais usando o manifesto do aplicativo](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. Na seção **Gerenciar**, selecione **Manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto. Opcionalmente, você pode selecionar **Baixar** e editar o manifesto localmente e, em seguida, usar **Carregar** para reaplicá-lo ao seu aplicativo. Para obter mais informações sobre o manifesto do aplicativo, confira o [artigo Noções básicas do manifesto do aplicativo do Azure AD](reference-app-manifest.md).

    A entrada de manifesto de aplicativo a seguir adiciona as declarações opcionais auth_time, ipaddr e upn aos tokens de ID, acesso e SAML.

    ```json
    "optionalClaims": {
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

2. Quando terminar, selecione **Avançar**. Agora, as declarações opcionais especificadas serão incluídas nos tokens para seu aplicativo.


### <a name="optionalclaims-type"></a>Tipo OptionalClaims

Declara as declarações opcionais solicitadas por um aplicativo. Um aplicativo pode configurar declarações opcionais a serem retornadas em cada um dos três tipos de tokens (token de ID, token de acesso, token SAML 2) que pode receber do serviço de token de segurança. O aplicativo pode configurar um conjunto de declarações opcionais a serem retornadas em cada tipo de token diferente. A propriedade OptionalClaims da entidade do aplicativo é um objeto OptionalClaims.

**Tabela 5: Propriedades do tipo OptionalClaims**

| Nome          | Tipo                       | Descrição                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de ID JWT.     |
| `accessToken` | Coleção (OptionalClaim) | As declarações opcionais retornadas no token de acesso JWT. |
| `saml2Token`  | Coleção (OptionalClaim) | As declarações opcionais retornadas no token SAML.       |

### <a name="optionalclaim-type"></a>Tipo OptionalClaim

Contém uma declaração opcional associada a um aplicativo ou uma entidade de segurança. As propriedades idToken, accessToken e saml2Token do tipo [OptionalClaims](/graph/api/resources/optionalclaims) são uma coleção de OptionalClaim.
Caso haja suporte por uma declaração específica, você também poderá modificar o comportamento de OptionalClaim usando o campo AdditionalProperties.

**Tabela 6: Propriedades do tipo OptionalClaim**

| Nome                   | Tipo                    | Descrição                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | O nome da declaração opcional.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | A origem (objeto de diretório) da declaração. Há declarações predefinidas e definidas pelo usuário de propriedades de extensão. Se o valor de origem for nulo, a declaração será uma declaração opcional predefinida. Se o valor de origem for um usuário, o valor na propriedade name será a propriedade de extensão do objeto de usuário. |
| `essential`            | Edm.Boolean             | Se o valor for true, a declaração especificada pelo cliente será necessária para garantir uma experiência de autorização sem problemas para a tarefa específica solicitada pelo usuário final. O valor padrão é false.                                                                                                                 |
| `additionalProperties` | Coleção (Edm.String) | Propriedades adicionais da declaração. Se uma propriedade existir na coleção, ela modificará o comportamento da declaração opcional especificado na propriedade name.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Configurando declarações opcionais de extensão de diretório

Além do conjunto de declarações opcionais padrão, você também pode configurar tokens para incluir extensões. Para obter mais informações, confira a [documentação extensionProperty do Microsoft Graph](/graph/api/resources/extensionproperty).

As declarações opcionais não dão suporte a esquema e extensões abertas, somente a extensões de diretório do estilo AAD-Graph. Esse recurso é útil para anexar informações adicionais do usuário que o aplicativo pode usar; por exemplo, um identificador adicional ou uma opção de configuração importante que o usuário configurou. Consulte o final desta página para obter um exemplo.

> [!NOTE]
> As extensões de esquema de diretório são um recurso exclusivo do Azure AD. Se o manifesto do aplicativo solicitar uma extensão personalizada e um usuário da MSA fizer logon em seu aplicativo, essas extensões não serão retornadas.

### <a name="directory-extension-formatting"></a>Formatação de extensão de diretório

Ao configurar declarações opcionais de extensão de diretório com o manifesto do aplicativo, use o nome completo da extensão (no formato: `extension_<appid>_<attributename>`). O `<appid>` precisa corresponder ao ID do aplicativo que está solicitando a declaração.

No JWT, essas declarações serão emitidas com o seguinte formato de nome: `extn.<attributename>`.

Em tokens SAML, essas declarações serão emitidas com o seguinte formato de URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Configurando declarações opcionais de grupos

   > [!NOTE]
   > A capacidade de emitir nomes de grupo para usuários e grupos sincronizados localmente está em Visualização Pública.

Esta seção aborda as opções de configuração em declarações opcionais para alterar os atributos de grupo usados em declarações de grupo do objectID do grupo padrão para os atributos sincronizados do Microsoft Active Directory local. Você pode configurar declarações opcionais de grupos para seu aplicativo por meio da interface do usuário ou do manifesto do aplicativo.

> [!IMPORTANT]
> Para obter mais detalhes, incluindo avisos importantes sobre a visualização pública de declarações de grupo de atributos locais, confira [Configurar declarações de grupo para aplicativos com o Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Configurar declarações opcionais de grupos por meio da interface do usuário:**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Na seção **Gerenciar**, escolha **Registros de aplicativo**.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.
1. Na seção **Gerenciar**, selecione **Configuração de token**.
1. Selecione **Adicionar grupos declaração**.
1. Selecione os tipos de grupo a serem retornados (**grupos de segurança** ou **funções de diretório**, **todos os grupos** e/ou **grupos atribuídos ao aplicativo**). Os **grupos atribuídos à opção de aplicativo** incluem apenas grupos atribuídos ao aplicativo. A opção **todos os grupos** inclui o grupo de **segurança**, **DirectoryRole** e **DistributionList**, mas não **os grupos atribuídos ao aplicativo**. 
1. Opcional: selecione as propriedades do tipo de token específico para modificar o valor da declaração grupos para conter os atributos do grupo local ou para alterar o tipo de declaração para uma função.
1. Clique em **Salvar**.

**Configurando declarações opcionais de grupos por meio do manifesto do aplicativo:**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.
1. Na seção **gerenciar** , selecione **manifesto**.
1. Adicione a seguinte entrada usando o editor de manifesto:

   Os valores válidos são:

   - "Todos" (essa opção inclui o SecurityGroup, DirectoryRole e DistributionList)
   - “SecurityGroup”
   - “DirectoryRole”
   - "Grupo de aplicativos" (essa opção inclui apenas grupos que são atribuídos ao aplicativo)

   Por exemplo:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Por padrão, as ObjectIDs de grupo serão emitidas no valor da declaração de grupo.  Para modificar o valor da declaração e conter os atributos do grupo local ou para alterar o tipo de declaração para função, use a configuração OptionalClaims da seguinte maneira:

1. Definir declarações opcionais de configuração de nome de grupo.

   Se você quiser que os grupos no token contenham os atributos do grupo do AD local na seção declarações opcionais, especifique a qual tipo de token a declaração opcional deve ser aplicada, o nome da declaração opcional solicitada e todas as propriedades adicionais desejadas.  Vários tipos de token podem ser listados:

   - idToken para o token de ID de OIDC
   - accessToken para o token de acesso OAuth
   - Saml2Token para tokens SAML.

   > [!NOTE]
   > O tipo Saml2Token aplica-se aos tokens de formato SAML 1.1 e SAML 2.0.

   Para cada tipo de token relevante, modifique a declaração de grupos para usar a seção OptionalClaims no manifesto. O esquema OptionalClaims é o seguinte:

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
   | **name:** | Precisa ser "grupos" |
   | **source:** | Não usado. Omitir ou especificar null |
   | **essential:** | Não usado. Omitir ou especificar false |
   | **additionalProperties:** | Lista de propriedades adicionais.  As opções válidas são "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   Em additionalProperties, é necessário somente um dentre "sam_account_name", "dns_domain_and_sam_account_name" e "netbios_domain_and_sam_account_name".  Se mais de um estiver presente, o primeiro será usado e os outros serão ignorados.

   Alguns aplicativos exigem informações de grupo sobre o usuário na declaração de função.  Para alterar o tipo de declaração de uma declaração de grupo para uma declaração de função, adicione "emit_as_roles" a propriedades adicionais.  Os valores de grupo serão emitidos na declaração de função.

   > [!NOTE]
   > Se "emit_as_roles" for usado, as funções de aplicativo configuradas que o usuário está atribuído não aparecerão na declaração de função.

**Exemplos:**

1) Emitir grupos como nomes de grupo nos tokens de acesso OAuth no formato dnsDomainName\sAMAccountName

    **Configuração da interface do usuário:**

    [![Configurar declarações opcionais](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Arquivo de manifesto do aplicativo:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Emitir nomes de grupos a serem retornados no formato netbiosDomain\sAMAccountName como a declaração de funções em tokens de ID SAML e OIDC

    **Configuração da interface do usuário:**

    [![Declarações opcionais no manifesto](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Arquivo de manifesto do aplicativo:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Exemplo de declarações opcional

Nesta seção, você pode examinar um cenário para ver como usar o recurso opcional de declarações para o aplicativo.
Há várias opções disponíveis para atualizar as propriedades na configuração de identidade do aplicativo para habilitar e configurar declarações opcionais:

- Você pode usar a interface do usuário **Configuração de token** (confira o exemplo abaixo)
- Você pode usar o **Manifesto** (confira o exemplo abaixo). Primeiro leia o [documento Noções básicas sobre o manifesto do aplicativo Azure AD](./reference-app-manifest.md) para obter uma introdução ao manifesto.
- Também é possível escrever um aplicativo que usa a [API do Microsoft Graph](/graph/use-the-api) para atualizar o aplicativo. O tipo [OptionalClaims](/graph/api/resources/optionalclaims) no guia de referência da API do Microsoft Graph pode ajudá-lo a configurar as declarações opcionais.

**Exemplo:**

No exemplo a seguir, você usará a interface do usuário **Configuração de token** e o **Manifesto** para adicionar declarações opcionais aos tokens de acesso, de ID e SAML destinados ao seu aplicativo. Diferentes declarações opcionais serão adicionadas a cada tipo de token que o aplicativo pode receber:

- Os tokens de ID agora contêm o UPN para usuários federados no formato completo (`<upn>_<homedomain>#EXT#@<resourcedomain>`).
- Os tokens de acesso que outros clientes solicitam para esse aplicativo agora incluirão a declaração auth_time.
- Agora os tokens SAML contêm a extensão do esquema de diretório skypeId (neste exemplo, a ID de aplicativo para esse aplicativo é ab603c56068041afb2f6832e2a17e237). Os tokens SAML vão expor a ID do Skype como `extension_skypeId`.

**Configuração da interface do usuário:**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.

1. Selecione **Azure Active Directory** no menu à esquerda.

1. Na seção **Gerenciar**, escolha **Registros de aplicativo**.

1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e selecione-o.

1. Na seção **Gerenciar**, selecione **Configuração de token**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token **ID**, selecione **UPN** na lista de declarações e selecione **Adicionar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token **Acesso**, selecione **auth_time** na lista de declarações e selecione **Adicionar**.

1. Na tela Visão geral da configuração do token, selecione o ícone de lápis ao lado de **UPN**, selecione a opção **Autenticado externamente** e selecione **Salvar**.

1. Selecione **Adicionar declaração opcional**, selecione o tipo de token **SAML**, selecione **extn.skypeID** na lista de declarações (aplicável somente se você tiver criado um objeto de usuário do Azure AD chamado SkypeID) e selecione **Adicionar**.

    [![Declarações opcionais para token SAML](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Configuração do manifesto:**

1. Entre no [portal do Azure](https://portal.azure.com).
1. Depois de autenticado, escolha o locatário do Azure AD selecionando-o no canto superior direito da página.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Localize o aplicativo para o qual você deseja configurar declarações opcionais na lista e selecione-o.
1. Na seção **Gerenciar**, selecione **Manifesto** para abrir o editor de manifesto embutido.
1. Você pode editar diretamente o manifesto usando esse editor. O manifesto segue o esquema para [Entidade de aplicativo](./reference-app-manifest.md)e formata automaticamente o manifesto quando é salvo. Novos elementos serão adicionados para o `OptionalClaims` propriedade.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
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

1. Quando terminar de atualizar o manifesto, selecione **Salvar** para salvar o manifesto.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as declarações padrão fornecidas pelo Azure AD.

- [Tokens de ID](id-tokens.md)
- [Tokens de acesso](access-tokens.md)
