---
title: "Versão prévia do gerenciamento de unidades administrativas no Azure Active Directory"
description: "Usando unidades administrativas para delegação mais granular de permissões no Active Directory do Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8464cd6b-1d1a-470d-a4fb-ee29b8eab4c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e12a0aea8264b1ea67c26294ec5bbe9c404a171e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Gerenciamento de unidades administrativas no Azure AD – visualização pública
Este artigo descreve as unidades administrativas – um novo contêiner de recursos do Azure Active Directory que pode ser usado para delegar permissões administrativas e aplicar políticas a subconjuntos de usuários. No Active Directory do Azure, unidades administrativas permitem aos administradores centrais delegar permissões para administradores regionais ou definir a diretiva em um nível granular.

Isso é útil em organizações com divisões independentes, por exemplo, uma grande universidade que é composta de muitas escolas independentes (Faculdade de Administração, Faculdade de Engenharia e assim por diante) que são independentes umas das outras. Essas divisões têm seus próprios administradores de TI que controlam o acesso, gerenciam usuários e definem políticas especificamente para sua divisão. Os administradores centrais desejam poder conceder essas divisões permissões de administradores entre os usuários em suas divisões específicas. Mais especificamente, usando esse exemplo, um administrador central pode, por exemplo, criar uma unidade administrativa de uma faculdade específica (Faculdade de Administração) e preenchê-la com somente os usuários da Faculdade de Negócios. Um administrador central pode incluir a equipe de TI da Faculdade de Negócios em uma função com escopo definido, em outras palavras, concede à equipe de TI da Faculdade de Administração permissões administrativas do Business somente para a unidade administrativa da Faculdade de Administração.

> [!IMPORTANT]
> Você poderá atribuir funções de administrador com escopo de unidade administrativa somente se você habilitar o Azure Active Directory Premium. Para saber mais, consulte [Introdução ao AD Premium do Azure](active-directory-get-started-premium.md).
>


Do ponto de vista do administrador central, uma unidade administrativa é um objeto de diretório que pode ser criado e populado com recursos. **Nesta versão de pré-visualização, esses recursos podem ser somente os usuários.** Após criada e populada, a unidade administrativa pode ser usada como um escopo para restringir a permissão concedida somente para os recursos contidos na unidade administrativa.

## <a name="managing-administrative-units"></a>Gerenciando unidades administrativas
Nesta versão de visualização, você pode criar e gerenciar unidades administrativas usando os cmdlets do Módulo do Active Directory do Azure para Windows PowerShell. Para saber mais sobre como fazer isso, consulte [Trabalhando com unidades administrativas](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

Para obter mais informações sobre os requisitos de software e a instalação do módulo do Azure AD, além de informações sobre os cmdlets do Módulo do Azure AD para gerenciar unidades administrativas, incluindo sintaxe, descrições de parâmetros e exemplos, consulte [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>Próximas etapas
[Edições do Active Directory do Azure](active-directory-editions.md)
