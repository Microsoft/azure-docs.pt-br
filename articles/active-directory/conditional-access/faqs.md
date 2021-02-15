---
title: Azure Active Directory perguntas frequentes de acesso condicional | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre o acesso condicional no Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8043e85db1ee1aadc814e98db12cab10ec17e129
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145231"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Azure Active Directory FAQs de acesso condicional

## <a name="which-applications-work-with-conditional-access-policies"></a>Quais aplicativos funcionam com políticas de acesso condicional?

Para obter informações sobre aplicativos que funcionam com políticas de acesso condicional, consulte [aplicativos e navegadores que usam regras de acesso condicional no Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>As políticas de acesso condicional são impostas para colaboração B2B e usuários convidados?

As políticas são impostas para usuários de colaboração B2B (entre empresas). No entanto, em alguns casos, um usuário pode não ser capaz de atender aos requisitos da política. Por exemplo, a organização de um usuário convidado pode não oferecer suporte à autenticação multifator. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>A política do SharePoint Online também se aplica ao OneDrive for Business?

Sim. A política do SharePoint Online também se aplica ao OneDrive for Business. Para obter mais informações, consulte o artigo [dependências do serviço de acesso condicional](service-dependencies.md) e considerar as políticas de direcionamento para o [aplicativo do Office 365](concept-conditional-access-cloud-apps.md#office-365) em vez disso.

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Por que não posso definir uma política diretamente em aplicativos cliente, como o Word ou o Outlook?

Uma política de acesso condicional define os requisitos para acessar um serviço. É imposta quando ocorre a autenticação para esse serviço. A política não é definida diretamente em um aplicativo cliente. Em vez disso, ela será aplicada quando um cliente chamar um serviço. Por exemplo, um conjunto de políticas no SharePoint se aplica aos clientes que chamam o SharePoint. Um conjunto de políticas no Exchange se aplica ao Outlook. Para obter mais informações, consulte o artigo [dependências do serviço de acesso condicional](service-dependencies.md) e considerar as políticas de direcionamento para o [aplicativo do Office 365](concept-conditional-access-cloud-apps.md#office-365) em vez disso.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Uma política de acesso condicional se aplica a contas de serviço?

As políticas de acesso condicional se aplicam a todas as contas de usuário. Isso inclui as contas de usuário usadas como contas de serviço. Geralmente, uma conta de serviço que é executada de forma autônoma não pode atender aos requisitos de uma política de acesso condicional. Por exemplo, autenticação multifator pode ser necessária. As contas de serviço podem ser excluídas de uma política usando uma [exclusão de usuário ou grupo](concept-conditional-access-users-groups.md#exclude-users). 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Qual é a política de exclusão padrão para plataformas de dispositivos sem suporte?

Atualmente, as políticas de acesso condicional são impostas seletivamente em usuários de dispositivos Android e iOS. Os aplicativos em outras plataformas de dispositivo são, por padrão, não afetados pela política de acesso condicional para dispositivos iOS e Android. A administração de locatário poderá substituir a política global para não permitir o acesso a usuários em plataformas sem suporte.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Como as políticas de acesso condicional funcionam para o Microsoft Teams?

O Microsoft Teams depende muito do Exchange Online e do SharePoint Online para cenários de produtividade de núcleo, como reuniões, calendários e compartilhamento de arquivos. As políticas de acesso condicional definidas para esses aplicativos de nuvem se aplicam ao Microsoft Teams quando um usuário assina diretamente no Microsoft Teams.

O Microsoft Teams também tem suporte separadamente como um aplicativo de nuvem em políticas de acesso condicional. As políticas de acesso condicional definidas para um aplicativo de nuvem se aplicam ao Microsoft Teams quando um usuário faz logon. No entanto, sem as políticas corretas em outros aplicativos, como o Exchange Online e o SharePoint Online, os usuários ainda poderão acessar esses recursos diretamente.

Os clientes de área de trabalho do Microsoft Teams para Windows e Mac oferecem suporte a autenticação moderna. A autenticação moderna traz a entrada com base no Azure Active Directory Authentication Library (ADAL) para aplicativos cliente do Microsoft Office entre plataformas.

Para obter mais informações, consulte o artigo [dependências do serviço de acesso condicional](service-dependencies.md) e considerar as políticas de direcionamento para o [aplicativo do Office 365](concept-conditional-access-cloud-apps.md#office-365) em vez disso.

## <a name="why-are-some-tabs-not-working-in-microsoft-teams-after-enabling-conditional-access-policies"></a>Por que algumas guias não estão funcionando no Microsoft Teams depois de habilitar as políticas de acesso condicional?

Depois de habilitar algumas políticas de acesso condicional no locatário do Microsoft Teams, algumas guias podem não funcionar mais no cliente de desktop conforme o esperado. No entanto, as guias afetadas funcionam ao usar o cliente Web do Microsoft Teams. As guias afetadas podem incluir Power BI, formulários, VSTS, PowerApps e lista do SharePoint.

Para ver as guias afetadas, você deve usar o cliente Web das equipes no Edge, no Internet Explorer ou no Chrome com a extensão de contas do Windows 10 instalada. Algumas guias dependem da autenticação na Web, que não funciona no cliente do Microsoft Teams desktop quando o acesso condicional está habilitado. A Microsoft está trabalhando com parceiros para habilitar esses cenários. Até o momento, habilitamos cenários envolvendo o Planner, o OneNote e o Stream.

## <a name="next-steps"></a>Próximas etapas

- Para configurar políticas de acesso condicional para seu ambiente, consulte o artigo [planejar uma implantação de acesso condicional](plan-conditional-access.md). 
