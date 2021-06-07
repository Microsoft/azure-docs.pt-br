---
title: Aplicativos cliente públicos de conta única e múltipla | Azure
description: Uma visão geral de aplicativos cliente públicos de conta única e múltipla.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752980"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Aplicativos cliente públicos de conta única e múltipla

Este artigo o ajudará a entender os tipos usados em aplicativos cliente públicos de conta única e múltipla, com foco em aplicativos cliente públicos de conta única. 

A ADAL (biblioteca de autenticação Azure Active Directory) modela o servidor.  Em vez disso, a MSAL (biblioteca de autenticação da Microsoft) modela o aplicativo cliente.  A maioria dos aplicativos Android é considerada clientes públicos. Um cliente público é um aplicativo que não pode manter um segredo com segurança.  

O MSAL especializada a superfície de API do `PublicClientApplication` para simplificar e esclarecer a experiência de desenvolvimento para aplicativos que permitem que apenas uma conta seja usada de cada vez. `PublicClientApplication` é subclasse por `SingleAccountPublicClientApplication` e `MultipleAccountPublicClientApplication` .  O diagrama a seguir mostra a relação entre essas classes.

![Diagrama de classe UML SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Aplicativo cliente público de conta única

A `SingleAccountPublicClientApplication` classe permite que você crie um aplicativo baseado em MSAL que permite que apenas uma única conta seja conectada por vez. `SingleAccountPublicClientApplication` é diferente de `PublicClientApplication` nestes aspectos:

- MSAL rastreia a conta atualmente conectada.
  - Se seu aplicativo estiver usando um agente (o padrão durante o registro do aplicativo portal do Azure) e estiver instalado em um dispositivo em que um agente está presente, o MSAL verificará se a conta ainda está disponível no dispositivo.
- `signIn` permite que você conecte uma conta explicitamente e separadamente dos escopos de solicitação.
- `acquireTokenSilent` Não requer um parâmetro de conta.  Se você fornecer uma conta e a conta fornecida não corresponder à conta atual controlada pelo MSAL, um `MsalClientException` será gerado.
- `acquireToken` Não permite que o usuário alterne contas. Se o usuário tentar alternar para uma conta diferente, uma exceção será lançada.
- `getCurrentAccount` Retorna um objeto de resultado que fornece o seguinte:
  - Um booliano que indica se a conta foi alterada. Uma conta pode ser alterada como resultado de ser removida do dispositivo, por exemplo.
  - A conta anterior. Isso será útil se você precisar fazer qualquer limpeza de dados local quando a conta for removida do dispositivo ou quando uma nova conta estiver conectada.
  - O currentAccount.
- `signOut` Remove todos os tokens associados ao cliente do dispositivo.  

Quando um agente de autenticação do Android, como Microsoft Authenticator ou Portal da Empresa do Intune, estiver instalado no dispositivo e seu aplicativo estiver configurado para usar o agente, o `signOut` não removerá a conta do dispositivo.

## <a name="single-account-scenario"></a>Cenário de conta única

O pseudocódigo a seguir ilustra o uso do `SingleAccountPublicClientApplication` .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Aplicativo cliente público de várias contas

A `MultipleAccountPublicClientApplication` classe é usada para criar aplicativos baseados em MSAL que permitem que várias contas sejam conectadas ao mesmo tempo. Ele permite que você obtenha, adicione e remova contas da seguinte maneira:

### <a name="add-an-account"></a>Adicionar uma conta

Use uma ou mais contas em seu aplicativo chamando `acquireToken` uma ou mais vezes.  

### <a name="get-accounts"></a>Obter contas

- Chame `getAccount` para obter uma conta específica.
- Chame `getAccounts` para obter uma lista de contas conhecidas no momento para o aplicativo.

Seu aplicativo não poderá enumerar todas as contas da plataforma de identidade da Microsoft no dispositivo conhecido pelo aplicativo agente. Ele só pode enumerar contas que foram usadas pelo seu aplicativo.  As contas que foram removidas do dispositivo não serão retornadas por essas funções.

### <a name="remove-an-account"></a>Remover uma conta

Remova uma conta chamando `removeAccount` com um identificador de conta.

Se seu aplicativo estiver configurado para usar um agente e um agente estiver instalado no dispositivo, a conta não será removida do agente quando você chamar `removeAccount` .  Somente os tokens associados ao cliente são removidos.

## <a name="multiple-account-scenario"></a>Cenário de conta múltipla

O pseudocódigo a seguir mostra como criar um aplicativo de várias contas, listar contas no dispositivo e adquirir tokens.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
