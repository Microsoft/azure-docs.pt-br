---
title: Tempos de vida de token configuráveis no Azure Active Directory | Microsoft Docs
description: Saiba como definir tempos de vida dos tokens emitidos pelo Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2019
ms.author: ryanwi
ms.custom: aaddev, annaba
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 901cf3e25ed63421f7e07d7773b6381fc54ea8a2
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489120"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Tempos de vida de token configuráveis no Azure Active Directory (versão prévia)

Especifique o tempo de vida de um token emitido pelo Azure Active Directory (Azure AD). Configure os tempos de vida de token de todos os aplicativos em uma organização, para um aplicativo multilocatário (várias organizações) ou para uma entidade de serviço específica em sua organização.

> [!IMPORTANT]
> Depois de ouvir os clientes durante a versão prévia, implementamos os [recursos de gerenciamento de sessão de autenticação](https://go.microsoft.com/fwlink/?linkid=2083106) no acesso condicional do Azure AD. Você pode usar esse novo recurso para configurar tempos de vida de token de atualização definindo a frequência de entrada. Depois de 1º de novembro de 2019, você não poderá usar a política de tempo de vida de token configurável para configurar tokens de atualização, mas ainda poderá usá-lo para configurar tokens de acesso.

No Azure AD, um objeto de política representa um conjunto de regras aplicadas a todos os aplicativos ou a aplicativos individuais em uma organização. Cada tipo de política tem uma estrutura exclusiva com um conjunto de propriedades que são aplicadas aos objetos aos quais são atribuídas.

Designe uma política como a padrão para sua organização. Essa política é aplicada a qualquer aplicativo na organização, desde que não seja substituída por uma política com uma prioridade mais alta. Você também pode atribuir uma política para aplicativos específicos. A ordem de prioridade varia por tipo de política.

> [!NOTE]
> A política de tempo de vida do token configurável não tem suporte para o SharePoint Online.  Embora você tenha a capacidade de criar essa política por meio do PowerShell, o SharePoint Online não a reconhecerá. Consulte o [blog do SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre como configurar o tempo limite da sessão ociosa.
>* O tempo de vida padrão para o token de acesso do SharePoint Online é de 1 hora. 
>* O tempo inativo máximo padrão do token de atualização do SharePoint Online é de 90 dias.


## <a name="token-types"></a>Tipos de token

Configure as políticas de tempo de vida de token para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID.

### <a name="access-tokens"></a>Tokens de acesso

Os clientes usam tokens de acesso para acessar um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de usuário, cliente e recurso. Tokens de acesso não podem ser revogados e são válidos até sua expiração. Um ator mal-intencionado que tenha obtido um token de acesso pode usá-lo pela extensão do tempo de vida. Ajustar o tempo de vida do token de acesso é uma compensação entre a melhorar o desempenho do sistema e aumentar o tempo pelo qual o cliente retém acesso depois que a conta do usuário é desabilitada. Um melhor desempenho do sistema é obtido, reduzindo o número de vezes que um cliente precisa adquirir um novo token de acesso.  O padrão é 1 hora; após 1 hora, o cliente deve usar o token de atualização para (normalmente de forma silenciosa) adquirir um novo token de atualização e outro de acesso. 

### <a name="refresh-tokens"></a>Tokens de atualização

Quando um cliente adquire um token de acesso para acessar um recurso protegido, ele recebe também um token de atualização. O token de atualização é usado para obter novos pares de tokens de acesso/atualização quando o token de acesso atual expira. Um token de atualização é associado a uma combinação de cliente e usuário. Um token de atualização pode ser [revogado a qualquer momento](access-tokens.md#token-revocation), e a validade do token é verificada sempre que for usado.  Tokens de atualização não são revogados quando usados para efetuar fetch de novos tokens de acesso — a prática recomendada, no entanto, é excluir o token antigo com segurança ao obter um novo. 

É importante fazer uma distinção entre clientes públicos e clientes confidenciais, pois isso afeta o tempo durante o qual os tokens de atualização podem ser usados. Para saber mais sobre os tipos diferentes de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente confidencial
Clientes confidenciais são aplicativos que podem armazenar com segurança uma senha (segredo) do cliente. Eles podem provar que as solicitações são provenientes do aplicativo cliente protegido, e não de um ator mal-intencionado. Por exemplo, um aplicativo Web é um cliente confidencial, pois pode armazenar um segredo do cliente no servidor Web. Ele não fica exposto. Como esses fluxos são mais seguros, os tempos de vida padrão de tokens de atualização emitidos para esses fluxos são `until-revoked`, não podem ser alterados usando uma política e não serão revogados em redefinições de senha voluntárias.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente público

Clientes públicos não são capazes de armazenar com segurança a senha (segredo) de um cliente. Por exemplo, um aplicativo iOS/Android não pode ocultar um segredo do proprietário do recurso e, portanto, é considerado um cliente público. Defina políticas em recursos para impedir que tokens de atualização de clientes públicos mais antigos do que um período especificado obtenham um novo par de tokens de acesso/atualização. (Para fazer isso, use a propriedade Tempo Máximo Inativo do Token de Atualização (`MaxInactiveTime`)) Use também políticas para definir um período além do qual os tokens de atualização não serão mais aceitos. (Para fazer isso, use a propriedade Idade Máxima do Token de Atualização.) Ajuste o tempo de vida de um token de atualização para controlar quando e com que frequência o usuário precisa reinserir as credenciais, em vez de ser autenticado novamente de forma silenciosa ao usar um aplicativo cliente público.

### <a name="id-tokens"></a>Tokens de ID
Tokens de ID são passados para sites e clientes nativos. Os tokens de ID contêm informações de perfil sobre um usuário. Um token de ID é associado a uma combinação específica de cliente e usuário. Os tokens de ID são considerados válidos até a expiração. Normalmente, um aplicativo Web corresponde o tempo de vida de sessão de um usuário no aplicativo ao tempo de vida do token de ID emitido para o usuário. Ajuste o tempo de vida de um token de ID para controlar com que frequência o aplicativo Web expira a sessão do aplicativo, com que frequência exige que o usuário seja autenticado novamente no Azure AD (de forma silenciosa ou interativa).

### <a name="single-sign-on-session-tokens"></a>Tokens de sessão de logon único
Quando um usuário se autentica no Azure AD, uma sessão de SSO (Logon Único) é estabelecida com o navegador do usuário e o Azure AD. O Token de SSO, na forma de um cookie, representa essa sessão. O token de sessão de SSO não está associado a um aplicativo cliente/recurso específico. Tokens de sessão de SSO podem ser revogados, e sua validade é verificada sempre que eles são usados.

O Azure AD usa dois tipos de tokens de sessão de SSO: persistente e não persistente. Tokens de sessão persistentes são armazenadas como cookies persistentes pelo navegador. Tokens de sessão não persistentes são armazenados como cookies de sessão. (Cookies de sessão são destruídos quando o navegador é fechado). Normalmente, um token de sessão não persistente é armazenado. Porém, quando o usuário marca a caixa de seleção **Mantenha-me conectado** durante a autenticação, um token de sessão persistente é armazenado.

Tokens de sessão não persistentes têm uma vida útil de 24 horas. Tokens persistentes têm um tempo de vida de 180 dias. Sempre que um token de sessão de SSO é usado dentro de seu período de validade, o período de validade é estendido em mais de 24 horas ou 180 dias, dependendo do tipo de token. Se o token de sessão de SSO não for usado dentro do período de validade, ele será considerado expirado e não será mais aceito.

Use uma política para definir o tempo limite após a emissão do primeiro token de sessão além do qual o token de sessão não será mais aceito. (Para fazer isso, use a propriedade Idade Máxima de Token de Sessão.) Ajuste o tempo de vida de um token de sessão para controlar quando e com que frequência o usuário precisa reinserir as credenciais, em vez de ser autenticado de forma silenciosa, ao usar um aplicativo Web.

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de tempo de vida de token
Uma política de tempo de vida do token é um tipo de objeto de política que contém regras de tempo de vida do token. Use as propriedades da política para controlar tempos de vida de tokens especificados. Se nenhuma política for definida, o sistema aplicará o valor de tempo de vida padrão.

### <a name="configurable-token-lifetime-properties"></a>Propriedades de tempo de vida de token configurável
| Propriedade | Cadeia de caracteres de propriedade de política | Afeta | Padrão | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Tempo de Vida do Token de Acesso |AccessTokenLifetime<sup>4</sup> |Tokens de acesso, tokens de ID, tokens SAML2 |1 hora |10 minutos |1 dia |
| Tempo Máximo Inativo de Token de Atualização |MaxInactiveTime |Tokens de atualização |90 dias |10 minutos |90 dias |
| Idade Máxima de Token de Atualização de Fator Único |MaxAgeSingleFactor |Tokens de atualização (para quaisquer usuários) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Atualização Multifator |MaxAgeMultiFactor |Tokens de atualização (para quaisquer usuários) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Sessão de Fator Único |MaxAgeSessionSingleFactor<sup>2</sup> |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Sessão Multifator |MaxAgeSessionMultiFactor<sup>3</sup> |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento máximo explícito que pode ser definido para esses atributos.
* <sup>4</sup> Para que o cliente Web do Microsoft Teams funcione, é recomendável definir AccessTokenLifetime para mais de 15 minutos para as equipes da Microsoft.

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

Para saber mais sobre a relação entre objetos de aplicativo e de entidade de serviço, confira [Objetos de aplicativos e entidade de serviço no Azure Active Directory](app-objects-and-service-principals.md).

A validade do token é avaliada no momento em que ele é usado. A política com a prioridade mais alta no aplicativo que está sendo acessado entra em vigor.

Todos os períodos de tempo usados aqui são formatados de acordo com o objeto C# [TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Assim, 80 dias e 30 minutos seria `80.00:30:00`.  Os D principal pode ser descartado se for zero, então 90 minutos seria `00:90:00`.  

> [!NOTE]
> Veja um exemplo de cenários.
>
> Um usuário quer acessar dois aplicativos Web: Aplicativo Web A e Aplicativo Web B.
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

**Afeta:** Tokens de acesso, tokens de ID

**Resumo:** Essa política controla por quanto tempo o acesso e os tokens de ID desse recurso serão considerados válidos. A redução da propriedade de Tempo de vida útil do token acesso minimiza o risco de uso de um token de acesso ou de ID por um ator mal-intencionado durante um longo período. (Esses tokens não podem ser revogados.) A desvantagem é que o desempenho é afetado negativamente, pois os tokens precisam ser substituído com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Tempo Máximo Inativo de Token de Atualização
**Cadeia de caracteres:** MaxInactiveTime

**Afeta:** Tokens de atualização

**Resumo:** Esta política controla quanto tempo um token de atualização poderá ter, antes que um cliente não possa mais utilizá-lo para recuperar um novo par de tokens de acesso/atualização ao tentar acessar esse recurso. Como um novo token de atualização normalmente retorna quando um token de atualização é usado, essa política impede o acesso se o cliente tentar acessar qualquer recurso usando o token de atualização atual durante o período especificado de tempo.

Essa política força os usuários que não estão ativos no cliente a se autenticarem novamente para recuperar um novo token de atualização.

A propriedade Tempo Máximo Inativo do Token de Atualização deve ser definida como um valor menor do que as propriedades Idade Máxima do Token de Fator Único e Idade Máxima de Token de Atualização multifator.

### <a name="single-factor-refresh-token-max-age"></a>Idade Máxima de Token de Atualização de Fator Único
**Cadeia de caracteres:** MaxAgeSingleFactor

**Afeta:** Tokens de atualização

**Resumo:** Essa política controla por quanto tempo um usuário poderá usar um token de atualização para obter um novo par de tokens de acesso/atualização, após a última autenticação com êxito usando apenas um único fator. Depois que um usuário autenticar e receber um novo token de atualização, ele poderá usar o fluxo do token de atualização para o período especificado. (Isso será verdadeiro desde que o token de atualização atual não seja revogado, e não fique sem uso por mais tempo do que o tempo de inatividade.) Nesse ponto, o usuário é forçado a se autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou menor do que a propriedade Idade Máxima de Token de Atualização Multifator.

### <a name="multi-factor-refresh-token-max-age"></a>Idade Máxima de Token de Atualização Multifator
**Cadeia de caracteres:** MaxAgeMultiFactor

**Afeta:** Tokens de atualização

**Resumo:** Essa política controla por quanto tempo um usuário poderá usar um token de atualização para obter um novo par de tokens de acesso/atualização, após a última autenticação com êxito usando múltiplos fatores. Depois que um usuário autenticar e receber um novo token de atualização, ele poderá usar o fluxo do token de atualização para o período especificado. (Isso será verdadeiro desde que o token de atualização atual não seja revogado, e não fique sem uso por mais tempo do que o tempo de inatividade.) Nesse ponto, os usuários são forçados a autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou maior do que a propriedade Idade Máxima de Token de Atualização de Fator Único.

### <a name="single-factor-session-token-max-age"></a>Idade Máxima de Token de Sessão de Fator Único
**Cadeia de caracteres:** MaxAgeSessionSingleFactor

**Afeta:** Tokens de sessão (persistentes e não persistentes)

**Resumo:** Essa política controla por quanto tempo um usuário poderá usar um token de sessão para obter uma nova ID e token de sessão, após a última autenticação com êxito usando apenas um único fator. Depois que um usuário autenticar e receber um novo token de sessão o, ele poderá usar o fluxo do token de sessão para o período especificado. (Isso será verdadeiro desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período especificado, o usuário será forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou menor do que a propriedade Idade Máxima de Token de Sessão Multifator.

### <a name="multi-factor-session-token-max-age"></a>Idade Máxima de Token de Sessão Multifator
**Cadeia de caracteres:** MaxAgeSessionMultiFactor

**Afeta:** Tokens de sessão (persistentes e não persistentes)

**Resumo:** Essa política controla por quanto tempo um usuário poderá usar um token de sessão para obter uma nova ID e um token de sessão, após a última vez em que foi autenticado com êxito usando múltiplos fatores. Depois que um usuário autenticar e receber um novo token de sessão o, ele poderá usar o fluxo do token de sessão para o período especificado. (Isso será verdadeiro desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período especificado, o usuário será forçado a autenticar novamente para receber um novo token de sessão.

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
Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas de aplicativos, entidades de serviço e sua organização em geral. Se você for um novo usuário do Azure AD, recomendamos que aprenda sobre [como obter um locatário do Azure AD](quickstart-create-new-tenant.md) antes de prosseguir com estes exemplos.  

Para começar, execute uma destas etapas:

1. Baixe a versão mais recente do [Azure AD PowerShell Visualização Pública](https://www.powershellgallery.com/packages/AzureADPreview).
2. Execute o comando `Connect` para entrar em sua conta do administrador do Azure AD. Execute esse comando sempre que você iniciar uma nova sessão.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Para ver todas as políticas criadas em sua organização, execute o comando a seguir. Execute esse comando após a maioria das operações nos cenários a seguir. A execução do comando também ajuda você a obter a ** ** de suas políticas.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exemplo: Gerenciar a política padrão de uma organização
Neste exemplo, você cria uma política que permite que os usuários façam logon com menos frequência em toda a organização. Para fazer isso, crie uma política de tempo de vida de token para Tokens de Atualização de Fator Único que é aplicada em toda sua organização. Essa política será aplicada a todos os aplicativos no a organização e a cada entidade de serviço que ainda não tenha uma política definida para ela.

1. Crie uma política de tempo de vida de token.

    1. Defina o Token de Atualização de Fator Único como "until-revoked". O token não expira até que o acesso seja revogado. Crie a seguinte definição de política:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. Para criar a política, execute o seguinte comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atualizar a política.

    Talvez você decida que a primeira política definida neste exemplo não é tão estrita quanto seu serviço exige. Para definir a expiração do Token de Atualização de Fator Único para dois dias, execute o seguinte comando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política para entrada na Web

Neste exemplo, crie uma política que exige dos usuários a autenticação mais frequente no aplicativo Web. Essa política define o tempo de vida dos tokens de acesso/Id e a idade máxima de um token de sessão multifator para a entidade de serviço do aplicativo Web.

1. Crie uma política de tempo de vida de token.

    Essa política, para entrada na Web, define o tempo de vida do token de acesso/ID e a idade máxima do Token de sessão de fator único como duas horas.

    1. Para criar a política, execute este comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Para ver a nova política e obter a **ObjectId** da política, execute o comando a seguir:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atribuir a política à entidade de serviço. Você também precisará da **ObjectId** de sua entidade de serviço.

    1. Use o cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) para ver todas as entidades de serviço de sua organização ou uma única entidade de serviço.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Quando você tiver a entidade de serviço, execute o seguinte comando:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exemplo: Criar uma política para um aplicativo nativo que chama uma API da Web
Neste exemplo, crie uma política que exige dos usuários a autenticação menos frequente. A política também aumenta a quantidade de tempo que um usuário pode ficar inativo antes que precise autenticar novamente. A política é aplicada à API da web. Quando o aplicativo nativo solicita a API da Web como um recurso, essa política é aplicada.

1. Crie uma política de tempo de vida de token.

    1. Para criar uma política estrita para uma API da Web, execute este comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Para ver sua nova política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atribua a política à sua API da Web. Você também precisará da **ObjectId** de seu aplicativo. Use o cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) para localizar o **ObjectID**do seu aplicativo ou use o [portal do Azure](https://portal.azure.com/).

    Obtenha o **ObjectID** do seu aplicativo e atribua a política:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Exemplo: Gerenciar uma política avançada
Neste exemplo, você cria algumas políticas para saber como o sistema de prioridade funciona. Você também aprenderá como gerenciar várias políticas que são aplicadas a vários objetos.

1. Crie uma política de tempo de vida de token.

    1. Para criar uma política padrão de organização que define o tempo de vida do Token de Atualização de Fator Único como 30 dias, execute este comando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Para ver sua nova política, execute o seguinte comando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Atribuir a política a uma entidade de serviço.

    Agora, você tem uma política que se aplica a toda a organização. Convém preservar essa política de 30 dias para uma entidade de serviço específica, mas altere a política padrão de organização para o limite superior de "until-revoked".

    1. Para ver todas as entidades de serviço de sua organização, use o cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    2. Quando você tiver a entidade de serviço, execute o seguinte comando:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. Defina o sinalizador `IsOrganizationDefault` como false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Crie uma nova política padrão de organização:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Agora você tem a política original vinculada à entidade de serviço, e a nova política definida como a política padrão de organização. É importante lembrar que as políticas aplicadas a entidades de serviço têm prioridade sobre as políticas padrão de organização.

## <a name="cmdlet-reference"></a>Referência de cmdlet

### <a name="manage-policies"></a>Gerenciar políticas

Use estes cmdlets para gerenciar as políticas.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Cria uma nova política.

```powershell
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

```powershell
Get-AzureADPolicy
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> [Opcional] |**ObjectId (ID)** da política que você deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Obtém todos os aplicativos e entidades de serviço vinculados a uma política.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que você deseja. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Atualiza uma política existente.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que você deseja. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Cadeia de caracteres do nome da política. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Opcional] |Matriz de JSON em representação textual que contém todas as regras da política. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Opcional] |Se for true, define a política como a política padrão da organização. Se for false, não fará nada. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Opcional] |O tipo da política. Para os tempos de vida de token, sempre use "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Opcional] |Define uma ID alternativa para a política. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Exclui a política especificada.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** da política que você deseja. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Políticas de aplicativo
Use os cmdlets a seguir para políticas de aplicativos.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Vincula a política especificada a um aplicativo.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Obtém a política atribuída a um aplicativo.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Remove uma política de um aplicativo.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Políticas de entidade de serviço
Use os cmdlets a seguir para políticas de entidade de serviço.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Vincula a política especificada a uma entidade de serviço.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** da política. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Obtém qualquer política vinculada à entidade de serviço especificada.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Remove a política da entidade da entidade de serviço especificada.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parâmetros | Descrição | Exemplo |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** do aplicativo. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** da política. | `-PolicyId <ObjectId of Policy>` |
