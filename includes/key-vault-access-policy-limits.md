---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934520"
---
O cofre de chaves dá suporte a até 1024 entradas de política de acesso, com cada entrada concedendo um conjunto distinto de permissões a uma determinada entidade de segurança. Devido a essa limitação, é recomendável atribuir políticas de acesso a grupos de usuários, quando possível, em vez de usuários individuais. O uso de grupos facilita muito o gerenciamento de permissões para várias pessoas em sua organização. Para obter mais informações, consulte [gerenciar o acesso a aplicativos e recursos usando grupos de Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Para obter detalhes completos sobre o controle de acesso do Key Vault, confira [Segurança do Azure Key Vault: Gerenciamento de identidades e acesso](../articles/key-vault/general/security-overview.md#identity-management).