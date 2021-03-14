---
title: Solucionar problemas de verificação do Publicador | Azure
titleSuffix: Microsoft identity platform
description: Descreve como solucionar problemas de verificação do editor para a plataforma Microsoft Identity chamando Microsoft Graph APIs.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: acce282dcaac971716a97d7722543d78e28b7a34
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517660"
---
# <a name="troubleshoot-publisher-verification"></a>Solucionar problemas de verificação do editor
Se você não conseguir concluir o processo ou estiver experimentando um comportamento inesperado com a [verificação do Publicador](publisher-verification-overview.md), comece fazendo o seguinte se estiver recebendo erros ou observando um comportamento inesperado: 

1. Examine os [requisitos](publisher-verification-overview.md#requirements) e certifique-se de que todos foram atendidos.

1. Examine as instruções para [marcar um aplicativo como editor verificado](mark-app-as-publisher-verified.md) e certifique-se de que todas as etapas tenham sido executadas com êxito.

1. Examine a lista de [problemas comuns](#common-issues).

1. Reproduza a solicitação usando o [Explorador do Graph](#making-microsoft-graph-api-calls) para coletar informações adicionais e descartar quaisquer problemas na interface do usuário.

## <a name="common-issues"></a>Problemas comuns
Abaixo estão alguns problemas comuns que podem ocorrer durante o processo. 

- **Não sei minha ID de Microsoft Partner Network (ID de MPN) ou não sei quem é o contato principal da conta** 
    1. Navegue até a [página de registro do MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Entrar com uma conta de usuário no locatário principal do Azure AD da organização 
    1. Se já existir uma conta do MPN, ela será reconhecida e você será adicionado à conta 
    1. Navegue até a [página de perfil do parceiro](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) onde a ID do MPN e o contato principal da conta serão listados

- **Não sei quem meu administrador global do Azure AD (também conhecido como administrador da empresa ou administrador de locatários) é, como posso encontrá-los? E quanto ao administrador de aplicativos ou ao administrador de aplicativos na nuvem?**
    1. Entre no [Portal do Azure AD](https://aad.portal.azure.com) usando uma conta de usuário no locatário principal da sua organização
    1. Navegue até [Gerenciamento de funções](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Clique na função de administrador desejada
    1. A lista de usuários atribuídos àquela função será exibida

- **Não sei quem é ou quem são os administradores da minha conta do MPN** Vá para a [página de Gerenciamento de usuários do MPN](https://partner.microsoft.com/pcv/users) e filtre a lista de usuários para ver quais usuários estão nas diferentes funções de administrador.

- **Estou recebendo um erro dizendo que minha ID do MPN é inválida ou que não tenho acesso a ela.**
    1. Acesse o [perfil de parceiro](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) e verifique se: 
        - A ID do MPN está correta. 
        - Não há erros ou “ações pendentes” mostradas, e o status de verificação sob o perfil Negócio jurídico e as informações do Parceiro dizem “autorizado” ou “sucesso”.
    1. Vá para a [página de Gerenciamento de locatários do MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) e confirme se o locatário no qual o aplicativo está registrado e a conta de usuário que você está usando para entrar estão na lista de locatários associados. Para adicionar um locatário adicional, siga as instruções [aqui](/partner-center/multi-tenant-account). Esteja ciente de que todos os administradores globais de qualquer locatário adicionado receberão privilégios de administrador global em sua conta do Partner Center.
    1. Vá para a [página de gerenciamento de usuário do MPN](https://partner.microsoft.com/pcv/users) e confirme se o usuário no qual você está entrando é um administrador global, administrador do MPN ou administrador de contas. Para adicionar um usuário a uma função no Partner Center, siga as instruções [aqui](/partner-center/create-user-accounts-and-set-permissions).

- **Quando eu entro no portal do Azure AD, não vejo nenhum aplicativo registrado. Por quê?** 
    Os registros do aplicativo podem ter sido criados usando uma conta de usuário diferente neste locatário, uma conta pessoal/de consumidor ou em um locatário diferente. Verifique se você está conectado com a conta correta no locatário onde os registros do aplicativo foram criados.

- **Estou recebendo um erro relacionado à autenticação multifator. O que devo fazer?** 
    Verifique se a [autenticação multifator](../fundamentals/concept-fundamentals-mfa-get-started.md) está habilitada e é **necessária** para o usuário com o qual você está entrando e para esse cenário. Por exemplo, a MFA pode ser:
    - Sempre necessário para o usuário com o qual você está entrando
    - [Necessário para o gerenciamento do Azure](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Necessário para o tipo de administrador com o](../conditional-access/howto-conditional-access-policy-admin-mfa.md) qual você está entrando.

## <a name="making-microsoft-graph-api-calls"></a>Chamar a API do Microsoft Graph 

Se você está tendo um problema, mas não consegue entender o motivo com base no que você está vendo na interface do usuário, pode ser útil executar outras soluções de problemas através de chamadas do Microsoft Graph para executar as mesmas operações que você pode executar no portal de Registro de aplicativo.

A maneira mais fácil de fazer essas solicitações é usando o [Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer). Você também pode considerar outras opções, como usar [Postman](https://www.postman.com/) ou PowerShell para [invocar uma solicitação da Web](/powershell/module/microsoft.powershell.utility/invoke-webrequest).  

Você pode usar o Microsoft Graph para definir e remover o editor verificado do seu aplicativo e verificar o resultado depois de executar uma dessas operações. O resultado pode ser visto no objeto de [aplicativo](/graph/api/resources/application) correspondente ao registro do aplicativo e a quaisquer [entidades de serviço](/graph/api/resources/serviceprincipal) que tenham sido instanciadas a partir desse aplicativo. Para obter mais informações sobre a relação entre esses objetos, consulte: [Objetos de entidade de serviço e aplicativo no Azure Active Directory](app-objects-and-service-principals.md).  

Aqui estão exemplos de algumas solicitações úteis:  

### <a name="set-verified-publisher"></a>Configurar editor verificado 

Solicitação

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Resposta 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* é sua ID do MPN. 

### <a name="unset-verified-publisher"></a>Remover definição de editor verificado 

Solicitação:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Resposta 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Obter informações do editor verificado a partir do aplicativo 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Obter informações do editor verificado a partir da entidade de serviço 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Referência de erro 

Veja a seguir uma lista dos códigos de erro potenciais que você pode receber, seja ao solucionar problemas com Microsoft Graph ou durante o processo no portal de registro de aplicativo.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

A ID de MPN fornecida (`MPNID`) não existe ou você não tem acesso a ela. Forneça uma ID de MPN válida e tente novamente.
    
Geralmente causado pelo usuário conectado que não é membro da função apropriada para a conta do MPN no Partner Center-consulte [os requisitos](publisher-verification-overview.md#requirements) para obter uma lista de funções qualificadas e veja [problemas comuns](#common-issues) para obter mais informações. Também pode ser causado pelo locatário no qual o aplicativo está registrado não está sendo adicionado à conta MPN ou uma ID de MPN inválida.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

A ID de MPN fornecida (`MPNID`) é inválida. Forneça uma ID de MPN válida e tente novamente.
    
Geralmente causado quando uma ID MPN é fornecida e corresponde a uma PLA (conta de local de parceiro). Somente contas globais do parceiro têm suporte. Confira [estrutura de conta do Partner Center](/partner-center/account-structure) para obter mais detalhes.

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

A ID de MPN fornecida (`MPNID`) é inválida. Forneça uma ID de MPN válida e tente novamente.
    
Geralmente causado pela ID de MPN incorreta que está sendo fornecida.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

A ID de MPN (`MPNID`) fornecida não concluiu o processo de verificação. Conclua esse processo no Partner Center e tente novamente. 
    
Geralmente causado pelo momento em que a conta MPN não concluiu o processo de [verificação](/partner-center/verification-responses) .

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

A ID de MPN fornecida (`MPNID`) é inválida. Forneça uma ID de MPN válida e tente novamente. 
   
Geralmente causado pela ID de MPN incorreta que está sendo fornecida.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

A ID de MPN fornecida (`MPNID`) é inválida. Forneça uma ID de MPN válida e tente novamente.
    
Geralmente causado pela ID de MPN incorreta que está sendo fornecida.

### <a name="applicationnotfound"></a>ApplicationNotFound

Não é possível localizar o aplicativo de destino (`AppId`). Forneça uma ID de aplicativo válida e tente novamente.
    
Geralmente causado quando a verificação está sendo executada via API do Graph, e a ID do aplicativo fornecido está incorreta. Observação-a ID do aplicativo deve ser fornecida, não a AppId/ClientId.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

Não há suporte para essa funcionalidade em um locatário do Azure AD B2C.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

Não há suporte para essa funcionalidade em um locatário verificado por email.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

O aplicativo de destino ( `AppId` ) deve ter um conjunto de domínios do Publicador. Defina um domínio do editor e tente novamente.

Ocorre quando um [domínio do Publicador](howto-configure-publisher-domain.md) não está configurado no aplicativo.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

O domínio do editor do aplicativo (`publisherDomain`) não corresponde ao domínio usado para executar a verificação por email no Partner Center (`pcDomain`). Verifique se esses domínios correspondem e tente novamente. 
    
Ocorre quando nem o domínio do [Editor](howto-configure-publisher-domain.md) do aplicativo nem um dos [domínios personalizados](../fundamentals/add-custom-domain.md) adicionados ao locatário do Azure ad correspondem ao domínio usado para executar a verificação de email no Partner Center.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

Você não está autorizado a definir a propriedade do Publicador verificada no aplicativo (<`AppId` ) 
  
Geralmente causado pelo usuário conectado que não é membro da função apropriada para a conta MPN no Azure AD – consulte [os requisitos](publisher-verification-overview.md#requirements) para obter uma lista de funções qualificadas e veja [problemas comuns](#common-issues) para obter mais informações.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

A ID do MPN não foi fornecida no corpo da solicitação ou o tipo de conteúdo da solicitação não era “aplicativo/json”.

### <a name="msanotsupported"></a>MSANotSupported 

Não há suporte para esse recurso em contas de consumidor da Microsoft. Somente os aplicativos registrados no Azure AD por um usuário do Azure AD têm suporte.

### <a name="interactionrequired"></a>InteractionRequired

Ocorre quando a autenticação multifator não foi executada antes de tentar adicionar um Publicador verificado ao aplicativo. Consulte [problemas comuns](#common-issues) para obter mais informações. Observação: a MFA deve ser executada na mesma sessão ao tentar adicionar um Publicador verificado. Se a MFA estiver habilitada, mas não precisar ser executada na sessão, a solicitação falhará. 

A mensagem de erro exibida será: "devido a uma alteração de configuração feita pelo administrador, ou porque você moveu para um novo local, você deve usar a autenticação multifator para continuar".

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

A mensagem de erro exibida é: "um Publicador verificado não pode ser adicionado a este aplicativo. Entre em contato com o administrador para obter assistência. "

Primeiro, verifique se você atendeu [aos requisitos de verificação do Publicador](publisher-verification-overview.md#requirements).

Quando uma solicitação para adicionar um Publicador verificado é feita, um número de sinais é usado para fazer uma avaliação de risco de segurança. Se a solicitação for considerada arriscada, um erro será retornado. Por motivos de segurança, a Microsoft não divulga os critérios específicos usados para determinar se uma solicitação é arriscada ou não.

## <a name="next-steps"></a>Próximas etapas

Se você revisou todas as informações anteriores e ainda continua recebendo um erro de Microsoft Graph, reúna o máximo possível das informações a seguir relacionadas à solicitação com falha e [entre em contato com o suporte da Microsoft](developer-support-help-options.md#create-an-azure-support-request).

- Timestamp 
- CorrelationId 
- ObjectID ou UserPrincipalName do usuário conectado 
- ObjectId do aplicativo de destino
- AppId do aplicativo de destino
- Tenantid onde o aplicativo está registrado
- ID MPN
- Solicitação REST sendo feita 
- Código de erro e mensagem sendo retornada