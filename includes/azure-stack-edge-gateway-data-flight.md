---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466143"
---
Para dados em trânsito:

- O protocolo TLS 1,2 é usado para dados que trafegam entre o dispositivo e o Azure. Não há nenhum fallback para o TLS 1,1 e anterior. A comunicação do agente será bloqueada se o TLS 1,2 não tiver suporte. O TLS 1,2 também é necessário para o gerenciamento do portal e do SDK.
- Quando os clientes acessam seu dispositivo por meio da interface do usuário da Web local de um navegador, o TLS 1,2 padrão é usado como o protocolo de segurança padrão.

  - A prática recomendada é configurar o navegador para usar o TLS 1,2.
  - Seu dispositivo só dá suporte a TLS 1,2 e não oferece suporte a versões mais antigas do TLS 1,1 nem TLS 1,0.
- Recomendamos que você use o SMB 3,0 com criptografia para proteger os dados ao copiá-los de seus servidores de dados.
