---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995601"
---
Se desejar que os usuários sejam solicitados a fornecer um segundo fator de autenticação antes de conceder acesso, você poderá configurar a MFA (autenticação multifator) do Azure AD. Você pode configurar a MFA por usuário ou pode aproveitar a MFA por meio do [acesso condicional](../articles/active-directory/conditional-access/overview.md).

* A MFA por usuário pode ser habilitada sem custo adicional. Ao habilitar a MFA por usuário, o usuário será solicitado a fornecer autenticação de dois fatores em relação a todos os aplicativos vinculados ao locatário do Azure AD. Consulte a [opção 1](#peruser) para obter as etapas.
* O acesso condicional permite um controle mais refinado sobre como um segundo fator deve ser promovido. Ele pode permitir a atribuição de MFA somente a VPN e excluir outros aplicativos vinculados ao locatário do Azure AD. Consulte a [opção 2](#conditional) para obter as etapas.