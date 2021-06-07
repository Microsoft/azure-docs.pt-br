---
title: incluir arquivo
description: incluir arquivo
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92379753"
---
- Cada identidade gerenciada é contabilizada com relação ao limite de cota de objeto em um locatário do Azure AD, conforme descrito em [Limites e restrições de serviço do Azure AD](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   A taxa em que as identidades gerenciadas podem ser criadas tem os seguintes limites:

    1. Por Locatário do Azure AD por região do Azure: 200 operações de criação por 20 segundos.
    2. Por Assinatura do Azure por região do Azure: 40 operações de criação por 20 segundos.

- Quando você cria identidades gerenciadas atribuídas pelo usuário, somente caracteres alfanuméricos (0-9, a-z, A-Z) e o hífen (-) têm suporte. Para que a atribuição a uma máquina virtual ou um conjunto de dimensionamento de máquinas virtuais funcione corretamente, o nome é limitado a 24 caracteres.
