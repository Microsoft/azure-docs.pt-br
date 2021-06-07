---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581535"
---
O dispositivo está associado a uma conta de armazenamento usada como destino dos dados no Azure. O acesso a uma conta de armazenamento é controlado pela assinatura e pelas duas chaves de acesso de armazenamento de 512 bits associadas àquela conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo do Azure Stack Edge acessa a conta de armazenamento. A outra chave fica de reserva para que você possa alternar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Proteja a chave de conta com cuidado. Não distribua a senha para outros usuários, codifique-a ou salve-a em qualquer lugar em texto sem formatação que seja acessível a outras pessoas.
- Gere novamente sua chave de conta por meio do portal do Azure se você considerar que ela pode estar comprometida. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).
- O administrador do Azure deve alterar periodicamente ou regenerar a chave primária ou secundária usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.