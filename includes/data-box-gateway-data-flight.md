---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581532"
---
Para dados em trânsito:

- O TLS 1,2 padrão é usado para dados que trafegam entre o dispositivo e o Azure. Não há nenhum fallback para o TLS 1,1 e anterior. A comunicação do agente será bloqueada se o TLS 1,2 não tiver suporte. O TLS 1,2 também é necessário para o gerenciamento do portal e do SDK.
- Quando os clientes acessam seu dispositivo por meio da interface do usuário da Web local de um navegador, o TLS 1,2 padrão é usado como o protocolo de segurança padrão.

    - A prática recomendada é configurar o navegador para usar o TLS 1,2.
    - Se o navegador não der suporte a TLS 1,2, você poderá usar TLS 1,1 ou TLS 1,0.
- Recomendamos que você use o SMB 3,0 com criptografia para proteger os dados ao copiá-los de seus servidores de dados.
