---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171867"
---
Nesta etapa, você deve testar o ouvinte do grupo de disponibilidade usando um aplicativo cliente em execução na mesma rede.

Para a conectividade do cliente, observe os seguintes requisitos:

* As conexões de cliente para o ouvinte devem vir de máquinas que residem em um serviço de nuvem diferente daquele que hospeda as réplicas de disponibilidade do AlwaysOn.
* Se as réplicas do AlwaysOn estiverem em sub-redes diferentes, os clientes deverão especificar *MultisubnetFailover=True* na cadeia de conexão. Essa condição resulta em tentativas de conexão em paralelo para réplicas nas várias sub-redes. Observe que esse cenário inclui uma implantação de grupo de disponibilidade do AlwaysOn entre regiões.

Um exemplo é conectar-se ao ouvinte de uma das VMs na mesma rede virtual do Azure (mas não uma que hospeda uma réplica). Uma maneira fácil para concluir este teste é tentar conectar o SQL Server Management Studio ao ouvinte do grupo de disponibilidade. Outro método simples é executar [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx) da seguinte maneira:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Se o valor de EndpointPort é *1433*, não é necessário especificá-lo na chamada. A chamada anterior também pressupõe que o computador do cliente está conectado ao mesmo domínio e que o chamador tem permissões no banco de dados usando a autenticação do Windows.
> 
> 

Ao testar o ouvinte, certifique-se de realizar failover ao grupo de disponibilidade para garantir que os clientes possam se conectar ao ouvinte através de failovers.

