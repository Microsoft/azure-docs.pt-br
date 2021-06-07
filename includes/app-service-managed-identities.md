---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649092"
---
Uma identidade gerenciada do Azure Active Directory permite que o aplicativo acesse facilmente os outros recursos protegidos pelo Azure Active Directory, como o Azure Key Vault. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou giro de nenhum segredo. Para saber mais sobre identidades gerenciadas no Azure Active Directory, veja [Identidades gerenciadas para recursos do Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades:

- Uma **identidade atribuída pelo sistema** é vinculada ao seu aplicativo e é excluída se o seu aplicativo for excluído. Um aplicativo só pode ter uma identidade atribuída pelo sistema.
- Uma **identidade atribuída pelo usuário** é um recurso independente do Azure que pode ser atribuído ao seu aplicativo. Um aplicativo pode ter várias identidades atribuídas pelo usuário.