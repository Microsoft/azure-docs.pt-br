---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730604"
---
Conecte-se à sua VM do Windows usando o protocolo RDP (RDP) por meio do IP que você passou durante a criação da VM.

1. Em seu cliente, abra o RDP. 
1. Vá para **Iniciar** e digite **mstsc**.
1. No painel **conexão de área de trabalho remota** , insira o endereço IP da VM e as credenciais de acesso usadas no arquivo de parâmetros do modelo de VM e, em seguida, selecione **conectar**.

   ![Captura de tela do painel de Conexão de Área de Trabalho Remota para se conectar via RDP à sua VM do Windows.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Talvez seja necessário aprovar a conexão com um computador não confiável. 

Agora você está conectado à sua VM que é executada no dispositivo. 
