---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171869"
---
Após criar o ouvinte do grupo de disponibilidade, é possível que seja necessário ajustar os parâmetros do cluster RegisterAllProvidersIP e HostRecordTTL para o recurso do ouvinte. Esses parâmetros podem reduzir o tempo de reconexão após um failover, o que pode impedir as interrupções de conexão. Para obter mais informações sobre esses parâmetros, bem como um código de exemplo, consulte [Criar ou configurar um ouvinte de grupo de disponibilidade](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

