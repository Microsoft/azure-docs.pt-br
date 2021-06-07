---
title: Gerenciar o acesso ao gerenciamento do Azure com Acesso Condicional no Azure AD
description: Saiba mais sobre como usar o Acesso Condicional no Azure AD para gerenciar o acesso ao gerenciamento do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83758768"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gerenciar o acesso ao gerenciamento do Azure com Acesso Condicional

> [!CAUTION]
> Verifique se você entende como o Acesso Condicional funciona antes de definir uma política para gerenciar o acesso ao gerenciamento do Azure. Verifique se você não criou nenhuma condição que possa bloquear o seu próprio acesso ao portal.

O Acesso Condicional no Azure AD (Azure Active Directory) controla o acesso a aplicativos de nuvem com base em determinadas condições que você especifica. Para permitir o acesso, você cria políticas de Acesso Condicional que permitem ou bloqueiam o acesso, dependendo de cumprirem-se ou não os requisitos na política. 

Normalmente, você usa o Acesso Condicional para controlar o acesso aos seus aplicativos de nuvem. Você também pode configurar políticas para controlar o acesso ao gerenciamento do Azure com base em determinadas condições (como o risco de entrada, localização ou dispositivo) e impor requisitos, assim como autenticação multifator.

Para criar uma política de gerenciamento do Azure, você seleciona **Microsoft Azure Management** em **aplicativos de nuvem** ao escolher o aplicativo no qual aplicar a política.

![Acesso condicional para o gerenciamento do Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A política que você cria aplica-se a todos os pontos de extremidade de gerenciamento do Azure, incluindo o seguinte:

- Portal do Azure
- Provedor do Azure Resource Manager
- APIs de Gerenciamento de Serviços (Clássico)
- Azure PowerShell
- Portal do administrador de assinaturas do Visual Studio
- Azure DevOps
- Portal do Azure Data Factory

Observe que a política se aplica ao Azure PowerShell, que chama a API do Azure Resource Manager. Não se aplica ao [PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-adv2), que chama o Microsoft Graph.

Para obter mais informações sobre como configurar uma política de exemplo para habilitar o Acesso Condicional para o gerenciamento do Microsoft Azure, confira o artigo [Acesso Condicional: exigir MFA para gerenciamento do Azure](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
