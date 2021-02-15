---
title: Alterar o tipo de autenticação de subdomínio usando o PowerShell e o Graph-Azure Active Directory | Microsoft Docs
description: Altere as configurações de autenticação de subdomínio padrão herdadas das configurações de domínio raiz em Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503679"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Alterar o tipo de autenticação de subdomínio no Azure Active Directory

Depois que um domínio raiz é adicionado ao Azure Active Directory (Azure AD), todos os subdomínios subsequentes adicionados a essa raiz em sua organização do Azure AD herdam automaticamente a configuração de autenticação do domínio raiz. No entanto, se você quiser gerenciar as configurações de autenticação de domínio independentemente das configurações de domínio raiz, agora você pode com a API Microsoft Graph. Por exemplo, se você tiver um domínio raiz federado, como contoso.com, este artigo poderá ajudá-lo a verificar um subdomínio, como o child.contoso.com, como gerenciado, em vez de federado.

No portal do AD do Azure, quando o domínio pai é federado e o administrador tenta verificar um subdomínio gerenciado na página **nomes de domínio personalizados** , você receberá um erro ' falha ao adicionar domínio ' com o motivo "uma ou mais propriedades contêm valores inválidos". Se você tentar adicionar esse subdomínio do centro de administração Microsoft 365, receberá um erro semelhante. Para obter mais informações sobre o erro, consulte [um domínio filho não herda alterações de domínio pai no Office 365, no Azure ou no Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Como verificar um subdomínio personalizado

Como os subdomínios herdam o tipo de autenticação do domínio raiz por padrão, é necessário promover o subdomínio para um domínio raiz no Azure AD usando o Microsoft Graph para que você possa definir o tipo de autenticação para o tipo desejado.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Adicionar o subdomínio e exibir seu tipo de autenticação

1. Use o PowerShell para adicionar o novo subdomínio, que tem o tipo de autenticação padrão do seu domínio raiz. Os centros de administração do Azure AD e Microsoft 365 ainda não dão suporte a essa operação.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Use o [Explorador do Azure ad Graph](https://graphexplorer.azurewebsites.net) para obter o domínio. Como o domínio não é um domínio raiz, ele herda o tipo de autenticação de domínio raiz. O comando e os resultados podem ter a seguinte aparência, usando sua própria ID de locatário:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Usar a API do Gerenciador do Graph do Azure AD para tornar este um domínio raiz

Use o seguinte comando para promover o subdomínio:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Alterar o tipo de autenticação de subdomínio

1. Use o seguinte comando para alterar o tipo de autenticação de subdomínio:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Verifique por meio de GET no Azure AD Graph Explorer que o tipo de autenticação de subdomínio agora é gerenciado:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Próximas etapas

- [Adicionar nomes de domínio personalizados](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Gerenciar nomes de domínio](domains-manage.md)
- [Aplicar ForceDelete a um nome de domínio personalizado com a API do Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)