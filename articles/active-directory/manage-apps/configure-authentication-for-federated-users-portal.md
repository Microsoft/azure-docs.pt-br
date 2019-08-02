---
title: Configurar a aceleração automática de entrada usando a política de descoberta de realm inicial | Microsoft Docs
description: Saiba como configurar a política de descoberta de realm inicial para autenticação Azure Active Directory para usuários federados, incluindo dicas de domínio e aceleração automática.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8f51fcd69a7115879aad97bbf696833e87877b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477216"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configurar aceleração automática de entrada para um aplicativo usando a política Descoberta de Realm Inicial

Este artigo fornece uma introdução à configuração de Azure Active Directory comportamento de autenticação para usuários federados. Ele abrange a configuração das restrições de aceleração automática e autenticação para usuários em domínios federados.

## <a name="home-realm-discovery"></a>Descoberta de Realm Inicial
Home Realm Discovery (HRD) is the process that allows Azure Active Directory (Azure AD) to determine where a user needs to authenticate at sign-in time.  Ao entrar em um locatário do Azure AD para acessar um recurso, ou a página de entrada comum do Azure AD, um usuário digita um nome (UPN). O Azure AD usa esse nome para descobrir onde o usuário precisa entrar. 

O usuário talvez precise ser levado para um dos seguintes locais para ser autenticado:

- O locatário inicial do usuário (pode ser o mesmo locatário do recurso que o usuário está tentando acessar). 

- Conta da Microsoft.  O usuário é um convidado no locatário do recurso.

-  Um provedor de identidade local como Serviços de Federação do Active Directory (AD FS).

- Outro provedor de identidade federado com o locatário do Azure AD.

## <a name="auto-acceleration"></a>Aceleração automática 
Some organizations configure domains in their Azure Active Directory tenant to federate with another IdP, such as AD FS for user authentication.  

When a user signs into an application, they are first presented with an Azure AD sign-in page. After they have typed their UPN, if they are in a federated domain they are then taken to the sign-in page of the IdP serving that domain. Em determinadas circunstâncias, os administradores talvez queiram direcionar usuários para a página de entrada quando estes estiverem entrando em aplicativos específicos. 

As a result users can skip the initial Azure Active Directory page. Esse processo é conhecido como "aceleração automática de entrada".

Em casos nos quais o locatário for federado com outro IdP para entrar, a aceleração automática aprimora mais a entrada.  Você pode configurar a aceleração automática para aplicativos individuais.

>[!NOTE]
>Se você configurar um aplicativo para aceleração automática, os usuários convidados não poderão entrar. Se você levar um usuário diretamente para um IdP federado para autenticação, não haverá maneira para ele voltar à página de entrada do Azure Active Directory. Usuários convidados, que talvez precisem ser direcionados para um outros locatários ou um IdP externo como uma conta Microsoft, não poderão entrar nesse aplicativo porque estão ignorando a etapa da Descoberta de Realm Inicial.  

Há duas maneiras de controlar a aceleração automática para um IdP federado:   

- Use uma dica de domínio em solicitações de autenticação para um aplicativo. 
- Configure uma política Descoberta de Realm Inicial para habilitar a aceleração automática.

### <a name="domain-hints"></a>Dicas de domínio    
Dicas de domínio são diretivas incluídas na solicitação de autenticação de um aplicativo. Elas podem ser usadas para agilizar o usuário até a página de entrada IdP. Ou elas podem ser usadas por um aplicativo multilocatário para agilizar o usuário diretamente para a página de entrada do Azure AD da marca do locatário.  

Por exemplo, o aplicativo “largeapp.com” pode permitir que os clientes acessem o aplicativo em um URL personalizado “contoso.largeapp.com”. O aplicativo também pode incluir uma dica de domínio para contoso.com na solicitação de autenticação. 

A sintaxe da dica de domínio varia de acordo com o protocolo usado, e ela é normalmente configurada no aplicativo.

**Web Services Federation**: whr=contoso.com na cadeia de caracteres de consulta.

**SAML**:  uma solicitação de autenticação SAML que contém uma dica de domínio ou uma cadeia de caracteres de consulta whr=contoso.com.

**Open ID Connect**: uma cadeia de caracteres de consulta domain_hint=contoso.com. 

Se uma dica de domínio estiver incluída na solicitação de autenticação do aplicativo e o locatário for federado com esse domínio, o Azure AD tentará redirecionar a entrada para o IdP configurado para esse domínio. 

Se a dica de domínio não se referir a um domínio federado verificado, ele será ignorado, e a Descoberta de Realm Inicial normal será invocada.

Para obter mais informações sobre aceleração automática usando as dicas de domínio compatíveis com o Azure Active Directory, consulte o blog [Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>If a domain hint is included in an authentication request, its presence overrides auto-acceleration that is set for the application in HRD policy.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Home Realm Discovery policy for auto-acceleration
Alguns aplicativos não oferecem uma maneira de configurar a solicitação de autenticação emitida. Nesses casos, não é possível usar dicas de domínio para controlar a aceleração automática. Aceleração automática pode ser configurada por meio da política para alcançar o mesmo comportamento.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Ativar autenticação direta para aplicativos herdados
A melhor prática é que os aplicativos usem bibliotecas AAD e o login interativo para autenticar usuários. As bibliotecas cuidam dos fluxos de usuários federados.  Às vezes, aplicativos herdados não são escritos para entender a federação. Eles não executam a descoberta de território inicial e não interagem com o endpoint federado correto para autenticar um usuário. Se preferir, você pode usar a Política de HRD para permitir que aplicativos herdados específicos que enviem credenciais de nome de usuário / senha sejam autenticados diretamente no Azure Active Directory. A sincronização de hash de senha deve estar ativada. 

> [!IMPORTANT]
> Ative a autenticação direta somente se você tiver a sincronização de hash de senhas ativada e você souber que está correto autenticar esse aplicativo sem nenhuma política implementada pelo seu IdP local. Se você desativar a Sincronização de Hash de Senha ou desativar a Sincronização de Diretórios com o AD Connect por qualquer motivo, remova essa política para evitar a possibilidade de autenticação direta usando um hash de senha antiga.

## <a name="set-hrd-policy"></a>Definir política HRD
Há três etapas para configurar a política de HRD em um aplicativo para aplicativos de auto-aceleração de entrada federada ou diretos baseados em nuvem:

1. Crie uma política de HRD.

2. Localize o principal de serviço ao qual anexar a política.

3. Anexe a política ao responsável pelo serviço. 

As políticas só entram em vigor para um aplicativo específico quando são anexadas a um responsável pelo serviço. 

Apenas uma política de HRD pode estar ativa em uma entidade de serviço a qualquer momento.  

Você pode usar diretamente a API do Microsoft Active Directory Graph, ou os cmdlets do Azure Active Directory do PowerShell para criar e gerenciar diretivas de HRD.

A API do Graph que manipula a política é descrita no artigo [Operações na política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) no MSDN.

Aqui está uma definição de política HRD de exemplo:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

O tipo de política é “HomeRealmDiscoveryPolicy”.

**AccelerateToFederatedDomain** é opcional. Se **AccelerateToFederatedDomain** for falso, a política não terá efeito na aceleração automática. Se **AccelerateToFederatedDomain** for true e houver apenas um domínio verificado e federado no locatário, os usuários serão levados diretamente para o IdP federado para login. Se for verdadeiro e houver mais de um domínio verificado no locatário, **PreferredDomain** deve ser especificado.

**PreferredDomain** é opcional. **PreferredDomain** deve indicar um domínio para o qual acelerar. Ele poderá ser omitido se o locatário tiver apenas um domínio federado.  Se ele for omitido e houver mais de um domínio federado verificado, a política não entrará em vigor.

 Se **PreferredDomain** for especificado, ele deverá corresponder a um domínio federado, verificado, para o locatário. Todos os usuários do aplicativo devem ser capazes de entrar nesse domínio.

**AllowCloudPasswordValidation** é opcional. Se **AllowCloudPasswordValidation** for true, o aplicativo poderá autenticar um usuário federado apresentando credenciais de nome de usuário / senha diretamente ao terminal do token do Azure Active Directory. Isso só funciona se o Password Hash Sync estiver ativado.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioridade e avaliação de políticas HRD
As políticas HRD podem ser criadas e atribuídas a organizações e entidades de serviço específicos. Isso significa que é possível que várias políticas se apliquem a um aplicativo específico. A política HRD que entra em vigor segue estas regras:


- Se uma dica de domínio estiver presente na solicitação de autenticação, qualquer política de HRD será ignorada para a aceleração automática. O comportamento especificado pela dica de domínio é usado.

- Pelo contrário, se uma política for atribuída explicitamente à entidade de serviço, ela será imposta. 

- Se nenhuma dica de política e nenhuma política forem atribuídas explicitamente à entidade de serviço, uma política atribuída explicitamente à organização pai da entidade de serviço será imposta. 

- Se não há nenhuma dica de domínio, e nenhuma política foi atribuída à entidade de serviço principal ou da organização, o comportamento de HDR padrão será usado.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Tutorial para definir a política de HRD em um aplicativo 
Usaremos os cmdlets do PowerShell do Azure AD para percorrer a alguns cenários, inclusive:


- Configurando a política de HRD para fazer a aceleração automática de um aplicativo em um locatário com um único domínio federado.

- Configurando a política do HRD para fazer a aceleração automática de um aplicativo em um dos vários domínios verificados para o seu locatário.

- Configurando a política de HRD para permitir que um aplicativo legado faça autenticação direta de nome de usuário / senha no Azure Active Directory para um usuário federado.

- Listando os aplicativos para os quais uma política é configurada.


### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, você cria, atualiza, vincula e exclui políticas em entidades de serviço do aplicativo no Azure AD.

1.  Para começar, faça o download da visualização do cmdlet do PowerShell do Azure AD mais recente. 

2.  Depois que você tiver baixado os cmdlets do PowerShell do Azure AD, execute o comando Conectar para entrar no Azure AD com a conta de administrador:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Execute o comando a seguir para ver todas as políticas na organização:

    ``` powershell
    Get-AzureADPolicy
    ```

Se não retornar nada, você não terá políticas criadas no locatário.

### <a name="example-set-hrd-policy-for-an-application"></a>Exemplo: Definir política de HRD para um aplicativo 

Neste exemplo, você cria uma política que, quando é atribuída a um aplicativo: 
- Acelera automaticamente os usuários para uma tela de login do AD FS quando eles estão entrando em um aplicativo quando há um único domínio em seu locatário. 
- Acelera automaticamente os usuários para uma tela de entrada do AD FS, há mais de um domínio federado em seu locatário.
- Permite que o nome de usuário / senha não-interativo entre diretamente no Azure Active Directory para usuários federados para os aplicativos aos quais a política está atribuída.

#### <a name="step-1-create-an-hrd-policy"></a>Etapa 1: Criar uma política de HRD

A diretiva a seguir acelera automaticamente os usuários para uma tela de entrada do AD FS quando eles estão entrando em um aplicativo quando há um único domínio em seu locatário.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
A política a seguir acelera automaticamente os usuários para uma tela de entrada do AD FS em que há mais de um domínio federado em seu locatário. Se você tiver mais de um domínio federado que autentica usuários para aplicativos, será necessário especificar o domínio para acelerar automaticamente.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Para criar uma política para habilitar a autenticação de nome de usuário / senha para usuários federados diretamente no Azure Active Directory para aplicativos específicos, execute o seguinte comando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Para ver a nova política e obter a **ObjectID**, execute o seguinte comando:

``` powershell
Get-AzureADPolicy
```


Para aplicar a política de HRD depois de criá-la, você pode atribuí-la a várias entidades de serviço de aplicativo.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Etapa 2: Localizar a entidade de serviço à qual atribuir a política  
Você precisa da **ObjectID** das entidades de serviço às quais deseja atribuir a política. Há várias maneiras de encontrar a **ObjectID** de entidades de serviço.    

Você pode usar o portal ou consultar [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Você também pode ir até a [Ferramenta Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer) e entrar na conta do Azure AD para ver todas as entidades de serviço da organização. 

Como você está usando o PowerShell, você pode usar o cmdlet a seguir para listar as entidades de serviço e suas IDs.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Etapa 3: Atribuir a política à entidade de serviço  
Depois que você tiver a **ObjectID** da entidade de serviço do aplicativo para o qual deseja configurar a aceleração automática, execute o comando a seguir. Esse comando associa a política HRD que você criou na etapa 1 à entidade de serviço que você localizou na etapa 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Você pode repetir esse comando para cada entidade de serviço principal à qual deseja adicionar a política.

No caso de um aplicativo já ter uma política de HomeRealmDiscovery atribuída, você não poderá adicionar um segundo.  Nesse caso, altere a definição da diretiva Descoberta de Domínio Residencial que é atribuída ao aplicativo para adicionar parâmetros adicionais.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Etapa 4: Verificar a quais entidades de serviço de aplicativo a sua política de HRD está atribuída
Para verificar quais aplicativos têm a política de HRD configurada, use o cmdlet **Get-AzureADPolicyAppliedObject**. Passe o **ObjectID** da política que você deseja verificar.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Etapa 5: Pronto!
Tente o aplicativo para verificar se a nova política está funcionando.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exemplo: Listar os aplicativos para os quais a política de HRD está configurada

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Etapa 1: Listar todas as políticas que foram criadas na organização 

``` powershell
Get-AzureADPolicy
```

Observe o **ObjectID** da política cujas atribuições você deseja listar.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Etapa 2: Listar as entidades de serviço às quais a política está atribuída  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Exemplo: Remover a política de HRD de um aplicativo
#### <a name="step-1-get-the-objectid"></a>Etapa 1: Obter a ObjectID
Use o exemplo anterior para obter a **ObjectID** da política e a entidade de serviço do aplicativo da qual você deseja removê-la. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Etapa 2: Remover a atribuição de política da entidade de serviço do aplicativo  

``` powershell
Remove-AzureADApplicationPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Etapa 3: Verificar a remoção listando as entidades de serviço às quais a política está atribuída 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre como a autenticação funciona no Azure AD, consulte [Cenários de autenticação do Azure AD](../develop/authentication-scenarios.md).
- Para obter mais informações sobre o logon único do usuário, consulte [logon único para aplicativos no Azure Active Directory](what-is-single-sign-on.md).
- Visite o [Guia do desenvolvedor do Active Directory](../develop/v1-overview.md) para obter uma visão geral de todo o conteúdo relacionado a desenvolvedor.
