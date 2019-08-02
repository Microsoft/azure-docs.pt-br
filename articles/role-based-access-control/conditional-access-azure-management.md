---
title: Gerenciar o acesso ao gerenciamento do Azure com acesso condicional no Azure Active Directory
description: Saiba mais sobre como usar o acesso condicional no Azure AD para gerenciar o acesso ao gerenciamento do Azure.
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
ms.openlocfilehash: 3aa24fdfc156c0197d724fd57729f5b11fa908ee
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278155"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gerenciar o acesso ao gerenciamento do Azure com acesso condicional

O acesso condicional no Azure Active Directory (AD do Azure) controla o acesso a aplicativos de nuvem com base em condições específicas que você especificar. Para permitir o acesso, você cria políticas de acesso condicional que permitem ou bloqueiam o acesso com base em se os requisitos na política são atendidos ou não. 

Normalmente, você usa o acesso condicional para controlar o acesso aos seus aplicativos de nuvem. Você também pode configurar políticas para controlar o acesso ao gerenciamento do Azure com base em determinadas condições (como o risco de entrada, localização ou dispositivo) e impor requisitos, assim como autenticação multifator.

Para criar uma política de gerenciamento do Azure, você seleciona **Microsoft Azure Management** em **aplicativos de nuvem** ao escolher o aplicativo no qual aplicar a política.

![Acesso condicional para o gerenciamento do Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A política que você cria aplica-se a todos os pontos de extremidade de gerenciamento do Azure, incluindo o seguinte:

- Portal do Azure
- Provedor de Azure Resource Manager
- APIs de gerenciamento de serviço clássico
- Azure PowerShell
- Portal do administrador de assinaturas do Visual Studio
- Azure DevOps

Observe que a política se aplica ao Azure PowerShell, que chama a API do Azure Resource Manager. Não se aplica ao [PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-adv2), que chama o Microsoft Graph.

> [!CAUTION]
> Certifique-se de que você entendeu como o acesso condicional funciona antes de configurar uma política para gerenciar o acesso ao gerenciamento do Azure. Verifique se você não criou nenhuma condição que possa bloquear o seu próprio acesso ao portal.

Para obter mais informações sobre como configurar e usar o acesso condicional, consulte [acesso condicional no Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).