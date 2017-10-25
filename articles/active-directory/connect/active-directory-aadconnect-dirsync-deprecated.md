---
title: "Atualização do DirSync e do Azure AD Sync | Microsoft Docs"
description: Descreve como atualizar do DirSync e do Azure AD Sync para o Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: bd68fb88-110b-4d76-978a-233e15590803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3674670e10500d2992539ab60fbdb31b666fcf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-windows-azure-active-directory-sync-and-azure-active-directory-sync"></a>Atualizar o Windows Azure Active Directory Sync e o Azure Active Directory Sync
O Azure AD Connect é a melhor maneira de conectar seu diretório local ao Azure AD e ao Office 365. Esse é um ótimo momento para atualizar para o Azure AD Connect do Windows Azure Active Directory Sync (DirSync) ou do Azure AD Sync, pois essas ferramentas foram preteridas e chegarão ao fim do suporte em 13 de abril de 2017.

As duas ferramentas de sincronização de identidade que foram preteridas eram oferecidas para clientes de floresta única (DirSync) e para clientes de várias florestas e outros clientes avançados (Azure AD Sync). Essas ferramentas mais antigas foram substituídas por uma solução única que está disponível para todos os cenários: o Azure AD Connect. Ele oferece nova funcionalidade, aprimoramentos de recursos e suporte a novos cenários. Para poder continuar a sincronizar os dados de identidade locais com o Azure AD e o Office 365, é altamente recomendável que você atualize para o Azure AD Connect.

A última versão do DirSync foi lançada em julho de 2014 e a última versão do Azure AD Sync foi lançada em maio de 2015.

## <a name="what-is-azure-ad-connect"></a>O que é o Azure AD Connect
O Azure AD Connect é o sucessor do DirSync e do Azure AD Sync. Ele combina todos os cenários aos quais essas duas ferramentas prestavam suporte. Você pode ler mais a respeito em [Como integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md).

## <a name="deprecation-schedule"></a>Agenda de preterição
| Data | Comentário |
| --- | --- |
| 13 de abril de 2016 |O Windows Azure Active Directory Sync ("DirSync") e o Microsoft Azure Active Directory Sync ("Azure AD Sync") são anunciados como preteridos. |
| 13 de abril de 2017 |O suporte termina. Os clientes não poderão mais abrir um caso de suporte sem atualizar primeiro para o Azure AD Connect. |
|31 de dezembro de 2017|O Azure AD deixará de aceitar comunicações da Sincronização do Microsoft Azure Active Directory (“DirSync”) e da Sincronização do Microsoft Azure Active Directory (“Azure AD Sync”).

## <a name="how-to-transition-to-azure-ad-connect"></a>Como fazer a transição para o Azure AD Connect
Se você está executando o DirSync, há duas maneiras de atualizar: atualização in-loco e implantação paralela. Uma atualização in-loco é recomendada para a maioria dos clientes e se você tem um sistema operacional recente e com menos de 50.000 objetos. Em outros casos, é recomendável fazer uma implantação paralela, em que a configuração do DirSync é movida para um novo servidor que executa o Azure AD Connect.

Se você usa o Azure AD Sync, recomenda-se uma atualização in-loco. Se desejar, você poderá instalar um novo servidor do Azure AD Connect em paralelo e fazer uma migração swing de seu servidor Azure AD Sync para o Azure AD Connect.

| Solução | Cenário |
| --- | --- |
| [Atualização do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Se você tiver um servidor DirSync existente já em execução.</li> |
| [Atualização do Azure AD Sync](active-directory-aadconnect-upgrade-previous-version.md) |<li>Se você estiver migrando do Azure AD Sync.</li> |

Para ver como fazer uma atualização in-loco do DirSync para o Azure AD Connect, confira este vídeo do Channel 9:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-in-place-upgrade-from-legacy-tools/player]
>
>

## <a name="faq"></a>Perguntas frequentes
**P: Eu recebi uma notificação por email da equipe do Azure e/ou uma mensagem do centro de mensagens do Office 365, mas estou usando o Connect.**  
A notificação também foi enviada para clientes que estão usando o Azure AD Connect com número de build 1.0.\*.0 (usando uma versão anterior à 1.1). A Microsoft recomenda que os clientes fiquem atualizados com as versões do Azure AD Connect. O recurso de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) introduzido na versão 1.1 facilita ter sempre uma versão recente do Azure AD Connect instalada.

**P: O DirSync/Azure AD Sync deixarão de funcionar em 13 de abril de 2017?**  
O DirSync e o Azure AD Sync continuarão funcionando em 13 de abril de 2017.  No entanto, o Azure AD deixará de aceitar comunicações do DirSync e do Azure AD Sync em 31 de dezembro de 2017.

**P: A partir de quais versões do DirSync eu posso atualizar?**  
Há suporte para atualizar a partir de qualquer versão do DirSync que está sendo usada atualmente.

**P: E o Conector do Azure AD para FIM/MIM?**  
O Conector do Azure AD para FIM/MIM **não** foi anunciado como preterido. Ele se encontra no **estado de congelamento de recursos**. Nenhuma funcionalidade nova foi adicionada e ele não recebe correções de bug. A Microsoft recomenda que os clientes que o utilizam planejem a migração para o Azure AD Connect. É altamente recomendável não iniciar novas implantações usando este recurso. Esse Conector será anunciado como preterido no futuro.

## <a name="additional-resources"></a>Recursos adicionais
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)
