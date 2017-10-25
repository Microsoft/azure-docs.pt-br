---
title: "Tempos de vida de token configuráveis no Azure Active Directory | Microsoft Docs"
description: Saiba como definir tempos de vida dos tokens emitidos pelo Azure AD.
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: billmath
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: d23721eba308096a05211eb6e26e1338a69cae0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Tempos de vida de token configuráveis no Azure Active Directory (Visualização Pública)
Especifique o tempo de vida de um token emitido pelo Azure Active Directory (Azure AD). Configure os tempos de vida de token de todos os aplicativos em uma organização, para um aplicativo multilocatário (várias organizações) ou para uma entidade de serviço específica em sua organização.

> [!NOTE]
> Esse recurso está atualmente em Visualização Pública. Esteja preparado para reverter ou remover quaisquer alterações. O recurso está disponível em qualquer assinatura do Azure Active Directory durante a Visualização Pública. No entanto, quando o recurso for disponibilizado para todos, alguns aspectos dele poderão exigir uma assinatura do [Azure Active Directory Premium](active-directory-get-started-premium.md).
>
>

No Azure AD, um objeto de política representa um conjunto de regras aplicadas a todos os aplicativos ou a aplicativos individuais em uma organização. Cada tipo de política tem uma estrutura exclusiva com um conjunto de propriedades que são aplicadas aos objetos aos quais são atribuídas.

Designe uma política como a padrão para sua organização. Essa política é aplicada a qualquer aplicativo na organização, desde que não seja substituída por uma política com uma prioridade mais alta. Você também pode atribuir uma política para aplicativos específicos. A ordem de prioridade varia por tipo de política.


## <a name="token-types"></a>Tipos de token

Configure as políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID.

### <a name="access-tokens"></a>Tokens de acesso
Os clientes usam tokens de acesso para acessar um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de usuário, cliente e recurso. Tokens de acesso não podem ser revogados e são válidos até sua expiração. Um ator mal-intencionado que tenha obtido um token de acesso pode usá-lo pela extensão do tempo de vida. Ajustar o tempo de vida do token de acesso é uma compensação entre a melhorar o desempenho do sistema e aumentar o tempo pelo qual o cliente retém acesso depois que a conta do usuário é desabilitada. Um melhor desempenho do sistema é obtido, reduzindo o número de vezes que um cliente precisa adquirir um novo token de acesso.

### <a name="refresh-tokens"></a>Tokens de atualização
Quando um cliente adquire um token de acesso para acessar um recurso protegido, ele recebe um token de atualização e um token de acesso. O token de atualização é usado para obter novos pares de tokens de acesso/atualização quando o token de acesso atual expira. Um token de atualização é associado a uma combinação de cliente e usuário. Um token de atualização pode ser revogado, e validade do token é verificada sempre que ele é usado.

É importante fazer uma distinção entre clientes públicos e clientes confidenciais. Para saber mais sobre os tipos diferentes de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente confidencial
Clientes confidenciais são aplicativos que podem armazenar com segurança uma senha (segredo) do cliente. Eles podem provar que as solicitações são provenientes do aplicativo cliente, e não de um ator mal-intencionado. Por exemplo, um aplicativo Web é um cliente confidencial, pois pode armazenar um segredo do cliente no servidor Web. Ele não fica exposto. Como esses fluxos são mais seguros, os tempos de vida padrão de tokens de atualização emitidos para esses fluxos são `until-revoked`, não podem ser alterados usando uma política e não serão revogados em redefinições de senha voluntárias.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente público

Clientes públicos não são capazes de armazenar com segurança a senha (segredo) de um cliente. Por exemplo, um aplicativo iOS/Android não pode ocultar um segredo do proprietário do recurso e, portanto, é considerado um cliente público. Defina políticas em recursos para impedir que tokens de atualização de clientes públicos mais antigos do que um período especificado obtenham um novo par de tokens de acesso/atualização. (Para fazer isso, use a propriedade Tempo Máximo Inativo do Token de Atualização.) Use também políticas para definir um período além do qual os tokens de atualização não serão mais aceitos. (Para fazer isso, use a propriedade Idade Máxima do Token de Atualização.) Ajuste o tempo de vida de um token de atualização para controlar quando e com que frequência o usuário precisa reinserir as credenciais, em vez de ser autenticado novamente de forma silenciosa ao usar um aplicativo cliente público.

### <a name="id-tokens"></a>Tokens de ID
Tokens de ID são passados para sites e clientes nativos. Os tokens de ID contêm informações de perfil sobre um usuário. Um token de ID é associado a uma combinação específica de cliente e usuário. Os tokens de ID são considerados válidos até a expiração. Normalmente, um aplicativo Web corresponde o tempo de vida de sessão de um usuário no aplicativo ao tempo de vida do token de ID emitido para o usuário. Ajuste o tempo de vida de um token de ID para controlar com que frequência o aplicativo Web expira a sessão do aplicativo, com que frequência exige que o usuário seja autenticado novamente no Azure AD (de forma silenciosa ou interativa).

### <a name="single-sign-on-session-tokens"></a>Tokens de sessão de logon único
Quando um usuário autentica no Azure AD e marca a caixa de seleção **Manter-me conectado**, uma sessão de SSO (logon único) é estabelecida com o navegador do usuário e o Azure AD. O Token de SSO, na forma de um cookie, representa essa sessão. Observe que o token de sessão de SSO não está associado a um aplicativo cliente/recurso específico. Tokens de sessão de SSO podem ser revogados, e sua validade é verificada sempre que eles são usados.

O Azure AD usa dois tipos de tokens de sessão de SSO: persistente e não persistente. Tokens de sessão persistentes são armazenadas como cookies persistentes pelo navegador. Tokens de sessão não persistentes são armazenados como cookies de sessão. (Cookies de sessão são destruídos quando o navegador é fechado).

Tokens de sessão não persistentes têm uma vida útil de 24 horas. Tokens persistentes têm um tempo de vida de 180 dias. Sempre que um token de sessão de SSO for usado dentro do período de validade, o período de validade será estendido por mais 24 horas ou 180 dias, dependendo do tipo de token. Se o token de sessão de SSO não for usado dentro do período de validade, ele será considerado expirado e não será mais aceito.

Use uma política para definir o tempo limite após a emissão do primeiro token de sessão além do qual o token de sessão não será mais aceito. (Para fazer isso, use a propriedade Idade Máxima de Token de Sessão.) Ajuste o tempo de vida de um token de sessão para controlar quando e com que frequência o usuário precisa reinserir as credenciais, em vez de ser autenticado de forma silenciosa, ao usar um aplicativo Web.

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de tempo de vida de token
Uma política de tempo de vida do token é um tipo de objeto de política que contém regras de tempo de vida do token. Use as propriedades da política para controlar tempos de vida de tokens especificados. Se nenhuma política for definida, o sistema aplicará o valor de tempo de vida padrão.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de tempo de vida de token configurável
| Propriedade | Cadeia de caracteres de propriedade de política | Afeta | Padrão | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Tempo de Vida do Token de Acesso |AccessTokenLifetime |Tokens de acesso, tokens de ID, tokens SAML2 |1 hora |10 minutos |1 dia |
| Tempo Máximo Inativo de Token de Atualização |MaxInactiveTime |Tokens de atualização |14 dias |10 minutos |90 dias |
| Idade Máxima de Token de Atualização de Fator Único |MaxAgeSingleFactor |Tokens de atualização (para quaisquer usuários) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Atualização Multifator |MaxAgeMultiFactor |Tokens de atualização (para quaisquer usuários) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Sessão de Fator Único |MaxAgeSessionSingleFactor<sup>2</sup> |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Sessão Multifator |MaxAgeSessionMultiFactor<sup>3</sup> |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento máximo explícito que pode ser definido para esses atributos.
* <sup>2</sup>Se **MaxAgeSessionSingleFactor** não for definido, esse valor usará o valor de **MaxAgeSingleFactor**. Se nenhum parâmetro for definido, a propriedade usará o valor padrão (until-revoked).
* <sup>3</sup>Se **MaxAgeSessionMultiFactor**, não for definido, esse valor usará o valor de **MaxAgeMultiFactor**. Se nenhum parâmetro for definido, a propriedade usará o valor padrão (until-revoked).

### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Padrão |
| --- | --- | --- |
| Idade Máxima dos Tokens de Atualização (emitidos para usuários federados com informações de revogação insuficientes<sup>1</sup>) |Tokens de atualização (emitidos para usuários federados com informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Tempo Máximo Inativo do Token de Atualização (emitido para clientes confidenciais) |Tokens de atualização (emitido para clientes confidenciais) |90 dias |
| Idade Máxima do Token de Atualização (emitido para clientes confidenciais) |Tokens de atualização (emitido para clientes confidenciais) |Until-revoked |

* <sup>1</sup>Os usuários federados com informações de revogação insuficientes incluem todos os usuários que não têm o atributo "LastPasswordChangeTimestamp" sincronizado. Esses usuários recebem essa breve Idade Máxima porque o AAD não é capaz de verificar quando revogar tokens vinculados a uma credencial antiga (como uma senha que foi alterada) e devem verificar novamente, com mais frequência, para garantir que o usuário e tokens associados estejam válidos ainda. Para melhorar essa experiência, os administradores de locatário devem garantir que estejam sincronizando o atributo "LastPasswordChangeTimestamp" (isso pode ser definido no objeto do usuário usando o Powershell ou por meio do AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Avaliação e priorização de política
Crie e atribua uma política de tempo de vida de token para um aplicativo específico, para sua organização e para entidades de serviço. Várias políticas podem se aplicar a um aplicativo específico. A política de tempo de vida do token que entra em vigor segue estas regras:

* Se uma política for atribuída explicitamente à entidade de serviço, ela será imposta.
* Se nenhuma política for explicitamente atribuída à entidade de serviço, uma política explicitamente atribuída à organização pai da entidade de serviço será imposta.
* Se nenhuma política foi atribuída explicitamente à entidade de serviço ou à organização, a política atribuída ao aplicativo será imposta.
* Se nenhuma política tiver sido atribuída à entidade de serviço, à organização ou ao objeto de aplicativo, os valores padrão serão aplicados. (Consulte a tabela em [Propriedades configuráveis de tempo de vida de token](#configurable-token-lifetime-properties).)

Para saber mais sobre a relação entre objetos de aplicativo e de entidade de serviço, confira [Objetos de aplicativos e entidade de serviço no Azure Active Directory](active-directory-application-objects.md).

A validade do token é avaliada no momento em que ele é usado. A política com a prioridade mais alta no aplicativo que está sendo acessado entra em vigor.

> [!NOTE]
> Veja um exemplo de cenários.
>
> Um usuário deseja acessar dois aplicativos Web: Aplicativo Web A e Aplicativo Web B.
> 
> Fatores:
> * Os dois aplicativos Web estão na mesma organização pai.
> * Política de tempo de vida de token 1 com uma Idade Máxima do Token de Sessão de oito horas é definida como o padrão da organização pai.
> * O Aplicativo Web A é um aplicativo de uso normal e não está vinculado a políticas.
> * O Aplicativo Web B é usado para processos altamente confidenciais. Essa entidade de serviço é vinculada à Política de tempo de vida de token 2, que tem uma Idade Máxima de Token de Sessão de 30 minutos.
>
> Às 12h, o usuário inicia uma nova sessão do navegador e tenta acessar o Aplicativo Web A. O usuário é redirecionado para o Azure AD e recebe uma solicitação para entrar. Isso crie um cookie com um token de sessão no navegador. O usuário é redirecionado ao Aplicativo Web A com um token de ID que permite e ele acessar o aplicativo.
>
> Às 12h15, o usuário tentar acessar o Aplicativo Web B. O navegador é redirecionado para o Azure AD, que detecta o cookie de sessão. A entidade de serviço do Aplicativo Web B está vinculada à Política de tempo de vida de token 2, mas também faz parte da organização pai, com a Política de tempo de vida de token 1 padrão. A Política de tempo de vida de token 2 entra em vigor, pois as políticas vinculadas a entidades de serviço têm uma prioridade maior do que as políticas padrão de organização. O token de sessão foi originalmente emitido nos últimos 30 minutos. Portanto, ele é considerado válido. O usuário é redirecionado para o Aplicativo Web B com um token de ID que lhe concede acesso.
>
> Às 13h, o usuário tenta acessar o Aplicativo Web B. O usuário é redirecionado ao Azure AD. O Aplicativo Web A não está vinculado a qualquer política, mas como está em uma organização com a Política de tempo de vida de token 1 padrão, essa política entra em vigor. O cookie de sessão que foi originalmente emitido nas últimas oito horas é detectado. O usuário é redirecionado silenciosamente de volta ao Aplicativo Web A com um novo token de ID. O usuário não precisa autenticar.
>
> Imediatamente após isso, o usuário tenta acessar o Aplicativo Web B. O usuário é redirecionado ao Azure AD. Como antes, a Política de tempo de vida de token 2 entra em vigor. Como o token foi emitido há mais de 30 minutos, o usuário recebe uma solicitação para reinserir suas credenciais de logon. Um token de sessão e um token de ID totalmente novos são emitidos. Assim, o usuário pode acessar o Aplicativo Web B.
>
>

## <a name="configurable-policy-property-details"></a>Detalhes configuráveis da propriedade da política
### <a name="access-token-lifetime"></a>Tempo de Vida do Token de Acesso
**Cadeia de caracteres:** AccessTokenLifetime

**Afeta:** tokens de acesso, tokens de ID

**Resumo:** essa política controla por quanto tempo tokens de acesso e ID para esse recurso são considerados válidos. A redução da propriedade de Tempo de vida útil do token acesso minimiza o risco de uso de um token de acesso ou de ID por um ator mal-intencionado durante um longo período. (Esses tokens não podem ser revogados.) A desvantagem é que o desempenho é afetado negativamente, pois os tokens precisam ser substituído com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Tempo Máximo Inativo de Token de Atualização
**Cadeia de caracteres:** MaxInactiveTime

**Afeta:** tokens de atualização

**Resumo:** essa política controla a idade que um token de atualização pode ter antes que um cliente não possa mais usá-lo para recuperar um novo par de tokens de atualização/acesso ao tentar acessar esse recurso. Como um novo token de atualização normalmente retorna quando um token de atualização é usado, essa política impede o acesso se o cliente tentar acessar qualquer recurso usando o token de atualização atual durante o período especificado de tempo.

Essa política força os usuários que não estão ativos no cliente a se autenticarem novamente para recuperar um novo token de atualização.

A propriedade Tempo Máximo Inativo do Token de Atualização deve ser definida como um valor menor do que as propriedades Idade Máxima do Token de Fator Único e Idade Máxima de Token de Atualização multifator.

### <a name="single-factor-refresh-token-max-age"></a>Idade Máxima de Token de Atualização de Fator Único
**Cadeia de caracteres:** MaxAgeSingleFactor

**Afeta:** tokens de atualização

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de atualização para obter um novo par de tokens de acesso/atualização após a última autenticação bem-sucedida usando apenas um único fator. Depois que um usuário autenticar e receber um novo token de atualização, ele poderá usar o fluxo do token de atualização para o período especificado. (Isso será verdadeiro desde que o token de atualização atual não seja revogado, e não fique sem uso por mais tempo do que o tempo de inatividade.) Nesse ponto, o usuário é forçado a se autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou menor do que a propriedade Idade Máxima de Token de Atualização Multifator.

### <a name="multi-factor-refresh-token-max-age"></a>Idade Máxima de Token de Atualização Multifator
**Cadeia de caracteres:** MaxAgeMultiFactor

**Afeta:** tokens de atualização

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de atualização para obter um novo par de tokens de acesso/atualização após a última autenticação bem-sucedida múltiplos fatores. Depois que um usuário autenticar e receber um novo token de atualização, ele poderá usar o fluxo do token de atualização para o período especificado. (Isso será verdadeiro desde que o token de atualização atual não seja revogado, e não fique sem uso por mais tempo do que o tempo de inatividade.) Nesse ponto, os usuários são forçados a autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou maior do que a propriedade Idade Máxima de Token de Atualização de Fator Único.

### <a name="single-factor-session-token-max-age"></a>Idade Máxima de Token de Sessão de Fator Único
**Cadeia de caracteres:** MaxAgeSessionSingleFactor

**Afeta:** tokens de sessão (persistentes e não persistentes)

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de sessão para obter uma nova ID e token de sessão após a última autenticação bem-sucedida usando apenas um único fator. Depois que um usuário autenticar e receber um novo token de sessão o, ele poderá usar o fluxo do token de sessão para o período especificado. (Isso será verdadeiro desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período especificado, o usuário será forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou menor do que a propriedade Idade Máxima de Token de Sessão Multifator.

### <a name="multi-factor-session-token-max-age"></a>Idade Máxima de Token de Sessão Multifator
**Cadeia de caracteres:** MaxAgeSessionMultiFactor

**Afeta:** tokens de sessão (persistentes e não persistentes)

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de sessão para obter uma nova ID e token de sessão após a última autenticação bem-sucedida usando apenas múltiplos fatores. Depois que um usuário autenticar e receber um novo token de sessão o, ele poderá usar o fluxo do token de sessão para o período especificado. (Isso será verdadeiro desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período especificado, o usuário será forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou maior do que a propriedade Idade Máxima de Token de Sessão de Fator Único.

## <a name="example-token-lifetime-policies"></a>Exemplo de políticas de tempo de vida do token
Muitos cenários são possíveis no Azure AD quando você cria e gerencia tempos de vida de token para aplicativos, entidades de serviço e sua organização geral. Nesta seção, examinaremos alguns cenários comuns de políticas que ajudarão você a impor novas regras para:

* Tempo de vida do token
* Tempo Máximo Inativos de Token
* Idade Máxima de Token

Nos exemplos, você poderá aprender a:

* Gerenciar a política padrão de uma organização
* Criar uma política para entrada na Web
* Criar uma política para um aplicativo nativo que chama uma API da Web
* Gerenciar uma política avançada

### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas de aplicativos, entidades de serviço e sua organização em geral. Se você for um novo usuário do Azure AD, recomendamos que aprenda sobre [como obter um locatário do Azure AD](active-directory-howto-tenant.md) antes de prosseguir com estes exemplos.  

Para começar, execute uma destas etapas:

1. Baixe a versão mais recente do [Azure AD PowerShell Visualização Pública](https://www.powershellgallery.com/packages/AzureADPreview).
2. Execute o comando `Connect` para entrar em sua conta do administrador do Azure AD. Execute esse comando sempre que você iniciar uma nova sessão.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas criadas em sua organização, execute o comando a seguir. Execute esse comando após a maioria das operações nos cenários a seguir. A execução do comando também ajuda você a obter a ** ** de suas políticas.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerenciar a política padrão de uma organização
Neste exemplo, crie uma política que permita aos usuários fazerem logon com menos frequência em toda sua organização. Para fazer isso, crie uma política de tempo de vida de token para Tokens de Atualização de Fator Único que é aplicada em toda sua organização. Essa política será aplicada a todos os aplicativos no a organização e a cada entidade de serviço que ainda não tenha uma política definida para ela.

1. Crie uma política de tempo de vida de token.

    1.  Defina o Token de Atualização de Fator Único como "until-revoked". O token não expira até que o acesso seja revogado. Crie a seguinte definição de política:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Para criar a política, execute o seguinte comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Atualizar a política.

    Talvez você decida que a primeira política definida neste exemplo não é tão estrita quanto seu serviço exige. Para definir a expiração do Token de Atualização de Fator Único para dois dias, execute o seguinte comando:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política para entrada na Web

Neste exemplo, crie uma política que exige dos usuários a autenticação mais frequente no aplicativo Web. Essa política define o tempo de vida dos tokens de acesso/Id e a idade máxima de um token de sessão multifator para a entidade de serviço do aplicativo Web.

1. Crie uma política de tempo de vida de token.

    Essa política, para entrada na Web, define o tempo de vida do token de acesso/ID e a idade máxima do Token de sessão de fator único como duas horas.

    1.  Para criar a política, execute este comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Atribuir a política à entidade de serviço. Você também precisará da **ObjectId** de sua entidade de serviço. 

    1.  Para ver todas as entidades de serviço de sua organização, consulte o [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ou, no [Graph Explorer do Azure AD](https://graphexplorer.cloudapp.net/), entre em sua conta do Azure AD.

    2.  Quando você tiver a **ObjectId** de sua entidade de serviço, execute o seguinte comando:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para um aplicativo nativo que chama uma API da Web
Neste exemplo, crie uma política que exige dos usuários a autenticação menos frequente. A política também aumenta a quantidade de tempo que um usuário pode ficar inativo antes que precise autenticar novamente. A política é aplicada à API da web. Quando o aplicativo nativo solicita a API da Web como um recurso, essa política é aplicada.

1. Crie uma política de tempo de vida de token.

    1.  Para criar uma política estrita para uma API da Web, execute este comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Atribua a política à sua API da Web. Você também precisará da **ObjectId** de seu aplicativo. A melhor maneira de localizar o aplicativo **ObjectId** é usar o [Portal do Azure](https://portal.azure.com/).

   Quando você tiver a **ObjectId** de seu aplicativo, execute o seguinte comando:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerenciar uma política avançada
Nesse exemplo, crie algumas políticas para saber como funciona o sistema de prioridade. Você também pode aprender a gerenciar várias políticas que são aplicadas a vários objetos.

1. Crie uma política de tempo de vida de token.

    1.  Para criar uma política padrão de organização que define o tempo de vida do Token de Atualização de Fator Único como 30 dias, execute este comando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Atribuir a política a uma entidade de serviço.

    Agora, você tem uma política que se aplica a toda a organização. Convém preservar essa política de 30 dias para uma entidade de serviço específica, mas altere a política padrão de organização para o limite superior de "until-revoked".

    1.  Para ver todas as entidades de serviço de sua organização, consulte o [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Ou, no [Graph Explorer do Azure AD](https://graphexplorer.cloudapp.net/), entre usando sua conta do Azure AD.

    2.  Quando você tiver a **ObjectId** de sua entidade de serviço, execute o seguinte comando:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Defina o sinalizador `IsOrganizationDefault` como false:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Crie uma nova política padrão de organização:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Agora você tem a política original vinculada à entidade de serviço, e a nova política definida como a política padrão de organização. É importante lembrar que as políticas aplicadas a entidades de serviço têm prioridade sobre as políticas padrão de organização.

## <a name="cmdlet-reference"></a>Referência de cmdlet

### <a name="manage-policies"></a>Gerenciar políticas

Use estes cmdlets para gerenciar as políticas.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Cria uma nova política.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matriz de JSON em representação textual que contém todas as regras da política. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Cadeia de caracteres do nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Se for true, define a política como a política padrão da organização. Se for false, não fará nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |O tipo da política. Para os tempos de vida de token, sempre use "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Define uma ID alternativa para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Obtém todas as políticas do Azure AD ou a política especificada.

```PowerShell
Get-AzureADPolicy
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> [Opcional] |**ObjectId (Id)** da política desejada. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todos os aplicativos e entidades de serviço vinculados a uma política.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da política desejada. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Atualiza uma política existente.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da política desejada. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Cadeia de caracteres do nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcional] |Matriz de JSON em representação textual que contém todas as regras da política. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcional] |Se for true, define a política como a política padrão da organização. Se for false, não fará nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcional] |O tipo da política. Para os tempos de vida de token, sempre use "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Define uma ID alternativa para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Exclui a política especificada.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** da política desejada. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Políticas de aplicativo
Use os cmdlets a seguir para políticas de aplicativos.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Vincula a política especificada a um aplicativo.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a política atribuída a um aplicativo.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Remove uma política de um aplicativo.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas de entidade de serviço
Use os cmdlets a seguir para políticas de entidade de serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Vincula a política especificada a uma entidade de serviço.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém qualquer política vinculada à entidade de serviço especificada.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Remove a política da entidade da entidade de serviço especificada.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |
