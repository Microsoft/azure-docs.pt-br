---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0e55c372c6f5dc3484bd64cf4f328479d2d0b245
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461307"
---
## <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transações principais (máximo de transações permitidas em 10 segundos, por cofre, por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>CRIAR chave|Chave HSM<br>Todas as outras transações|Chave de software<br>CRIAR chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA de 2.048 bits|5|1.000|10|2.000|
|RSA de 3.072 bits|5|250|10|500|
|RSA de 4.096 bits|5|125|10|250|
|ECC P-256|5|1.000|10|2.000|
|ECC P-384|5|1.000|10|2.000|
|ECC P-521|5|1.000|10|2.000|
|ECC SECP256K1|5|1.000|10|2.000|

> [!NOTE]
> Na tabela anterior, podemos ver que para chaves de software RSA de 2.048 bits, são permitidas transações de GET 2.000 por 10 segundos. Para chaves de HSM RSA de 2.048 bits, são permitidas transações de GET de 1.000 por 10 segundos.
>
> Os limites de limitação são ponderados e imposição está em sua soma. Por exemplo, conforme mostrado na tabela anterior, quando você executa operações GET em chaves de HSM RSA, ele é oito vezes mais caro para usar chaves de 4.096 bits comparadas às chaves de 2.048 bits. Isso ocorre porque 1.000/125 = 8.
>
> Em um determinado intervalo de 10 segundos, um cliente do Azure Key Vault pode fazer *apenas uma* das seguintes operações antes de encontrar um `429` limitação do código de status HTTP:
> - 2.000 transações de GET de chave de software RSA de 2.048 bits
> - 1.000 transações da RSA de 2.048 bits HSM-chave GET
> - Transações de 125 RSA de 4.096 bits HSM-chave GET
> - Transações de HSM-chave RSA de 4.096 bits GET 124 e 8 de RSA de 2.048 bits HSM-chave GET

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerenciado e transações de cofre:
| Tipo de transação | Máximo de transações permitidas em 10 segundos, por cofre, por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2.000 |

Para obter informações sobre como lidar com a limitação quando esses limites são excedidos, consulte [diretrizes de limitação do Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> um limite de toda a assinatura para todos os tipos de transação é cinco vezes por limite do Cofre de chaves. Por exemplo, HSM-outras transações por assinatura são limitadas a 5.000 transações em 10 segundos por assinatura.
