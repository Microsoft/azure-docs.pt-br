---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171980"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Para interromper e iniciar um dispositivo de nuvem

1. Para interromper um dispositivo de nuvem, acesse a VM de seu dispositivo de nuvem.
    ![Máquina virtual do Dispositivo de nuvem StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Na barra de comandos, clique em **Parar**.

    ![Máquina virtual do Dispositivo de nuvem StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Quando solicitado a confirmar, clique em **Sim**.

    ![Máquina virtual do Dispositivo de nuvem StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Quando você para uma VM, ela é desalocada. Enquanto o dispositivo de nuvem está parando, seu status muda para **Desalocando**. Após a interrupção do dispositivo de nuvem, seu status muda para **Parado (desalocado)** .

    ![Máquina virtual do Dispositivo de nuvem StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Após a interrupção da VM, clique em **Iniciar** (o botão torna-se disponível) para iniciar a VM. Após o dispositivo de nuvem iniciar, seu status muda para **Iniciado**.

    ![Máquina virtual do Dispositivo de nuvem StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Use estes cmdlets para interromper e iniciar um dispositivo de nuvem.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Para reiniciar um dispositivo de nuvem

Para reiniciar um dispositivo de nuvem, acesse a VM de seu dispositivo de nuvem. Na barra de comandos, clique em **Reiniciar**. Quando receber a solicitação, confirme a reinicialização. Quando o dispositivo de nuvem estiver pronto para uso, seu status mudará para **Em execução**.

![Máquina virtual do Dispositivo de nuvem StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Use este cmdlet para reiniciar um dispositivo de nuvem.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

