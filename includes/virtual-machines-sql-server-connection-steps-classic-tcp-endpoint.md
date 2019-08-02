---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171865"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Criar um ponto de extremidade TCP para a máquina virtual
Para acessar o SQL Server da Internet, a máquina virtual deve ter um ponto de extremidade para escutar comunicação TCP de entrada. Essa etapa de configuração do Azure, direciona o tráfego da porta TCP de entrada para uma porta TCP que está acessível para a máquina virtual.

> [!NOTE]
> Se estiver se conectando dentro do mesmo serviço de nuvem ou rede virtual, não precisará criar um ponto de extremidade acessível publicamente. Nesse caso, você pode continuar para a próxima etapa. Para obter mais informações, consulte [Cenários de conexão](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. No Portal do Azure, selecione **Máquinas virtuais (clássico)** .
2. Em seguida, selecione a máquina virtual do SQL Server.
3. Selecione **Pontos de Extremidade** e clique no botão **Adicionar** na parte superior da folha de Pontos de extremidade.
   
    ![Etapas do Portal para Criação de Pontos de Extremidade](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. Na folha **Adicionar Ponto de Extremidade**, forneça um **Nome**, como SQLEndpoint.
5. Selecione**TCP** como o **Protocolo**.
6. Para **Porta pública**, especifique um número de porta, como **57500**.
7. Para **Porta privada**, especifique a porta de escuta do SQL Server, cujo valor padrão é **1433**.
8. Clique em **OK** para criar o ponto de extremidade.

