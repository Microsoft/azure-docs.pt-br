---
title: Perguntas frequentes sobre o acesso condicional ao Azure Active Directory | Microsoft Docs
description: Obtenha respostas para as perguntas frequentes sobre o acesso condicional no Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5bc6f90100e5c09eac2b6e5d0e114d4445daa7c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Perguntas frequentes sobre o acesso condicional ao Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais aplicativos funcionam com políticas de acesso condicional?

Para obter informações sobre aplicativos que funcionam com políticas de acesso condicional, consulte [Aplicativos e navegadores que usam regras de acesso condicional no Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>As políticas de acesso condicional são impostas para colaboração B2B e usuários convidados?

As políticas são impostas para usuários de colaboração B2B (entre empresas). No entanto, em alguns casos, um usuário pode não ser capaz de atender aos requisitos da política. Por exemplo, a organização de um usuário convidado pode não oferecer suporte à autenticação multifator. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A política do SharePoint Online também se aplica ao OneDrive for Business?

Sim. A política do SharePoint Online também se aplica ao OneDrive for Business.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Por que não é possível definir uma política em aplicativos cliente, como o Word ou o Outlook?

Uma política de acesso condicional define os requisitos para acessar um serviço. É imposta quando ocorre a autenticação para esse serviço. A política não é definida diretamente em um aplicativo cliente. Em vez disso, ela será aplicada quando um cliente chamar um serviço. Por exemplo, um conjunto de políticas no SharePoint se aplica aos clientes que chamam o SharePoint. Um conjunto de políticas no Exchange se aplica ao Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional se aplica a contas de serviço?

As políticas de acesso condicional se aplicam a todas as contas de usuário. Isso inclui as contas de usuário usadas como contas de serviço. Geralmente, uma conta de serviço executada de forma autônoma não pode satisfazer os requisitos da política de acesso condicional. Por exemplo, autenticação multifator pode ser necessária. As contas de serviços podem ser excluídas de uma política ao usar configurações de gerenciamento de política de acesso condicional. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>As APIs do Graph estão disponíveis para configurar as políticas de acesso condicional?

No momento, não. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Qual é a política de exclusão padrão para plataformas de dispositivos sem suporte?

No momento, as políticas de acesso condicional são impostas seletivamente aos usuários de dispositivos com Android e iOS. Os aplicativos em outras plataformas de dispositivo não são, por padrão, afetados pela política de acesso condicional para dispositivos iOS e Android. A administração de locatário poderá substituir a política global para não permitir o acesso a usuários em plataformas sem suporte.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como as políticas de acesso condicional funcionam para o Microsoft Teams?

O Microsoft Teams depende muito do Exchange Online e do SharePoint Online para cenários de produtividade de núcleo, como reuniões, calendários e compartilhamento de arquivos. As políticas de acesso condicional definidas para esses aplicativos de nuvem se aplicam ao Microsoft Teams quando um usuário se conecta.

O Microsoft Teams também tem suporte separadamente como um aplicativo de nuvem em políticas de acesso condicional do Azure Active Directory. As políticas de acesso condicional definidas para um aplicativo de nuvem se aplicam ao Microsoft Teams quando um usuário entra.

Os clientes de área de trabalho do Microsoft Teams para Windows e Mac oferecem suporte a autenticação moderna. A autenticação moderna traz a entrada com base no Azure Active Directory Authentication Library (ADAL) para aplicativos cliente do Microsoft Office entre plataformas. 