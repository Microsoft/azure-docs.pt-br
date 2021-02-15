---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82562110"
---
O dispositivo está associado a uma conta de armazenamento usada como destino dos dados no Azure. O acesso a uma conta de armazenamento é controlado pela assinatura e pelas duas chaves de acesso de armazenamento de 512 bits associadas àquela conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo do Azure Stack Edge acessa a conta de armazenamento. A outra chave fica de reserva para que você possa alternar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Proteja a chave de conta com cuidado. Não distribua a senha para outros usuários, nem codifique ou salve em qualquer lugar em texto sem formatação acessível a outras pessoas.
- Gere novamente sua chave de conta por meio do portal do Azure se você considerar que ela pode estar comprometida. Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).
- O administrador do Azure deve alterar periodicamente ou regenerar a chave primária ou secundária usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.