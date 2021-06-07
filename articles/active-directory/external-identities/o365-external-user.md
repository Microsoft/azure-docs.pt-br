---
title: Microsoft 365 compartilhamento externo e colaboração B2B-Azure AD
description: Discute o compartilhamento de recursos com parceiros externos usando Microsoft 365 e Azure Active Directory colaboração B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: d003008ea5b0d2591574f6f488b0145ee6f08a5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008121"
---
# <a name="microsoft-365-external-sharing-and-azure-active-directory-azure-ad-b2b-collaboration"></a>Microsoft 365 compartilhamento externo e colaboração B2B do Azure Active Directory (Azure AD)

Na colaboração B2B do Azure AD e Microsoft 365 compartilhamento externo (OneDrive, SharePoint Online, grupos unificados etc.), os usuários externos são autenticados usando o Azure AD B2B.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Como o Azure AD B2B difere do compartilhamento externo no SharePoint Online?

O OneDrive/SharePoint Online tem um gerenciador de convite separado. Suporte para compartilhamento externo no OneDrive/SharePoint Online iniciado antes de o Azure AD ter desenvolvido o suporte. Ao longo do tempo, o compartilhamento externo do OneDrive/SharePoint Online acumulou vários recursos e muitos milhões de usuários que usam o padrão de compartilhamento interno do produto. No entanto, existem algumas diferenças sutis entre como funciona o compartilhamento externo do OneDrive / SharePoint Online e como funciona a colaboração B2B do Azure AD. Você pode aprender mais sobre o compartilhamento externo do OneDrive / SharePoint Online em [Visão geral do compartilhamento externo](/sharepoint/external-sharing-overview). O processo geralmente é diferente do Azure AD B2B das seguintes maneiras:

- O OneDrive/SharePoint Online adiciona usuários ao diretório depois que os usuários tiverem resgatado seus convites. Portanto, antes de resgate, você não vê o usuário no Portal do Azure AD. Se outro site convidar um usuário durante esse período, um novo convite será gerado. No entanto, quando você usa a colaboração B2B do Azure AD, os usuários são adicionados imediatamente no convite para que eles apareçam em todos os lugares.

- A experiência de resgate no OneDrive/SharePoint Online tem uma aparência diferente da experiência na colaboração B2B do Azure AD. Depois que um usuário resgata um convite, as experiências são parecidas.

- A colaboração B2B do Azure AD convidou usuários que possam ser escolhidos das caixas de diálogo de compartilhamento do OneDrive/SharePoint Online. Os usuários convidados do OneDrive/SharePoint Online também aparecem no Azure AD após o resgate do convite.

- Os requisitos de licenciamento são diferentes. Para saber mais sobre licenciamento, confira [Licenciamento de identidades externas do Azure ad](./external-identities-pricing.md) e [visão geral do compartilhamento externo do SharePoint Online](/sharepoint/external-sharing-overview).
Para gerenciar o compartilhamento externo no OneDrive / SharePoint Online com a colaboração do Azure AD B2B, defina a configuração de compartilhamento externo do OneDrive / SharePoint Online como **Permitir compartilhamento apenas com os usuários externos que já existem no diretório da sua organização**. Os usuários podem acessar sites compartilhados externamente e escolher colaboradores externos adicionados pelo administrador. O administrador pode adicionar os colaboradores externos por meio de APIs de convite de colaboração B2B.


![A configuração de compartilhamento externo do OneDrive/SharePoint](media/o365-external-user/odsp-sharing-setting.png)

Após habilitar o compartilhamento externo, a capacidade de pesquisar usuários convidados existentes no seletor de pessoas do SPO (SharePoint Online) é DESATIVADA por padrão para corresponder ao comportamento herdado.

Esse recurso pode ser habilitado usando a configuração 'ShowPeoplePickerSuggestionsForGuestUsers' na coleta do site e do locatário. Defina esse recurso usando os cmdlets Set-SPOTenant e SPOSite, que permitem aos membros pesquisar todos os usuários convidados existentes no diretório. Alterações no escopo de locatário não afetam sites SPO já provisionados.

## <a name="next-steps"></a>Próximas etapas

* [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
* [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)
* [Delegação de convites de colaboração B2B](delegate-invitations.md)
* [Grupos dinâmicos e colaboração B2B](use-dynamic-groups.md)
* [Solução de problemas Azure Active Directory colaboração B2B](troubleshoot.md)
