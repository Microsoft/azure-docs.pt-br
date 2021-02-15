---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67172559"
---
Para dados em trânsito:

- O TLS 1,2 padrão é usado para dados que trafegam entre o dispositivo e o Azure. Não há nenhum fallback para o TLS 1,1 e anterior. A comunicação do agente será bloqueada se o TLS 1,2 não tiver suporte. O TLS 1,2 também é necessário para o gerenciamento do portal e do SDK.
- Quando os clientes acessam seu dispositivo por meio da interface do usuário da Web local de um navegador, o TLS 1,2 padrão é usado como o protocolo de segurança padrão.

    - A prática recomendada é configurar o navegador para usar o TLS 1,2.
    - Se o navegador não der suporte a TLS 1,2, você poderá usar TLS 1,1 ou TLS 1,0.
- Recomendamos que você use o SMB 3,0 com criptografia para proteger os dados ao copiá-los de seus servidores de dados.
