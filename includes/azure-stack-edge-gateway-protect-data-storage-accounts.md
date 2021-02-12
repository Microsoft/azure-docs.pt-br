---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900747"
---
O dispositivo está associado a uma conta de armazenamento usada como destino dos dados no Azure. O acesso a uma conta de armazenamento é controlado pela assinatura e pelas duas chaves de acesso de armazenamento de 512 bits associadas àquela conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo Data Box Edge acessa a conta de armazenamento. A outra chave fica de reserva para que você possa alternar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos seguir estas práticas recomendadas para a rotação de chaves:

- Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Proteja a chave de conta com cuidado. Não distribua a senha para outros usuários, nem codifique ou salve em qualquer lugar em texto sem formatação acessível a outras pessoas.
- [Gere novamente sua chave de conta](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) por meio do portal do Azure se você considerar que ela pode estar comprometida.
- O administrador do Azure deve alterar periodicamente ou regenerar a chave primária ou secundária usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.
