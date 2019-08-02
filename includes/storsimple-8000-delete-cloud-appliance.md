---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171979"
---
#### <a name="to-delete-a-cloud-appliance"></a>Para excluir um dispositivo de nuvem

1. Entre no Portal do Azure.
2. Você só pode excluir um dispositivo desativado que não contenha dados. Exclua os dados do dispositivo pela primeira vez ou faça [failover dos dados](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md) em contêineres de volume para outro dispositivo. Depois que os dados forem excluídos, você estará pronto para desativar o dispositivo.
3. Na página de serviço do StorSimple Device Manager, clique em **Dispositivos** e selecione o dispositivo. Clique com o botão direito do mouse e selecione **Desativar**.
4. Assim que o dispositivo for desativado, clique com o botão direito do mouse no dispositivo e selecione **Excluir**.

    ![Selecionar um dispositivo desativado e clicar em excluir](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. Digite o nome do dispositivo para confirmar sua exclusão. Após a exclusão do dispositivo, a lista de dispositivos será atualizada.

    ![Confirmar exclusão](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. Você será notificado depois que o dispositivo for excluído.

    ![Notificação de exclusão bem-sucedida de dispositivo](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. A lista de atualizações de dispositivos para indicar o dispositivo excluído.

    ![Lista de dispositivos atualizada](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
