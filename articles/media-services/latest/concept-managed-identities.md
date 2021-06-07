---
title: Identidades gerenciadas
description: Os serviços de mídia podem ser usados com identidades gerenciadas do Azure.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258432"
---
# <a name="managed-identities"></a>Identidades gerenciadas

Um desafio comum para desenvolvedores é o gerenciamento de segredos e credenciais para proteger a comunicação entre diferentes serviços. No Azure, as identidades gerenciadas eliminam a necessidade de os desenvolvedores precisarem gerenciar credenciais fornecendo uma identidade para o recurso do Azure no Azure AD e a usando para obter tokens do Azure AD (Azure Active Directory).

Atualmente, há dois cenários em que identidades gerenciadas podem ser usadas com os serviços de mídia:

- Use a identidade gerenciada da conta dos serviços de mídia para acessar as contas de armazenamento.

- Use a identidade gerenciada da conta dos serviços de mídia para acessar Key Vault acessar as chaves do cliente.

As próximas duas seções descrevem as etapas dos dois cenários.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Usar a identidade gerenciada da conta dos serviços de mídia para acessar contas de armazenamento

1. Crie uma conta dos serviços de mídia com uma identidade gerenciada.
1. Conceda o acesso de entidade de identidade gerenciada a uma conta de armazenamento que você possui.
1. Os serviços de mídia podem, então, acessar a conta de armazenamento em seu nome usando a identidade gerenciada.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Use a identidade gerenciada da conta dos serviços de mídia para acessar Key Vault acessar as chaves do cliente

1. Crie uma conta dos serviços de mídia com uma identidade gerenciada.
1. Conceda acesso de entidade de identidade gerenciada a um Key Vault que você possui.
1. Configure a conta dos serviços de mídia para usar a criptografia de conta baseada na chave do cliente.
1. Os serviços de mídia acessam Key Vault em seu nome usando a identidade gerenciada.

Para obter mais informações sobre chaves gerenciadas pelo cliente e Key Vault, consulte [traga sua própria chave (chaves gerenciadas pelo cliente) com os serviços de mídia](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Tutoriais

Esses tutoriais incluem os dois cenários mencionados acima.

- [Usar o portal do Azure para usar as chaves gerenciadas pelo cliente ou o BYOK com os Serviços de Mídia](tutorial-byok-portal.md)
- [Use chaves gerenciadas pelo cliente ou BYOK com a API REST dos serviços de mídia](tutorial-byok-postman.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o que as identidades gerenciadas podem fazer por você e seus aplicativos do Azure, confira [identidades gerenciadas do Azure ad](../../active-directory/managed-identities-azure-resources/overview.md).
