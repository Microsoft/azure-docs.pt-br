---
title: Contas de serviço gerenciado de grupo para Azure AD Domain Services | Microsoft Docs
description: Saiba como criar uma conta de serviço gerenciado de grupo (gMSA) para uso com Azure Active Directory Domain Services domínios gerenciados
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619977"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Criar uma conta de serviço gerenciado de grupo (gMSA) no Azure Active Directory Domain Services

Normalmente, os aplicativos e serviços precisam de uma identidade para se autenticar com outros recursos. Por exemplo, um serviço Web pode precisar se autenticar com um serviço de banco de dados. Se um aplicativo ou serviço tiver várias instâncias, como um farm de servidores Web, criar e configurar manualmente as identidades para esses recursos levará tempo.

Em vez disso, uma conta de serviço gerenciado de grupo (gMSA) pode ser criada no domínio gerenciado Azure Active Directory Domain Services (Azure AD DS). O sistema operacional do Windows gerencia automaticamente as credenciais de um gMSA, o que simplifica o gerenciamento de grandes grupos de recursos.

Este artigo mostra como criar um gMSA em um domínio gerenciado usando Azure PowerShell.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, conclua o tutorial para [criar e configurar um Azure Active Directory Domain Services domínio gerenciado][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM de gerenciamento][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Visão geral das contas de serviço gerenciado

Uma conta de serviço gerenciado autônoma (sMSA) é uma conta de domínio cuja senha é gerenciada automaticamente. Essa abordagem simplifica o gerenciamento de SPN (nome da entidade de serviço) e permite o gerenciamento delegado para outros administradores. Você não precisa criar e girar manualmente as credenciais para a conta.

Uma conta de serviço gerenciado de grupo (gMSA) fornece a mesma simplificação de gerenciamento, mas para vários servidores no domínio. Um gMSA permite que todas as instâncias de um serviço hospedadas em um farm de servidores usem a mesma entidade de serviço para que os protocolos de autenticação mútua funcionem. Quando um gMSA é usado como entidade de serviço, o sistema operacional Windows gerencia novamente a senha da conta em vez de depender do administrador.

Para obter mais informações, consulte [visão geral de contas de serviço gerenciado de grupo (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Usando contas de serviço no Azure AD DS

Como os domínios gerenciados são bloqueados e gerenciados pela Microsoft, há algumas considerações ao usar contas de serviço:

* Crie contas de serviço em unidades organizacionais (UO) personalizadas no domínio gerenciado.
    * Você não pode criar uma conta de serviço nos UOs *AADDC usuários* internos ou *AADDC computadores* .
    * Em vez disso, [crie uma UO personalizada][create-custom-ou] no domínio gerenciado e, em seguida, crie contas de serviço nessa UO personalizada.
* A chave raiz dos KDS (serviços de distribuição de chaves) foi criada previamente.
    * A chave raiz KDS é usada para gerar e recuperar senhas para gMSAs. No Azure AD DS, a raiz KDS é criada para você.
    * Você não tem privilégios para criar outro ou exibir a chave raiz padrão KDS.

## <a name="create-a-gmsa"></a>Criar uma gMSA

Primeiro, crie uma UO personalizada usando o cmdlet [New-ADOrganizationalUnit][New-AdOrganizationalUnit] . Para obter mais informações sobre como criar e gerenciar UOs personalizadas, consulte [UOs personalizadas no Azure AD DS][create-custom-ou].

> [!TIP]
> Para concluir essas etapas para criar um gMSA, [Use sua VM de gerenciamento][tutorial-create-management-vm]. Essa VM de gerenciamento já deve ter os cmdlets do PowerShell do AD necessários e a conexão com o domínio gerenciado.

O exemplo a seguir cria uma UO personalizada chamada *myNewOU* no domínio gerenciado chamado *aaddscontoso.com*. Use sua própria unidade organizacional e nome de domínio gerenciado:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Agora, crie um gMSA usando o cmdlet [New-ADServiceAccount][New-ADServiceAccount] . Os seguintes parâmetros de exemplo são definidos:

* **-Name** é definido como *WebFarmSvc*
* Parâmetro **-path** especifica a UO personalizada para o gMSA criado na etapa anterior.
* As entradas DNS e os nomes de entidade de serviço são definidos para *WebFarmSvc.aaddscontoso.com*
* As entidades de segurança no *AADDSCONTOSO-Server $* têm permissão para recuperar a senha e usar a identidade.

Especifique seus próprios nomes e nomes de domínio.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Agora, aplicativos e serviços podem ser configurados para usar o gMSA conforme necessário.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o gMSAs, consulte [introdução às contas de serviço gerenciado de grupo][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
