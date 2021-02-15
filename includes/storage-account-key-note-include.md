---
title: arquivo de inclusão
description: arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027317"
---
## <a name="protect-your-access-keys"></a>Proteger suas chaves de acesso

As chaves de acesso da conta de armazenamento são semelhantes a uma senha raiz para sua conta de armazenamento. Sempre tenha cuidado para proteger suas chaves de acesso. Use Azure Key Vault para gerenciar e girar suas chaves com segurança. Evite distribuir chaves de acesso para outros usuários, embuti-las em código ou salvá-las em qualquer lugar em texto sem formatação que seja acessível a outras pessoas. Gire suas chaves se acreditar que elas podem ter sido comprometidas.

> [!NOTE]
> A Microsoft recomenda usar o Azure Active Directory (AD do Azure) para autorizar solicitações em dados de BLOB e de fila, se possível, em vez de chave compartilhada. O Azure AD fornece segurança superior e facilidade de uso sobre chave compartilhada. Para obter mais informações sobre como autorizar o acesso a dados com o Azure AD, consulte [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
