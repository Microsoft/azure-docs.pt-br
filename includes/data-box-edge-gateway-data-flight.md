---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172559"
---
Para dados em trânsito:

- Padrão de TLS 1.2 é usado para dados que trafegam entre o dispositivo e o Azure. Não há nenhum fallback para o TLS 1.1 e versões anteriores. Comunicação do agente será bloqueada se não há suporte para TLS 1.2. TLS 1.2 também é necessária para o gerenciamento do SDK e do portal.
- Quando os clientes acessam seu dispositivo por meio da interface do usuário de um navegador da web local, padrão do protocolo TLS 1.2 é usado como o protocolo de segurança padrão.

    - A prática recomendada é configurar seu navegador para usar o TLS 1.2.
    - Se o navegador não dá suporte a TLS 1.2, você pode usar o TLS 1.1 ou TLS 1.0.
- É recomendável que você usar o SMB 3.0 com criptografia para proteger os dados ao copiar de seus servidores de dados.
