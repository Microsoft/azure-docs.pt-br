---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 7700f1c92aecab76dbc347814b7b161bc3d822a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171978"
---
### <a name="to-configure-and-register-the-device"></a>Para configurar e registrar o dispositivo
1. Acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Consulte [Usar o PuTTY para se conectar ao console serial do dispositivo](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) para obter instruções. **Siga o procedimento corretamente ou você não conseguirá acessar o console.**
2. Na sessão aberta, pressione **Enter** uma vez para obter um prompt de comando.
3. Você deverá escolher o idioma que deseja definir para seu dispositivo. Especifique a linguagem e pressione **Enter**.
   
    ![O StorSimple configura e registra o dispositivo 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. No menu do console serial apresentado, escolha a opção 1 para **Fazer logon com acesso completo**.
   
    ![Dispositivo de registro do StorSimple 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Conclua as etapas a seguir para definir as configurações de rede necessárias e mínimas para seu dispositivo.
   
   > [!IMPORTANT]
   > Essas etapas de configuração devem ser executadas no controlador ativo do dispositivo. O menu do console serial indica o estado do controlador na mensagem de faixa. Se você não estiver conectado ao controlador ativo, desconecte e conecte-se ao controlador ativo.
   
   1. No prompt de comando, digite sua senha. A senha do dispositivo padrão é **Senha1**.
   2. Digite o seguinte comando:
      
        `Invoke-HcsSetupWizard`
   3. Um assistente de instalação aparecerá para ajudá-lo a configurar as definições da rede para o dispositivo. Forneça as seguintes informações:
      
      * Endereço IP da interface de rede DADOS 0
      * Máscara da sub-rede
      * Gateway
      * Endereço IP do servidor DNS Primário
      * Endereço IP do servidor NTP Primário
      
      > [!NOTE]
      > Você terá que aguardar alguns minutos para que a máscara de sub-rede e as configurações de DNS sejam aplicadas.
    
   4. Opcionalmente, configure seu servidor proxy da Web.
      
      > [!IMPORTANT]
      > Embora a configuração do proxy da Web seja opcional, saiba que se você usar um proxy da Web, só poderá configurá-lo aqui. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md).
     
6. Pressione Ctrl + C para sair do assistente de instalação.
8. Execute o cmdlet a seguir para apontar o dispositivo para o portal do Microsoft Azure Governamental (já que ele aponta para o portal clássico do Azure público por padrão). Isso reiniciará os dois controladores. É recomendável que você use duas sessões PuTTY para se conectar simultaneamente a ambos os controladores para poder ver quando cada controlador for reiniciado.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Você verá uma mensagem de confirmação. Aceite o padrão (**Y**).
9. Execute o seguinte cmdlet para retomar a instalação:
   
    `Invoke-HcsSetupWizard`
   
    ![Retomar o assistente de instalação](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Aceite as configurações de rede. Você verá uma mensagem de validação depois que aceitar cada configuração.
11. Por motivos de segurança, a senha de administrador do dispositivo expira após a primeira sessão, e você precisará alterá-la agora. Quando solicitado, forneça uma senha de administrador do dispositivo. Uma senha de administrador do dispositivo válida deve ter entre 8 e 15 caracteres. A senha deve conter três das seguintes opções: caracteres com letras minúsculas e maiúsculas, numéricos e especiais.
    
    <br/>![Dispositivo de registro do StorSimple 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. A etapa final do assistente de configuração registra seu dispositivo no serviço StorSimple Device Manager. Para isso, será necessária a chave de registro do serviço obtida na [Etapa 2: obter a chave de registro do serviço](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Depois de fornecer a chave de registro, talvez seja necessário aguardar de 2 a 3 minutos antes do dispositivo ser registrado.
    
    > [!NOTE]
    > Você pode pressionar Ctrl + C a qualquer momento para sair do assistente de instalação. Se você tiver inserido todas as configurações de rede (endereço IP para Dados 0, Máscara de sub-rede e Gateway), as entradas serão mantidas.
    
    ![Progresso do registro do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Depois do dispositivo ser registrado, uma chave de Criptografia de Dados do Serviço será exibida. Copie essa chave e salve-a em um local seguro. **Essa chave é necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço Gerenciador de Dispositivos StorSimple.** Consulte a [segurança do StorSimple](../articles/storsimple/storsimple-8000-security.md) para obter mais informações sobre essa chave.
    
    ![Dispositivo de registro do StorSimple 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Para copiar o texto da janela do console serial, basta selecionar o texto. Então, você conseguirá colá-lo na área de transferência ou em qualquer editor de texto.
    > 
    > NÃO use **Ctrl + C** para copiar a chave de criptografia de dados do serviço. Usar **Ctrl + C** fará com que você saia do assistente de instalação. Como resultado, a senha de administrador do dispositivo não será alterada, e o dispositivo voltará para a senha padrão.
    
14. Saia do console serial.
15. Volte para o Portal do Azure Governamental e conclua as seguintes etapas:
    
    1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple.
    2. Clique em **Dispositivos**. Na lista de dispositivos, identifique o dispositivo que você está implantando. Verifique se o dispositivo se conectou com êxito ao serviço ao examinar seu status. O status do dispositivo deve ser **Online**.
            
        Se o status do dispositivo for **Offline**, aguarde alguns minutos para o dispositivo ficar online.
       
        Se o dispositivo continuar offline após alguns minutos, você precisará se certificar de que a rede do firewall foi configurada conforme descrito nos [requisitos de rede para seu dispositivo StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Verifique se a porta 9354 está aberta para comunicações de saída, pois ela é usada pelo barramento de serviço para a comunicação serviço-dispositivo do StorSimple Device Manager.

