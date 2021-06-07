---
title: Inscrição por autoatendimento para usuários verificados por email – Azure AD | Microsoft Docs
description: Usar a inscrição por autoatendimento em uma organização do Azure AD (Azure Active Directory)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95625886ed11256a40e5993540d7e545134d6dd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860857"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>O que é inscrição por autoatendimento no Azure Active Directory?

Este artigo explica como usar a inscrição por autoatendimento para preencher uma organização no Azure AD (Azure Active Directory). Se você deseja assumir um nome de domínio de uma organização do Azure AD não gerenciada, confira [Assumir um diretório não gerenciado como administrador](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Por que usar a inscrição por autoatendimento?

* Leve aos clientes os serviços que eles desejam mais rápido
* Crie ofertas baseadas em email para um serviço
* Crie fluxos de inscrição baseados em email que permitem rapidamente que os usuários criem identidades usando seus aliases de email de trabalho fáceis de lembrar
* Um autoatendimento criado no diretório do Azure AD pode ser convertido em um diretório gerenciado que pode ser usado para outros serviços

## <a name="terms-and-definitions"></a>Termos e definições

* **Inscrição por autoatendimento**: Esse é o método pelo qual um usuário se inscreve em um serviço de nuvem e tem uma identidade criada automaticamente para ele no Azure AD, com base em seu domínio de email.
* **Diretório não gerenciado do Azure AD**: Esse é o diretório no qual essa identidade é criada. Um diretório não gerenciado é um diretório sem nenhum administrador global.
* **Usuário verificado por email**: Esse é um tipo de conta de usuário do Azure AD. Um usuário que tem uma identidade criada automaticamente após a inscrição para uma oferta de autoatendimento é conhecido como um usuário verificado por email. Um usuário verificadas por email é um membro comum de um diretório marcado com creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Como controlar as configurações de autoatendimento?

Os administradores têm dois controles de autoatendimento atualmente. Eles podem controlar se:

* Os usuários podem ingressar no diretório por email
* Os usuários podem licenciar eles próprios para aplicativos e serviços

### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar esses recursos?

Um administrador pode configurar esses recursos usando os parâmetros Set-MsolCompanySettings do cmdlet do Azure AD a seguir:

* **AllowEmailVerifiedUsers** controla se um usuário pode criar ou ingressar em um diretório não gerenciado. Se você definir esse parâmetro como $false, nenhum usuário verificado por email poderá ingressar no diretório.
* **AllowAdHocSubscriptions** controla a capacidade dos usuários de executarem a inscrição por autoatendimento. Se você definir esse parâmetro como $false, nenhum usuário poderá realizar a inscrição por autoatendimento.
  
AllowEmailVerifiedUsers e AllowAdHocSubscriptions são as configurações de todo o diretório que podem ser aplicadas a um gerenciado ou um diretório não gerenciado. Aqui está um exemplo onde:

* Você administra um diretório com um domínio verificado como contoso.com
* Você usa a colaboração B2B de um diretório diferente para convidar um usuário que ainda não existe (userdoesnotexist@contoso.com) no diretório base do contoso.com
* O diretório base tem o AllowEmailVerifiedUsers ativado

Se as condições anteriores forem verdadeiras, em seguida, um usuário de membro é criado no diretório inicial e um usuário de convidado B2B é criado no diretório que convida.

Para obter mais informações sobre as inscrições na avaliação gratuita do Flow e do PowerApps, consulte os seguintes artigos:

* [Como posso impedir meus usuários existentes de começarem a usar o Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Perguntas e respostas sobre o Flow na sua organização](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Como os controles funcionam juntos?
Esses dois parâmetros podem ser usados em conjunto para definir um controle mais preciso sobre a inscrição por autoatendimento. Por exemplo, o seguinte comando permitirá que os usuários realizem a inscrição por autoatendimento, mas apenas se os usuários já tiverem uma conta no Azure AD (em outras palavras, os usuários que precisassem criar uma conta verificada por email primeiro não poderiam realizar a inscrição por autoatendimento):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

O fluxograma a seguir explica as diferentes combinações desses parâmetros e as condições resultantes para o diretório e para a inscrição por autoatendimento.

![fluxograma de controles de inscrição por autoatendimento](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Os detalhes dessa configuração podem ser recuperados pelo cmdlet Get-MsolCompanyInformation do PowerShell. Para obter mais informações sobre isso, confira [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation).

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

Para obter mais informações e exemplos de como usar esses parâmetros, consulte [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

## <a name="next-steps"></a>Próximas etapas

* [Adicionar um nome de domínio personalizado ao Azure AD](../fundamentals/add-custom-domain.md)
* [Como instalar e configurar o PowerShell do Azure](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [Fechar sua conta corporativa ou de estudante em um diretório não gerenciado](users-close-account.md)
