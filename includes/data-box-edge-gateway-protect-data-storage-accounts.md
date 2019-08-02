---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172566"
---
Seu dispositivo está associado uma conta de armazenamento que é usada como um destino para seus dados no Azure. O acesso à conta de armazenamento é controlado pela assinatura e o armazenamento de 512 bits duas chaves associadas à conta de armazenamento de acesso.

Uma das chaves é usada para autenticação quando o dispositivo de borda da caixa de dados acessa a conta de armazenamento. A outra chave é mantida em reserva, portanto, você pode girar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Protege com cuidado sua chave de conta. Não distribua a senha para outros usuários, duro codificá-lo ou salvá-lo em qualquer lugar em texto sem formatação que é acessível a outras pessoas.
- [Regenerar a chave de conta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) por meio do Azure portal se você acha que poderia ser comprometido.
- Periodicamente, o administrador do Azure deve alterar ou regenerar a chave primária ou secundária, usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.