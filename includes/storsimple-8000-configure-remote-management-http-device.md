---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67171983"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Para configurar o gerenciamento remoto no dispositivo de nuvem

1. Em seu serviço StorSimple Device Manager, clique em **Dispositivos**. Selecione e clique em seu dispositivo de nuvem na lista de dispositivos conectados ao serviço.
    ![Seleção de dispositivo de nuvem StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Acesse **Configurações > Segurança** para abrir a folha **Configurações de segurança**.

     ![Configurações de segurança do StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Vá até a seção **Gerenciamento Remoto**. Clique na caixa **Gerenciamento remoto**.
     ![Gerenciamento remoto do StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. Na folha **Gerenciamento remoto**:

    1. Habilite **Habilitar administração remota**.
    2. O padrão é conectar-se por HTTPS. Escolha se conectar usando HTTP. A conexão por HTTP só será aceitável em redes confiáveis. Certifique-se de que HTTP esteja habilitado.
    3. Na barra de comandos na parte superior da folha, clique em **... Mais** e clique em **baixar certificado** para baixar um certificado de gerenciamento remoto. Especifique um local no qual o arquivo será salvo. Esse certificado deve ser instalado na máquina cliente ou host que você usa para se conectar ao dispositivo de nuvem.

        ![Folha do gerenciamento remoto](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Clique em **Salvar** e, quando receber a solicitação, confirme as alterações.