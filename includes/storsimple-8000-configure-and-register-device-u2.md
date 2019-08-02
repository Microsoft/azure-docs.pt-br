---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 56514f5dcf4bfe205ef46ee64dcf4dcf638d4f62
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172127"
---
#### <a name="to-configure-and-register-the-device"></a>Para configurar e registrar o dispositivo

1. Acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Consulte [Usar o PuTTY para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obter instruções. **Siga o procedimento corretamente ou você não conseguirá acessar o console.**

2. Na sessão aberta, pressione **Enter** uma vez para obter um prompt de comando.

3. Você deverá escolher o idioma que deseja definir para seu dispositivo. Especifique a linguagem e pressione **Enter**.

4. No menu do console serial apresentado, escolha a opção 1 para **Fazer logon com acesso completo**.
     Conclua as etapas de 5 a 12 para definir as configurações de rede necessárias e mínimas para seu dispositivo. **Essas etapas de configuração devem ser executadas no controlador ativo do dispositivo.** O menu do console serial indica o estado do controlador na mensagem de faixa. Se você não estiver conectado ao controlador ativo, desconecte e conecte-se ao controlador ativo.

5. No prompt de comando, digite sua senha. A senha do dispositivo padrão é **Senha1**.

6. Digite o seguinte comando: `Invoke-HcsSetupWizard`.

7. Um assistente de instalação aparecerá para ajudá-lo a configurar as definições da rede para o dispositivo. Forneça as seguintes informações:
   
   * Endereço IP para a interface de rede DADOS 0
   * Máscara da sub-rede
   * Gateway
   * Endereço IP do servidor DNS Primário

   Veja abaixo um exemplo de saída.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    No exemplo de saída anterior, você verá que o sistema está validando as configurações de rede após cada etapa do processo.

     > [!NOTE]
     > Você terá que aguardar alguns minutos para que a máscara da sub-rede e as configurações de DNS sejam aplicadas. Se você receber a mensagem de erro "Verificar a conectividade de rede com Dados 0", verifique a conexão de rede física na interface de rede DADOS 0 de seu controlador ativo.

8. (Opcional) configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, **saiba que se você usar um proxy da Web, poderá apenas configurá-lo aqui**. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
9. Configure um servidor NTP primário para seu dispositivo. Os servidores NTP são necessários, pois seu dispositivo deve sincronizar a hora para que ele possa se autenticar com seus provedores de serviço de nuvem. Verifique se sua rede permite que o tráfego NTP passe do data center para a Internet. Se isso não for possível, especifique um servidor NTP interno.

    Um exemplo de saída é mostrado abaixo.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Por motivos de segurança, a senha de administrador do dispositivo expira após a primeira sessão, e você precisará alterá-la agora. Quando solicitado, forneça uma senha de administrador do dispositivo. Uma senha de administrador do dispositivo válida deve ter entre 8 e 15 caracteres. A senha deve conter três das seguintes opções: caracteres com letras minúsculas e maiúsculas, numéricos e especiais.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. A etapa final do assistente de configuração registra seu dispositivo no serviço StorSimple Device Manager. Para isso, será necessária a chave de registro do serviço obtida na etapa 2. Depois de fornecer a chave de registro, talvez seja necessário aguardar de 2 a 3 minutos antes do dispositivo ser registrado.
    
    > [!NOTE]
    > Você pode pressionar Ctrl + C a qualquer momento para sair do assistente de instalação. Se você tiver inserido todas as configurações de rede (endereço IP para Dados 0, Máscara de sub-rede e Gateway), as entradas serão mantidas.
    
    Um exemplo de saída é mostrado abaixo.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Depois do dispositivo ser registrado, uma chave de Criptografia de Dados do Serviço será exibida. Copie essa chave e salve-a em um local seguro. **Essa chave será necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço StorSimple Device Manager.** Consulte a [segurança do StorSimple](../articles/storsimple/storsimple-security.md) para obter mais informações sobre essa chave.
    
    ![Dispositivo de registro do StorSimple 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Para copiar o texto da janela do console serial, basta selecionar o texto. Então, você conseguirá colá-lo na área de transferência ou em qualquer editor de texto. NÃO use Ctrl + C para copiar a chave de criptografia de dados do serviço. Usar Ctrl + C fará com que você saia do assistente de instalação. Como resultado, a senha de administrador do dispositivo não será alterada, e o dispositivo voltará para a senha padrão.
    
13. Saia do console serial.
14. Volte para o Portal do Azure e execute as seguintes etapas:
    
    1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple.
    2. Clique em **Dispositivos**.
    3. Na lista tabular de dispositivos , verifique se o dispositivo conectou com êxito o serviço pesquisando o status. O status do dispositivo deve estar **Pronto para configurar**.
       
        ![Página dos Dispositivos StorSimple](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Talvez seja necessário aguardar alguns minutos para que o status do dispositivo mude para **Pronto para configurar**.
       
        Se o dispositivo não aparecer na lista, verifique se a rede do firewall foi configurada conforme descrito nos [requisitos de rede para seu dispositivo StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md). Verifique se a porta 9354 está aberta para comunicações de saída, pois ela é usada pelo barramento de serviço para a comunicação serviço-dispositivo do Gerenciador de Dispositivos do StorSimple.

