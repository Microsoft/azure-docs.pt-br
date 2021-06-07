---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: c2548b1669366564809ed2fde725cb3399922a29
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803284"
---
O serviço do Azure Key Vault é compatível com dois tipos de recursos: cofres e HSMs gerenciados. As duas seções a seguir descrevem os limites de serviço de cada um deles, respectivamente.

### <a name="resource-type-vault"></a>Tipo de recurso: cofre

Esta seção descreve os limites de serviço para o tipo de recurso `vaults`.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Principais transações (máximo de transações permitidas a cada 10 segundos por cofre e por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>Chave CREATE|Chave HSM<br>Todas as outras transações|Chave de software<br>Chave CREATE|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA de 2.048 bits|5|1,000|10|2\.000|
|RSA de 3.072 bits|5|250|10|500|
|RSA de 4.096 bits|5|125|10|250|
|ECC P-256|5|1,000|10|2\.000|
|ECC P-384|5|1,000|10|2\.000|
|ECC P-521|5|1,000|10|2\.000|
|ECC SECP256K1|5|1,000|10|2\.000|
||||||

> [!NOTE]
> Na tabela anterior, vimos que, para chaves de software do tipo RSA de 2.048 bits, são permitidas 2 mil transações GET a cada 10 segundos. Para chaves HSM do tipo RSA de 2.048 bits, são permitidas 1 mil transações GET a cada 10 segundos.
>
> Os limites da limitação acima são ponderados, e a imposição está na soma. Por exemplo, conforme mostrado na tabela anterior, quando você executa operações GET em chaves HSM do tipo RSA, o uso de chaves de 4.096 bits é oito vezes mais caro em comparação com as chaves de 2.048 bits. Isso ocorre porque 1.000/125 = 8.
>
> Em um determinado intervalo de 10 segundos, um cliente do Azure Key Vault poderá fazer *somente uma* das seguintes operações antes de encontrar um código de status HTTP de limitação de `429`:
> - 2 mil transações GET de chave de software do tipo RSA de 2.048 bits
> - 1 mil transações GET de chave HSM do tipo RSA de 2.048 bits
> - 125 transações GET de chave HSM do tipo RSA de 4.096 bits
> - 124 transações GET de chave HSM do tipo RSA de 4.096 bits e 8 transações GET de chave HSM do tipo RSA de 2.048 bits

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerenciado e transações de cofre:

| Tipo de transação | Máximo de transações permitidas a cada 10 segundos por cofre e por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2\.000 |

Para obter informações sobre como lidar com a limitação quando esses limites forem excedidos, confira [Diretrizes de limitação do Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> O limite de assinaturas para todos os tipos de transações será cinco vezes o limite do cofre de chaves. Por exemplo, outras transações HSM por assinatura são limitadas a 5.000 transações a cada 10 segundos por assinatura.

#### <a name="backup-keys-secrets-certificates"></a>Chaves de backup, segredos, certificados

Quando você fizer backup de um objeto do cofre de chaves, como um segredo, uma chave ou um certificado, a operação de backup baixará o objeto como um blob criptografado. Esse blob não poderá ser descriptografado fora do Azure. Para obter dados utilizáveis desse blob, restaure o blob em um cofre de chaves na mesma assinatura e geografia do Azure

| Tipo de transação | Máximo permitido de versões de objeto do cofre de chaves |
| --- | --- |
| Fazer backup de chave, segredo e certificado individual |500 |

> [!NOTE]
> A tentativa de fazer backup de um objeto de chave, segredo ou certificado com mais versões do que o limite acima resultará em um erro. Não é possível excluir versões anteriores de uma chave, um segredo ou um certificado. 

#### <a name="azure-private-link-integration"></a>Integração de Link Privado do Azure

> [!NOTE]
> O número de cofres de chaves com pontos de extremidade privados habilitados por assinatura é um limite ajustável. Esse limite mostrado abaixo é o limite padrão. Se você quiser solicitar um aumento de limite para o serviço, envie um email para akv-privatelink@microsoft.com. Aprovaremos essas solicitações dependendo do caso.

| Recurso | Limite |
| -------- | -----:|
| Pontos de extremidade privados por cofre de chaves | 64 |
| Cofres de chaves com pontos de extremidade privados por assinatura | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Tipo de recurso: HSM gerenciado (versão prévia)

Esta seção descreve os limites de serviço para o tipo de recurso `managed HSM`.

#### <a name="object-limits"></a>Limites do objeto

|Item|limites|
|----|------:|
Número de instâncias de HSM por assinatura por região|1 (durante a versão prévia)
Número de chaves por pool de HSM|5.000
Número de versões por chave|100
Número de definições de função personalizadas por HSM|50
Número de atribuições de função no escopo do HSM|50
Número de atribuições de função em cada escopo de chave|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limites de transação para operações administrativas (número de operações por segundo por instância de HSM)
|Operação |Número de operações por segundo|
|----|------:|
Todas as operações de RBAC<br/>(inclui todas as operações CRUD para definições de função e atribuições de função)|5
Backup/restauração completo do HSM<br/>(apenas uma operação de backup ou restauração simultânea por instância de HSM com suporte)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limites de transação para operações criptográficas (número de operações por segundo por instância de HSM)

- Cada instância de HSM gerenciado constitui três partições de HSM com balanceamento de carga. Os limites de taxa de transferência são uma função de capacidade de hardware subjacente alocada para cada partição. As tabelas a seguir mostram a taxa de transferência máxima com pelo menos uma partição disponível. A taxa de transferência real pode ser até três vezes maior se todas as três partições estiverem disponíveis.
- Os limites de taxa de transferência indicados presumem que uma única chave está sendo usada para alcançar a taxa de transferência máxima. Por exemplo, se uma única chave RSA-2048 for usada, a taxa de transferência máxima será de 1.100 operações de sinal. Se você usar 1.100 chaves diferentes com uma transação por segundo, elas não poderão alcançar a mesma taxa de transferência.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operações de chave RSA (número de operações por segundo por instância de HSM)

|Operação|2048 bits|3072 bits|4096 bits|
|--|--:|--:|--:|
Criar Chave|1| 1| 1
Excluir chave (exclusão reversível)|10|10|10 
Limpar a Chave|10|10|10 
Chave de Backup|10|10|10 
Restaurar chave|10|10|10 
Obter Informações da Chave|1100|1100|1100
Encrypt|10000|10000|6000
Descriptografar|1100|360|160
Encapsular|10000|10000|6000
Desencapsular|1100|360|160
Assinar|1100|360|160
Verificar|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operações de chave EC (número de operações por segundo por instância HSM)

Esta tabela descreve o número de operações por segundo para cada tipo de curva.

|Operação|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Criar Chave| 1| 1| 1| 1
Excluir chave (exclusão reversível)|10|10|10|10
Limpar a Chave|10|10|10|10
Chave de Backup|10|10|10|10
Restaurar chave|10|10|10|10
Obter Informações da Chave|1100|1100|1100|1100
Assinar|260|260|165|56
Verificar|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operações de chave AES (número de operações por segundo por instância HSM)
- Operações de Criptografar e Descriptografar assumem um tamanho de pacote de 4 KB.
- Os limites de taxa de transferência para Criptografar/Descriptografar se aplicam a algoritmos AES-CBC e AES-GCM.
- Os limites de taxa de transferência para encapsulamento/desencapsulamento se aplicam ao algoritmo AES-KW.

|Operação|128 bits|192 bits|256 bits|
|--|--:|--:|--:|
Criar Chave|1| 1| 1
Excluir chave (exclusão reversível)|10|10|10
Limpar a Chave|10|10|10
Chave de Backup|10|10|10
Restaurar chave|10|10|10
Obter Informações da Chave|1100|1100|1100
Encrypt|8000|8000 |8000 
Descriptografar|8000|8000|8000
Encapsular|9000|9000|9000
Desencapsular|9000|9000|9000

