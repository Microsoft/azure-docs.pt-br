---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86544039"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>Para criar pontos de extremidade públicos no dispositivo de nuvem

1. Entre no portal do Azure.
2. Acesse **Máquinas Virtuais** e selecione a máquina virtual que está sendo usada como seu dispositivo de nuvem.
    
3. Você precisa criar uma regra de NSG (grupo de segurança de rede) para controlar o fluxo de entrada e saída de tráfego de sua máquina virtual. Execute as etapas a seguir para criar uma regra de NSG.
    1. Selecione **Grupo de segurança de rede**.
        ![Captura de tela da página da máquina virtual. Na seção Configurações, grupo de segurança de rede está realçado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. Clique no grupo de segurança de rede padrão apresentado.
        ![Captura de tela da página do grupo de segurança de rede. O grupo de segurança de rede padrão é realçado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. Selecione **Regras de segurança de entrada**.
        ![Captura de tela de uma página mostrando as propriedades do grupo de segurança de rede padrão. No painel de navegação, as regras de segurança de entrada são realçadas.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. Clique em **+ Adicionar** para criar uma regra de segurança de entrada.
        ![Captura de tela da página regras de segurança de entrada. O sinal de adição e a palavra Add são os próximos uns dos outros e são realçados.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        Na folha Adicionar regra de segurança de entrada:

        1. Para o **Nome**, digite o seguinte nome para o ponto de extremidade: WinRMHttps.
        
        2. Para a **Prioridade**, selecione um número menor do que 1000 (que é a prioridade para a regra padrão). Quanto maior o valor, menor a prioridade.

        3. Defina **Fonte** como **Qualquer**.

        4. Para **Serviço**, selecione **WinRM**. O **Protocolo** é definido automaticamente como **TCP**, e **Intervalo de portas** é definido como **5986**.

        5. Clique em **OK** para criar a regra.

            ![Captura de tela da folha Adicionar regra de segurança de entrada. Os valores são preenchidos conforme descrito no procedimento, e o botão OK é realçado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. A etapa final é associar o grupo de segurança de rede com uma sub-rede ou uma interface de rede específica. Execute as seguintes etapas para associar o grupo de segurança de rede a uma sub-rede.
    1. Acesse **Sub-redes**.
    2. Clique em **+ Associar**.
        ![Captura de tela da página sub-redes. O sinal de adição e a palavra associar estão próximos um do outro e são realçados.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. Selecione sua rede virtual e selecione a sub-rede apropriada.
    4. Clique em **OK** para criar a regra.

        ![Captura de tela da página associar sub-rede. A rede virtual é selecionada e o botão OK é realçado.](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

Após a criação da regra, você poderá exibir seus detalhes para determinar o endereço VIP (IP Virtual Público). Registre esse endereço.


