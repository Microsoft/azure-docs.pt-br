---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171873"
---
1. No Gerenciador de Cluster de Failover, expanda **funções** e realce seu grupo de disponibilidade.  

2. Na guia **Recursos** , clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

3. Selecione a guia **Dependências** . Se houver vários recursos listados, verifique se os endereços IP têm dependências OR, e não AND.  

4. Clique em **OK**.

5. Clique com o botão direito do mouse no nome do ouvinte e, em seguida, clique em **Colocar online**.

6. Quando o ouvinte estiver online, na guia **Recursos**, clique com o botão direito do mouse no grupo de disponibilidade e clique em **Propriedades**.
   
    ![Configurar o recurso do grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Crie uma dependência no recurso de nome do ouvinte (não o nome de recursos de endereço IP) e, em seguida, clique em **OK**.
   
    ![Adicionar dependência no nome do ouvinte](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Inicie o SQL Server Management Studio e conecte-se à réplica principal.

9. Vá até **Alta Disponibilidade do AlwaysOn** > **Grupos de Disponibilidade** >  **\<AvailabilityGroupName\>**  > **Ouvintes do Grupo de Disponibilidade**.  
    O nome do ouvinte que você criou no Gerenciador de Cluster de Failover deve ser exibido.

10. Clique com o botão direito do mouse no nome do ouvinte e, em seguida, clique em **Propriedades**.

11. Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (neste tutorial, 1433 era o padrão) e clique em **OK**.

