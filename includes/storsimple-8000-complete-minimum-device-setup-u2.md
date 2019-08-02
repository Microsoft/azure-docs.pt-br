---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c44effe0bde3c7e880e53706fcb59d91a8605e7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171973"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Para concluir a configuração mínima do dispositivo StorSimple

   > [!NOTE]
   > Após concluir a configuração mínima do dispositivo, não é possível alterar o seu nome.
   
1. Na lista tabular de dispositivos na folha **Dispositivos**, selecione e clique em seu dispositivo. O dispositivo está em um estado **Pronto para configurar**. A folha **Configurar dispositivo** é aberta.

     ![Interfaces de rede da instalação mínima do dispositivo do StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Na folha **Configurar dispositivo**:
   
   1. Forneça um **nome amigável** para seu dispositivo. O nome do dispositivo padrão reflete as informações, tais como, o número de série e o modelo do dispositivo. Você pode atribuir um nome amigável de até 64 caracteres para gerenciar o dispositivo.
   2. Defina o **fuso horário** com base na localização geográfica na qual o dispositivo está sendo implantado. Seu dispositivo usa esse fuso horário para todas as operações agendadas.
   3. Nas **configurações de DATA 0**:

       1. Sua interface de rede DATA 0 aparece como habilitada com as configurações de rede (IP, sub-rede, gateway) definidas por meio do assistente de configuração. DATA 0 também é habilitada automaticamente para a nuvem e iSCSI.

       2. Forneça os endereços IP fixos para os Controladores 0 e 1. **Os endereços IP fixos do controlador precisam ser IPs gratuitos dentro da sub-rede acessível pelo endereço IP do dispositivo.** Se a interface DADOS 0 foi configurada para o IPv4, os endereços IP fixos precisam ser fornecidos no formato IPv4. Se você forneceu um prefixo para a configuração de IPv6, os endereços IP fixos serão preenchidos automaticamente nesses campos.

            ![Interfaces de rede da instalação mínima do dispositivo do StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Os endereços IP fixos do controlador são usados para atender às atualizações do dispositivo e para a coleta de lixo. Portanto, os IPs fixos devem ser roteáveis e conseguir se conectar à Internet. Você pode verificar se seus IPs de controlador fixos são roteáveis usando o cmdlet [Test-HcsmConnection][Test]. O exemplo a seguir mostra IPs de controlador fixos que são roteados para a Internet e podem acessar os servidores do Microsoft Update.

            ![Test-HcsmConnection mostrando IPs roteáveis](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Clique em **OK**. Começa a configuração do dispositivo. Após a conclusão da configuração do dispositivo, você será notificado. O status do dispositivo muda para **Online** na folha **Dispositivos**.

    ![Interfaces de rede da instalação mínima do dispositivo do StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx
