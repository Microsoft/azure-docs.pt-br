---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: victorh
ms.openlocfilehash: 74031a8dbc9b64d6a09533789eed1296ff334d47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563319"
---
Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS.

Por exemplo, o domínio 'contoso.com' pode conter vários registros DNS, como 'mail.contoso.com' (para um servidor de email) e 'www.contoso.com' (para um site da Web).

Ao criar uma zona DNS no DNS do Azure:

* O nome da zona deve ser exclusivo dentro do grupo de recursos e a zona não deve existir ainda. Caso contrário, a operação falhará.
* O mesmo nome de zona pode ser reutilizado em outro grupo de recursos ou uma assinatura do Azure diferente.
* Quando várias zonas têm o mesmo nome, cada instância recebe endereços de servidor de nomes diferentes. Apenas um conjunto de endereços pode ser configurado com o registrador de nome de domínio.

> [!NOTE]
> Você não precisa ter um nome de domínio para criar uma zona DNS com esse nome de domínio no DNS do Azure. No entanto, você precisa ser o proprietário do domínio para configurar os servidores de nomes do DNS do Azure como os servidores de nome corretos para o nome de domínio no registrador de nome de domínio.
> 
> Confira [Delegar um domínio ao DNS do Azure](../articles/dns/dns-domain-delegation.md)para saber mais.