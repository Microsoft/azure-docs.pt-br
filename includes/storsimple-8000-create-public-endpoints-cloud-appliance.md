---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 1cf5bbdad555c50c418851904f36a578522843b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171984"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Para criar pontos de extremidade públicos no dispositivo de nuvem

1. Entre no Portal do Azure.
2. Acesse **Máquinas Virtuais** e selecione a máquina virtual que está sendo usada como seu dispositivo de nuvem.
    
3. Você precisa criar uma regra de NSG (grupo de segurança de rede) para controlar o fluxo de entrada e saída de tráfego de sua máquina virtual. Execute as etapas a seguir para criar uma regra de NSG.
    1. Selecione **Grupo de segurança de rede**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Clique no grupo de segurança de rede padrão apresentado.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selecione **Regras de segurança de entrada**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Clique em **+ Adicionar** para criar uma regra de segurança de entrada.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Na folha Adicionar regra de segurança de entrada:

        1. Para o **nome**, digite o seguinte nome para o ponto de extremidade: WinRMHttps.
        
        2. Para a **Prioridade**, selecione um número menor do que 1000 (que é a prioridade para a regra padrão). Quanto maior o valor, menor a prioridade.

        3. Defina **Fonte** como **Qualquer**.

        4. Para **Serviço**, selecione **WinRM**. O **Protocolo** é definido automaticamente como **TCP**, e **Intervalo de portas** é definido como **5986**.

        5. Clique em **OK** para criar a regra.

            ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. A etapa final é associar o grupo de segurança de rede com uma sub-rede ou uma interface de rede específica. Execute as seguintes etapas para associar o grupo de segurança de rede a uma sub-rede.
    1. Acesse **Sub-redes**.
    2. Clique em **+ Associar**.
        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selecione sua rede virtual e selecione a sub-rede apropriada.
    4. Clique em **OK** para criar a regra.

        ![](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Após a criação da regra, você poderá exibir seus detalhes para determinar o endereço VIP (IP Virtual Público). Registre esse endereço.


