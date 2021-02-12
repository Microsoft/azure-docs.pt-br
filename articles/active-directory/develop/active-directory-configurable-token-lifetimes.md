---
title: Tempos de vida de token configuráveis
titleSuffix: Microsoft identity platform
description: Saiba como definir tempos de vida para acesso, SAML e tokens de ID emitidos pela plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 1bd60a60aa5f6fffcc459f0e14d550740e48496d
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428142"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Tempos de vida de token configuráveis na plataforma Microsoft Identity (versão prévia)

Você pode especificar o tempo de vida de um token de acesso, ID ou SAML emitido pela plataforma de identidade da Microsoft. Configure os tempos de vida de token de todos os aplicativos em uma organização, para um aplicativo multilocatário (várias organizações) ou para uma entidade de serviço específica em sua organização. No entanto, atualmente não damos suporte à configuração de tempos de vida do token para [entidades de serviço de identidade gerenciadas](../managed-identities-azure-resources/overview.md).

No Azure AD, um objeto de política representa um conjunto de regras aplicadas a todos os aplicativos ou a aplicativos individuais em uma organização. Cada tipo de política tem uma estrutura exclusiva com um conjunto de propriedades que são aplicadas aos objetos aos quais são atribuídas.

Designe uma política como a padrão para sua organização. Essa política é aplicada a qualquer aplicativo na organização, desde que não seja substituída por uma política com uma prioridade mais alta. Você também pode atribuir uma política para aplicativos específicos. A ordem de prioridade varia por tipo de política.

Para obter exemplos, leia [exemplos de como configurar tempos de vida de token](configure-token-lifetimes.md).

> [!NOTE]
> A política de tempo de vida de token configurável só se aplica a clientes móveis e de desktop que acessam os recursos do SharePoint Online e do OneDrive for Business e não se aplicam a sessões do navegador da Web.
> Para gerenciar o tempo de vida das sessões do navegador da Web para o SharePoint Online e o OneDrive for Business, use o recurso de [tempo de vida da sessão de acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md) . Consulte o [blog do SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) para saber mais sobre como configurar o tempo limite da sessão ociosa.

## <a name="license-requirements"></a>Requisitos de licença

O uso desse recurso requer uma licença do Azure AD Premium P1. Para encontrar a licença certa para seus requisitos, consulte comparando [recursos disponíveis em geral das edições gratuita e Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Os clientes com [licenças do Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) também têm acesso a recursos de Acesso Condicional.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Políticas de tempo de vida de token para tokens de acesso, SAML e ID

Você pode definir políticas de tempo de vida de token para tokens de acesso, tokens SAML e tokens de ID. 

### <a name="access-tokens"></a>Tokens de acesso

Os clientes usam tokens de acesso para acessar um recurso protegido. Um token de acesso só pode ser usado para uma combinação específica de usuário, cliente e recurso. Tokens de acesso não podem ser revogados e são válidos até sua expiração. Um ator mal-intencionado que tenha obtido um token de acesso pode usá-lo pela extensão do tempo de vida. Ajustar o tempo de vida do token de acesso é uma compensação entre a melhorar o desempenho do sistema e aumentar o tempo pelo qual o cliente retém acesso depois que a conta do usuário é desabilitada. Um melhor desempenho do sistema é obtido, reduzindo o número de vezes que um cliente precisa adquirir um novo token de acesso.  O padrão é 1 hora; após 1 hora, o cliente deve usar o token de atualização para (normalmente de forma silenciosa) adquirir um novo token de atualização e outro de acesso.

### <a name="saml-tokens"></a>Tokens SAML

Os tokens SAML são usados por muitos aplicativos SaaS baseados na Web e são obtidos usando o ponto de extremidade do protocolo SAML2 do Azure Active Directory. Eles também são consumidos por aplicativos que usam o WS-Federation. O tempo de vida padrão do token é de 1 hora. Da perspectiva de um aplicativo, o período de validade do token é especificado pelo valor NotOnOrAfter do `<conditions …>` elemento no token. Após o término do período de validade do token, o cliente deve iniciar uma nova solicitação de autenticação, que geralmente será satisfeita sem a entrada interativa como resultado do token de sessão de logon único (SSO).

O valor de NotOnOrAfter pode ser alterado usando o `AccessTokenLifetime` parâmetro em um `TokenLifetimePolicy` . Ele será definido para o tempo de vida configurado na política, se houver, além de um fator de distorção de relógio de cinco minutos.

O NotOnOrAfter de confirmação da entidade especificado no `<SubjectConfirmationData>` elemento não é afetado pela configuração de tempo de vida do token. 

### <a name="id-tokens"></a>Tokens de ID

Tokens de ID são passados para sites e clientes nativos. Os tokens de ID contêm informações de perfil sobre um usuário. Um token de ID é associado a uma combinação específica de cliente e usuário. Os tokens de ID são considerados válidos até a expiração. Normalmente, um aplicativo Web corresponde o tempo de vida de sessão de um usuário no aplicativo ao tempo de vida do token de ID emitido para o usuário. Você pode ajustar o tempo de vida de um token de ID para controlar a frequência com que o aplicativo Web expira a sessão do aplicativo e com que frequência ele requer que o usuário seja autenticado novamente com a plataforma de identidade da Microsoft (de forma silenciosa ou interativa).

### <a name="token-lifetime-policy-properties"></a>Propriedades da política de tempo de vida de token

Uma política de tempo de vida do token é um tipo de objeto de política que contém regras de tempo de vida do token. Essa política controla por quanto tempo os tokens de acesso, SAML e ID para esse recurso são considerados válidos. Se nenhuma política for definida, o sistema aplicará o valor de tempo de vida padrão. 

A redução da propriedade de Tempo de vida útil do token acesso minimiza o risco de uso de um token de acesso ou de ID por um ator mal-intencionado durante um longo período. (Esses tokens não podem ser revogados.) A desvantagem é que o desempenho é afetado negativamente, pois os tokens precisam ser substituídos com mais frequência.

Para obter um exemplo, consulte [criar uma política para entrada na Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

| Propriedade | Cadeia de caracteres de propriedade de política | Afeta | Padrão | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Tempo de Vida do Token de Acesso |AccessTokenLifetime |Tokens de acesso, tokens de ID, tokens SAML2 |1 hora |10 minutos |1 dia |

> [!NOTE]
> Para garantir que o cliente Web do Microsoft Teams funcione, é recomendável manter o AccessTokenLifetime a mais de 15 minutos para o Microsoft Teams.

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Políticas de vida útil de token para tokens de atualização e tokens de sessão

Você não pode definir políticas de tempo de vida de token para tokens de atualização e tokens de sessão.

> [!IMPORTANT]
> A partir de 30 de janeiro de 2021, você não pode configurar tempos de vida de atualização e de token de sessão. Azure Active Directory não honra mais a configuração de token de atualização e de sessão nas políticas existentes.  Novos tokens emitidos após os tokens existentes expiraram agora são definidos para a [configuração padrão](#configurable-token-lifetime-properties-after-the-retirement). Você ainda pode configurar o acesso, SAML e tempos de vida de token de ID após a atualização e a desativação da configuração do token de sessão.
>
> O tempo de vida do token existente não será alterado. Depois que eles expirarem, um novo token será emitido com base no valor padrão.
>
> Se você precisar continuar a definir o período de tempo antes que um usuário seja solicitado a entrar novamente, configure a frequência de entrada no acesso condicional. Para saber mais sobre o acesso condicional, leia [Configurar gerenciamento de sessão de autenticação com acesso condicional](../conditional-access/howto-conditional-access-session-lifetime.md).

:::image type="content" source="./media/active-directory-configurable-token-lifetimes/roadmap.svg" alt-text="Informações de aposentadoria":::

### <a name="refresh-tokens"></a>Tokens de atualização

Quando um cliente adquire um token de acesso para acessar um recurso protegido, ele recebe também um token de atualização. O token de atualização é usado para obter novos pares de tokens de acesso/atualização quando o token de acesso atual expira. Um token de atualização é associado a uma combinação de cliente e usuário. Um token de atualização pode ser [revogado a qualquer momento](access-tokens.md#token-revocation), e a validade do token é verificada sempre que for usado.  Tokens de atualização não são revogados quando usados para efetuar fetch de novos tokens de acesso — a prática recomendada, no entanto, é excluir o token antigo com segurança ao obter um novo.

É importante fazer uma distinção entre clientes públicos e clientes confidenciais, pois isso afeta o tempo durante o qual os tokens de atualização podem ser usados. Para saber mais sobre os tipos diferentes de clientes, consulte [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente confidencial
Clientes confidenciais são aplicativos que podem armazenar com segurança uma senha (segredo) do cliente. Eles podem provar que as solicitações são provenientes do aplicativo cliente protegido, e não de um ator mal-intencionado. Por exemplo, um aplicativo Web é um cliente confidencial, pois pode armazenar um segredo do cliente no servidor Web. Ele não fica exposto. Como esses fluxos são mais seguros, os tempos de vida padrão de tokens de atualização emitidos para esses fluxos são `until-revoked`, não podem ser alterados usando uma política e não serão revogados em redefinições de senha voluntárias.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tempos de vida de token com tokens de atualização de cliente público

Clientes públicos não são capazes de armazenar com segurança a senha (segredo) de um cliente. Por exemplo, um aplicativo iOS/Android não pode ocultar um segredo do proprietário do recurso e, portanto, é considerado um cliente público. Defina políticas em recursos para impedir que tokens de atualização de clientes públicos mais antigos do que um período especificado obtenham um novo par de tokens de acesso/atualização. Para fazer isso, use a [Propriedade tempo máximo inativo do token de atualização](#refresh-token-max-inactive-time) ( `MaxInactiveTime` ). Use também políticas para definir um período além do qual os tokens de atualização não serão mais aceitos. Para fazer isso, use a propriedade idade máxima de token de [atualização de fator único](#single-factor-session-token-max-age) ou idade [de sessão de vários fatores](#multi-factor-refresh-token-max-age) . Ajuste o tempo de vida de um token de atualização para controlar quando e com que frequência o usuário precisa reinserir as credenciais, em vez de ser autenticado novamente de forma silenciosa ao usar um aplicativo cliente público.

A propriedade idade máxima é o período de tempo que um único token pode ser usado. 

### <a name="single-sign-on-session-tokens"></a>Tokens de sessão de logon único
Quando um usuário é autenticado com a plataforma de identidade da Microsoft, uma sessão de logon único (SSO) é estabelecida com o navegador do usuário e a plataforma de identidade da Microsoft. O Token de SSO, na forma de um cookie, representa essa sessão. O token de sessão de SSO não está associado a um aplicativo cliente/recurso específico. Tokens de sessão de SSO podem ser revogados, e sua validade é verificada sempre que eles são usados.

A plataforma de identidade da Microsoft usa dois tipos de tokens de sessão de SSO: persistente e não persistente. Tokens de sessão persistentes são armazenadas como cookies persistentes pelo navegador. Tokens de sessão não persistentes são armazenados como cookies de sessão. (Os cookies de sessão são destruídos quando o navegador é fechado.) Normalmente, um token de sessão não persistente é armazenado. Porém, quando o usuário marca a caixa de seleção **Mantenha-me conectado** durante a autenticação, um token de sessão persistente é armazenado.

Tokens de sessão não persistentes têm uma vida útil de 24 horas. Os tokens persistentes têm um tempo de vida de 90 dias. Sempre que um token de sessão de SSO é usado dentro de seu período de validade, o período de validade é estendido em mais de 24 horas ou 90 dias, dependendo do tipo de token. Se o token de sessão de SSO não for usado dentro do período de validade, ele será considerado expirado e não será mais aceito.

Use uma política para definir o tempo limite após a emissão do primeiro token de sessão além do qual o token de sessão não será mais aceito. (Para fazer isso, use a propriedade idade máxima de token de sessão.) Você pode ajustar o tempo de vida de um token de sessão para controlar quando e com que frequência um usuário precisa reinserir as credenciais, em vez de ser autenticado silenciosamente, ao usar um aplicativo Web.

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Propriedades da política de tempo de vida de token de atualização e de sessão
Uma política de tempo de vida do token é um tipo de objeto de política que contém regras de tempo de vida do token. Use as propriedades da política para controlar tempos de vida de tokens especificados. Se nenhuma política for definida, o sistema aplicará o valor de tempo de vida padrão.

#### <a name="configurable-token-lifetime-properties"></a>Propriedades de tempo de vida de token configurável
| Propriedade | Cadeia de caracteres de propriedade de política | Afeta | Padrão | Mínimo | Máximo |
| --- | --- | --- | --- | --- | --- |
| Tempo Máximo Inativo de Token de Atualização |MaxInactiveTime |Tokens de atualização |90 dias |10 minutos |90 dias |
| Idade Máxima de Token de Atualização de Fator Único |MaxAgeSingleFactor |Tokens de atualização (para quaisquer usuários) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Atualização Multifator |MaxAgeMultiFactor |Tokens de atualização (para quaisquer usuários) | Until-revoked |10 minutos |180 dias<sup>1</sup> |
| Idade Máxima de Token de Sessão de Fator Único |MaxAgeSessionSingleFactor |Tokens de sessão (persistentes e não persistentes) |Until-revoked |10 minutos |Until-revoked<sup>1</sup> |
| Idade Máxima de Token de Sessão Multifator |MaxAgeSessionMultiFactor |Tokens de sessão (persistentes e não persistentes) | Until-revoked |10 minutos | 180 dias<sup>1</sup> |

* <sup>1</sup>365 dias é o comprimento máximo explícito que pode ser definido para esses atributos.

#### <a name="exceptions"></a>Exceções
| Propriedade | Afeta | Padrão |
| --- | --- | --- |
| Idade Máxima dos Tokens de Atualização (emitidos para usuários federados com informações de revogação insuficientes<sup>1</sup>) |Tokens de atualização (emitidos para usuários federados com informações de revogação insuficientes<sup>1</sup>) |12 horas |
| Tempo Máximo Inativo do Token de Atualização (emitido para clientes confidenciais) |Tokens de atualização (emitido para clientes confidenciais) |90 dias |
| Idade Máxima do Token de Atualização (emitido para clientes confidenciais) |Tokens de atualização (emitido para clientes confidenciais) |Until-revoked |

* <sup>1</sup> usuários federados que têm informações de revogação insuficientes incluem todos os usuários que não têm o atributo "LastPasswordChangeTimestamp" sincronizado. Esses usuários recebem essa idade máxima curta porque Azure Active Directory não é capaz de verificar quando revogar tokens vinculados a uma credencial antiga (como uma senha que foi alterada) e deve fazer check-in com mais frequência para garantir que o usuário e os tokens associados ainda estejam em boas condições. Para melhorar essa experiência, os administradores de locatários devem garantir que estejam sincronizando o atributo "LastPasswordChangeTimestamp" (isso pode ser definido no objeto de usuário usando o PowerShell ou por meio de AADSync).

### <a name="configurable-policy-property-details"></a>Detalhes configuráveis da propriedade da política

#### <a name="refresh-token-max-inactive-time"></a>Tempo Máximo Inativo de Token de Atualização
**Cadeia de caracteres:** MaxInactiveTime

**Afeta:** tokens de atualização

**Resumo:** essa política controla a idade que um token de atualização pode ter antes que um cliente não possa mais usá-lo para recuperar um novo par de tokens de atualização/acesso ao tentar acessar esse recurso. Como um novo token de atualização normalmente retorna quando um token de atualização é usado, essa política impede o acesso se o cliente tentar acessar qualquer recurso usando o token de atualização atual durante o período especificado de tempo.

Essa política força os usuários que não estão ativos no cliente a se autenticarem novamente para recuperar um novo token de atualização.

A propriedade Tempo Máximo Inativo do Token de Atualização deve ser definida como um valor menor do que as propriedades Idade Máxima do Token de Fator Único e Idade Máxima de Token de Atualização multifator.

Para obter um exemplo, consulte [criar uma política para um aplicativo nativo que chama uma API da Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-refresh-token-max-age"></a>Idade Máxima de Token de Atualização de Fator Único
**Cadeia de caracteres:** MaxAgeSingleFactor

**Afeta:** tokens de atualização

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de atualização para obter um novo par de tokens de acesso/atualização após a última autenticação bem-sucedida usando apenas um único fator. Depois que um usuário autenticar e receber um novo token de atualização, ele poderá usar o fluxo do token de atualização para o período especificado. (Isso é verdadeiro, desde que o token de atualização atual não seja revogado e não seja deixado não utilizado por mais tempo do que a hora inativa.) Nesse ponto, o usuário é forçado a autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou menor do que a propriedade Idade Máxima de Token de Atualização Multifator.

Para obter um exemplo, consulte [criar uma política para um aplicativo nativo que chama uma API da Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="multi-factor-refresh-token-max-age"></a>Idade Máxima de Token de Atualização Multifator
**Cadeia de caracteres:** MaxAgeMultiFactor

**Afeta:** tokens de atualização

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de atualização para obter um novo par de tokens de acesso/atualização após a última autenticação bem-sucedida múltiplos fatores. Depois que um usuário autenticar e receber um novo token de atualização, ele poderá usar o fluxo do token de atualização para o período especificado. (Isso é verdadeiro, desde que o token de atualização atual não seja revogado e não seja usado por mais tempo do que a hora inativa.) Nesse ponto, os usuários são forçados a autenticar novamente para receber um novo token de atualização.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou maior do que a propriedade Idade Máxima de Token de Atualização de Fator Único.

Para obter um exemplo, consulte [criar uma política para um aplicativo nativo que chama uma API da Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

#### <a name="single-factor-session-token-max-age"></a>Idade Máxima de Token de Sessão de Fator Único
**Cadeia de caracteres:** MaxAgeSessionSingleFactor

**Afeta:** tokens de sessão (persistentes e não persistentes)

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de sessão para obter uma nova ID e token de sessão após a última autenticação bem-sucedida usando apenas um único fator. Depois que um usuário autenticar e receber um novo token de sessão o, ele poderá usar o fluxo do token de sessão para o período especificado. (Isso será verdadeiro, desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período de tempo especificado, o usuário é forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou menor do que a propriedade Idade Máxima de Token de Sessão Multifator.

Para obter um exemplo, consulte [criar uma política para entrada na Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

#### <a name="multi-factor-session-token-max-age"></a>Idade Máxima de Token de Sessão Multifator
**Cadeia de caracteres:** MaxAgeSessionMultiFactor

**Afeta:** tokens de sessão (persistentes e não persistentes)

**Resumo:** essa política controla por quanto tempo um usuário pode usar um token de sessão para obter uma nova ID e token de sessão após a última autenticação bem-sucedida usando apenas múltiplos fatores. Depois que um usuário autenticar e receber um novo token de sessão o, ele poderá usar o fluxo do token de sessão para o período especificado. (Isso será verdadeiro, desde que o token de sessão atual não seja revogado e não tenha expirado.) Após o período de tempo especificado, o usuário é forçado a autenticar novamente para receber um novo token de sessão.

Reduzir a idade máxima força os usuários a se autenticarem com mais frequência. Como a autenticação de fator único é considerada menos segura do que a autenticação multifator, recomendamos a definição dessa propriedade com um valor igual ou maior do que a propriedade Idade Máxima de Token de Sessão de Fator Único.

## <a name="configurable-token-lifetime-properties-after-the-retirement"></a>Propriedades de tempo de vida de token configuráveis após a aposentadoria
A atualização e a configuração de token de sessão são afetadas pelas propriedades a seguir e seus valores definidos respectivamente. Após a desativação da configuração de token de atualização e de sessão em 30 de janeiro de 2021, o Azure AD honrará apenas os valores padrão descritos abaixo. Se você decidir não usar o acesso condicional para gerenciar a frequência de entrada, seus tokens de atualização e de sessão serão definidos para a configuração padrão nessa data e você não poderá mais alterar seus tempos de vida.  

|Propriedade   |Cadeia de caracteres de propriedade de política    |Afeta |Padrão |
|----------|-----------|------------|------------|
|Tempo de Vida do Token de Acesso |AccessTokenLifetime |Tokens de acesso, tokens de ID, tokens SAML2 |1 hora |
|Tempo Máximo Inativo de Token de Atualização |MaxInactiveTime  |Tokens de atualização |90 dias  |
|Idade Máxima de Token de Atualização de Fator Único  |MaxAgeSingleFactor  |Tokens de atualização (para quaisquer usuários)  |Until-revoked  |
|Idade Máxima de Token de Atualização Multifator  |MaxAgeMultiFactor  |Tokens de atualização (para quaisquer usuários) |Until-revoked  |
|Idade Máxima de Token de Sessão de Fator Único  |MaxAgeSessionSingleFactor |Tokens de sessão (persistentes e não persistentes)  |Until-revoked |
|Idade Máxima de Token de Sessão Multifator  |MaxAgeSessionMultiFactor  |Tokens de sessão (persistentes e não persistentes)  |Until-revoked |

Você pode usar o PowerShell para localizar as políticas que serão afetadas pela desativação.  Use os [cmdlets do PowerShell](configure-token-lifetimes.md#get-started) para ver todas as políticas criadas em sua organização ou para descobrir quais aplicativos e entidades de serviço estão vinculados a uma política específica.

## <a name="policy-evaluation-and-prioritization"></a>Avaliação e priorização de política
Crie e atribua uma política de tempo de vida de token para um aplicativo específico, para sua organização e para entidades de serviço. Várias políticas podem se aplicar a um aplicativo específico. A política de tempo de vida do token que entra em vigor segue estas regras:

* Se uma política for atribuída explicitamente à entidade de serviço, ela será imposta.
* Se nenhuma política for explicitamente atribuída à entidade de serviço, uma política explicitamente atribuída à organização pai da entidade de serviço será imposta.
* Se nenhuma política foi atribuída explicitamente à entidade de serviço ou à organização, a política atribuída ao aplicativo será imposta.
* Se nenhuma política tiver sido atribuída à entidade de serviço, à organização ou ao objeto de aplicativo, os valores padrão serão impostos. (Consulte a tabela em [Propriedades configuráveis de tempo de vida de token](#configurable-token-lifetime-properties-after-the-retirement).)

Para saber mais sobre a relação entre objetos de aplicativo e de entidade de serviço, confira [Objetos de aplicativos e entidade de serviço no Azure Active Directory](app-objects-and-service-principals.md).

A validade do token é avaliada no momento em que ele é usado. A política com a prioridade mais alta no aplicativo que está sendo acessado entra em vigor.

Todos os períodos de tempo usados aqui são formatados de acordo com o objeto C# [TimeSpan](/dotnet/api/system.timespan) - D.HH:MM:SS.  Assim, 80 dias e 30 minutos seria `80.00:30:00`.  Os D principal pode ser descartado se for zero, então 90 minutos seria `00:90:00`.  

### <a name="example-scenario"></a>Cenário de exemplo

Um usuário deseja acessar dois aplicativos Web: Aplicativo Web A e Aplicativo Web B.

Fatores:
* Os dois aplicativos Web estão na mesma organização pai.
* Política de tempo de vida de token 1 com uma Idade Máxima do Token de Sessão de oito horas é definida como o padrão da organização pai.
* O Aplicativo Web A é um aplicativo de uso normal e não está vinculado a políticas.
* O Aplicativo Web B é usado para processos altamente confidenciais. Essa entidade de serviço é vinculada à Política de tempo de vida de token 2, que tem uma Idade Máxima de Token de Sessão de 30 minutos.

Às 12:00 PM, o usuário inicia uma nova sessão do navegador e tenta acessar o aplicativo Web A. O usuário é redirecionado para a plataforma de identidade da Microsoft e é solicitado a entrar. Isso crie um cookie com um token de sessão no navegador. O usuário é redirecionado ao Aplicativo Web A com um token de ID que permite e ele acessar o aplicativo.

Às 12:15 PM, o usuário tenta acessar o aplicativo Web B. O navegador redireciona para a plataforma de identidade da Microsoft, que detecta o cookie de sessão. A entidade de serviço do Aplicativo Web B está vinculada à Política de tempo de vida de token 2, mas também faz parte da organização pai, com a Política de tempo de vida de token 1 padrão. A Política de tempo de vida de token 2 entra em vigor, pois as políticas vinculadas a entidades de serviço têm uma prioridade maior do que as políticas padrão de organização. O token de sessão foi originalmente emitido nos últimos 30 minutos. Portanto, ele é considerado válido. O usuário é redirecionado para o Aplicativo Web B com um token de ID que lhe concede acesso.

Às 1:00 PM, o usuário tenta acessar o aplicativo Web A. O usuário é redirecionado para a plataforma de identidade da Microsoft. O Aplicativo Web A não está vinculado a qualquer política, mas como está em uma organização com a Política de tempo de vida de token 1 padrão, essa política entra em vigor. O cookie de sessão que foi originalmente emitido nas últimas oito horas é detectado. O usuário é redirecionado silenciosamente de volta ao Aplicativo Web A com um novo token de ID. O usuário não precisa autenticar.

Imediatamente depois, o usuário tenta acessar o aplicativo Web B. O usuário é redirecionado para a plataforma de identidade da Microsoft. Como antes, a Política de tempo de vida de token 2 entra em vigor. Como o token foi emitido há mais de 30 minutos, o usuário recebe uma solicitação para reinserir suas credenciais de logon. Um token de sessão e um token de ID totalmente novos são emitidos. Assim, o usuário pode acessar o Aplicativo Web B.

## <a name="cmdlet-reference"></a>Referência de cmdlet

Estes são os cmdlets no [módulo Azure Active Directory PowerShell for Graph Preview](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Gerenciar políticas

Use estes cmdlets para gerenciar as políticas.

| Cmdlet | Descrição | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Cria uma nova política. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtém todas as políticas do Azure AD ou a política especificada. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Obtém todos os aplicativos e entidades de serviço vinculados a uma política. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Atualiza uma política existente. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Exclui a política especificada. |

### <a name="application-policies"></a>Políticas de aplicativo
Use os cmdlets a seguir para políticas de aplicativos.</br></br>

| Cmdlet | Descrição | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Vincula a política especificada a um aplicativo. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtém a política atribuída a um aplicativo. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Remove uma política de um aplicativo. |

### <a name="service-principal-policies"></a>Políticas de entidade de serviço
Use os cmdlets a seguir para políticas de entidade de serviço.

| Cmdlet | Descrição | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Vincula a política especificada a uma entidade de serviço. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Obtém qualquer política vinculada à entidade de serviço especificada.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Remove a política da entidade da entidade de serviço especificada.|

## <a name="next-steps"></a>Próximas etapas

Para saber mais, leia [exemplos de como configurar tempos de vida de token](configure-token-lifetimes.md).
