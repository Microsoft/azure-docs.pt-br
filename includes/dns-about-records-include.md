---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: f7fa018225d82e2db4a3c51b333aea6fcb9a1514
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563320"
---
### <a name="record-names"></a>Nomes de registros

No DNS do Azure, os registros são especificados usando nomes relativos. Um FQDN (nome de domínio *totalmente qualificado*) inclui o nome da zona, enquanto um nome *relativo* não o inclui. Por exemplo, o nome relativo do registro 'www' na zona 'contoso.com' fornece o nome totalmente qualificado do registro 'www.contoso.com'.

Um registro *apex* é um registro DNS na raiz (ou *apex*) de uma zona DNS. Por exemplo, na zona DNS ‘contoso.com’, um registro apex também tem o nome totalmente qualificado 'contoso.com' (ele às vezes é chamado de domínio *sem www*).  Por convenção, o nome relativo '\@' é usado para representar os registros de apex.

### <a name="record-types"></a>Tipos de registro

Cada registro DNS tem um nome e um tipo. Os registros são organizados em vários tipos de acordo com os dados que eles contêm. O tipo mais comum é um registro 'A', que mapeia um nome para um endereço IPv4. Outro tipo comum é um registro 'MX', que mapeia um nome para um servidor de email.

O DNS do Azure dá suporte a todos os tipos de registro DNS comuns: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV, and TXT. Observe que [registros SPF são representados usando registros TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Conjuntos de registros

Às vezes, você precisa criar mais de um registro DNS com determinado nome e tipo. Por exemplo, vamos supor que o site 'www.contoso.com' seja hospedado em dois endereços IP diferentes. O site exige dois registros A diferentes, um para cada endereço IP. Aqui está um exemplo de um conjunto de registros:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

O DNS do Azure gerencia todos os registros DNS usando *conjuntos de registros*. Um conjunto de registros (também conhecido como conjunto de registros de *recurso*) é uma coleção de registros DNS em uma zona que tem o mesmo nome e o mesmo tipo. A maioria dos conjuntos de registros contém um único registro. No entanto, exemplos como o que é mostrado acima, em que um conjunto de registros contém mais de um registro, não são incomuns.

Por exemplo, digamos que você já criou um registro 'www' A na zona 'contoso.com' apontando para o endereço IP '134.170.185.46' (o primeiro registro acima).  Para criar o segundo registro, você deve adicioná-lo ao conjunto de registros existente em vez de criar um conjunto de registros adicional.

Os conjuntos de registros SOA e CNAME são exceções. Os padrões DNS não permitem vários registros com o mesmo nome para esses tipos e, assim, esses conjuntos de registros podem conter apenas um único registro.