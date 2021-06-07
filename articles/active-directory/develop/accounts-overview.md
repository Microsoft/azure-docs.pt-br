---
title: Contas da plataforma de identidade da Microsoft & perfis de locatário no Android | Azure
description: Uma visão geral das contas da plataforma de identidade da Microsoft para Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755969"
---
# <a name="accounts--tenant-profiles-android"></a>Contas e perfis de locatário (Android)

Este artigo fornece uma visão geral do que `account` é uma na plataforma de identidade da Microsoft.

A API da MSAL (biblioteca de autenticação da Microsoft) substitui o termo *usuário* pela *conta* do termo. Um dos motivos é que um usuário (agente humano ou de software) pode ter ou pode usar várias contas. Essas contas podem estar na própria organização do usuário e/ou em outras organizações das quais o usuário é membro.

Uma conta na plataforma Microsoft Identity consiste em:

- Um identificador exclusivo.  
- Uma ou mais credenciais usadas para demonstrar a propriedade/o controle da conta.
- Um ou mais perfis que consistem em atributos como:
  - Imagem, nome fornecido, nome da família, título, localização do escritório
- Uma conta tem uma fonte de autoridade ou sistema de registro. Esse é o sistema em que a conta é criada e onde as credenciais associadas a essa conta são armazenadas. Em sistemas multilocatários como a plataforma Microsoft Identity, o sistema de registro é o `tenant` local em que a conta foi criada. Esse locatário também é chamado de `home tenant` .
- As contas na plataforma de identidade da Microsoft têm os seguintes sistemas de registro:
  - Azure Active Directory, incluindo Azure Active Directory B2C.
  - Conta Microsoft (em tempo real).
- Contas de sistemas de registro fora da plataforma de identidade da Microsoft são representadas na plataforma Microsoft Identity, incluindo:
  - identidades de diretórios locais conectados (Windows Server Active Directory)
  - identidades externas do LinkedIn, GitHub e assim por diante.
  Nesses casos, uma conta tem um sistema de origem de registro e um sistema de registro na plataforma de identidade da Microsoft.
- A plataforma de identidade da Microsoft permite que uma conta seja usada para acessar recursos pertencentes a várias organizações (Azure Active Directory locatários).
  - Para registrar que uma conta de um sistema de registro (locatário do AAD A) tem acesso a um recurso em outro sistema de registro (locatário do AAD B), a conta deve ser representada no locatário em que o recurso está definido. Isso é feito criando um registro local da conta do sistema A no sistema B.
  - Esse registro local, que é a representação da conta, está associado à conta original.
  - MSAL expõe esse registro local como um `Tenant Profile` .
  - O perfil do locatário pode ter atributos diferentes que são apropriados para o contexto local, como cargo, local do escritório, informações de contato, etc.
- Como uma conta pode estar presente em um ou mais locatários, uma conta pode ter mais de um perfil.

> [!NOTE]
> MSAL trata o sistema de conta Microsoft (Live, MSA) como outro locatário na plataforma de identidade da Microsoft. A ID do locatário do locatário do conta Microsoft é: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagrama de visão geral da conta

![Diagrama de visão geral da conta](./media/accounts-overview/accounts-overview.svg)

No diagrama acima:

- A conta `bob@contoso.com` é criada no Windows Server local Active Directory (origem do registro no sistema local).
- A conta `tom@live.com` é criada no locatário conta Microsoft.
- `bob@contoso.com` tem acesso a pelo menos um recurso nos seguintes locatários Azure Active Directory:
  - contoso.com (sistema em nuvem de registro vinculado ao sistema local de registro)
  - fabrikam.com
  - woodgrovebank.com
  - Um perfil de locatário `bob@contoso.com` existe em cada um desses locatários.
- `tom@live.com` tem acesso aos recursos nos seguintes locatários da Microsoft:
  - contoso.com
  - fabrikam.com
  - Um perfil de locatário `tom@live.com` existe em cada um desses locatários.
- As informações sobre Tom e Bob em outros locatários podem ser diferentes daquelas no sistema de registro. Eles podem diferir por atributos como cargo, local do escritório e assim por diante. Eles podem ser membros de grupos e/ou funções dentro de cada organização (Azure Active Directory locatário). Nós nos referimos a essas informações como bob@contoso.com perfil de locatário.

No diagrama, bob@contoso.com e tom@live.com têm acesso a recursos em locatários Azure Active Directory diferentes. Para obter mais informações, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../external-identities/add-users-administrator.md).

## <a name="accounts-and-single-sign-on-sso"></a>Contas e logon único (SSO)

O cache do token MSAL armazena um *único token de atualização* por conta. Esse token de atualização pode ser usado para solicitar silenciosamente tokens de acesso de vários locatários da plataforma Microsoft Identity. Quando um agente é instalado em um dispositivo, a conta é gerenciada pelo agente e o logon único em todo o dispositivo se torna possível.

> [!IMPORTANT]
> O comportamento de token de atualização e de conta de consumidor para consumidores é diferente do restante da plataforma de identidade da Microsoft. Para obter mais informações, consulte [políticas do B2C & contas](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identificadores de conta

A ID da conta MSAL não é uma ID de objeto de conta. Ele não deve ser analisado e/ou confiado para transmitir qualquer coisa além da exclusividade na plataforma de identidade da Microsoft.

Para compatibilidade com a ADAL (biblioteca de autenticação do Azure AD) e para facilitar a migração do ADAL para o MSAL, o MSAL pode pesquisar contas usando qualquer identificador válido para a conta disponível no cache MSAL.  Por exemplo, o seguinte sempre recuperará o mesmo objeto de conta para, tom@live.com pois cada um dos identificadores é válido:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Acessando declarações sobre uma conta

Além de solicitar um token de acesso, o MSAL também solicita sempre um token de ID de cada locatário. Ele faz isso solicitando sempre os seguintes escopos:

- openid
- perfil

O token de ID contém uma lista de declarações. `Claims` são pares de nome/valor sobre a conta e são usados para fazer a solicitação.

Como mencionado anteriormente, cada locatário em que uma conta existe pode armazenar informações diferentes sobre a conta, incluindo, mas não se limitando a atributos como: cargo, local do escritório e assim por diante.

Embora uma conta possa ser membro ou convidada em várias organizações, o MSAL não consulta um serviço para obter uma lista dos locatários dos quais a conta é membro. Em vez disso, o MSAL cria uma lista de locatários em que a conta está presente, como resultado de solicitações de token que foram feitas.

As declarações expostas no objeto Account são sempre as declarações do/{Authority} "Home locatário" para uma conta. Se essa conta não tiver sido usada para solicitar um token para seu locatário inicial, o MSAL não poderá fornecer declarações por meio do objeto Account.  Por exemplo:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Para ver uma lista de declarações disponíveis no objeto Account, consulte [declarações em um id_token](./id-tokens.md#claims-in-an-id_token)

> [!TIP]
> Para incluir declarações adicionais em seu id_token, consulte a documentação de declarações opcionais em [como fornecer declarações opcionais para seu aplicativo do Azure ad](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>Acessar declarações de perfil de locatário

Para acessar declarações sobre uma conta como aparecem em outros locatários, primeiro você precisa converter seu objeto de conta no `IMultiTenantAccount` . Todas as contas podem ser multilocatário, mas o número de perfis de locatário disponíveis via MSAL é baseado em quais locatários você solicitou tokens usando a conta atual.  Por exemplo:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Políticas do B2C & contas

Os tokens de atualização para uma conta não são compartilhados entre as políticas B2C. Como resultado, o logon único usando tokens não é possível. Isso não significa que o logon único não seja possível. Isso significa que o logon único precisa usar uma experiência interativa na qual um cookie está disponível para habilitar o logon único.

Isso também significa que, no caso de MSAL, se você adquirir tokens usando políticas B2C diferentes, eles serão tratados como contas separadas, cada um com seu próprio identificador. Se você quiser usar uma conta para solicitar um token usando `acquireTokenSilent` , precisará selecionar a conta na lista de contas que corresponde à política que você está usando com a solicitação de token. Por exemplo:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```