<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-download-hotfixes"></a>Para baixar os hotfixes
Execute as etapas a seguir para baixar a atualização do software.

1. Inicie o Internet Explorer e acesse [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Caso esta seja a primeira vez que você usa o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.
    ![Instalar o catálogo](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. Na caixa de pesquisa do Catálogo do Microsoft Update, insira o número do KB (Base de Dados de Conhecimento) do hotfix que você quer baixar, por exemplo, **3063418** e clique em **Pesquisar**.
4. Você verá o pacote **StorSimple Update 1.2 Appliance Update** . Clique em **Adicionar**. A atualização será adicionada ao carrinho de compras.
5. Pesquise quaisquer hotfixes adicionais listados na tabela acima (**3043005** e **3063416**) e adicione cada um deles ao carrinho de compras.
6. Clique em **Exibir carrinho**.
   
    ![Exibir carrinho](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. Clique em **Download**. Especifique ou **Procure** a localização em que deseja que o download apareça. As atualizações são baixadas para o local especificado e colocadas em uma subpasta com o mesmo nome que a atualização. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.

> [!NOTE]
> Os hotfixes devem estar acessíveis nos dois controladores para detectar mensagens de erro potenciais do controlador em par.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Para instalar e verificar os hotfixes do modo normal
Realize as etapas a seguir para instalar e verificar os hotfixes do modo normal. Caso você já os tenha instalado usando o Portal do Azure, ignore essa etapa e vá para [Instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar a atualização do software, acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). No prompt de comando, pressione **Enter**.
2. Selecione a **Opção 1** para fazer logon no dispositivo com acesso completo.
3. Para instalar o pacote de atualização, no prompt de comando, digite:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Use o IP em vez do DNS no caminho de compartilhamento no comando acima. O parâmetro credential é usado somente para acessar um compartilhamento autenticado.
   
    É recomendável usar o parâmetro de credencial para acessar compartilhamentos. Até mesmo os compartilhamentos abertos para "todos" geralmente não são abertos para usuários não autenticados.
   
    Um exemplo de saída é mostrado abaixo.
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. Digite **Y** quando solicitado a confirmar a instalação do hotfix.
5. Monitore a atualização usando o cmdlet `Get-HcsUpdateStatus` .
   
    A saída de exemplo a seguir mostra a atualização em andamento. O `RunInprogress` será `True` quando a atualização estiver em andamento.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 9/02/2015 10:36:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     A saída de exemplo a seguir indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização estiver concluída.

    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 9/02/2015 10:56:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > Ocasionalmente, o cmdlet relatará `False` quando a atualização ainda estiver em andamento. Para garantir que o hotfix seja concluído, aguarde alguns minutos, execute esse comando novamente e verifique se `RunInProgress` é `False`. Em caso positivo, o hotfix foi concluído.
   > 
   > 
6. Depois que a atualização do software estiver concluída, verifique as versões de software do sistema. Digite o seguinte comando:
   
    `Get-HcsSystem`
   
    Você deverá ver as seguintes versões:
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     Se os números de versão não mudarem após a aplicação da atualização, isso indica que houve falha na aplicação do hotfix. Caso isso aconteça, entre em contato com o [Suporte da Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) para obter mais ajuda.
7. Repita as etapas 3 a 5 para instalar o hotfix do modo normal restante (KB3043005).

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Para instalar e verificar hotfixes do modo de manutenção
Use a KB3063416 para instalar atualizações de firmware de disco. Estas são atualizações interrompidas e levam cerca de 30 a 45 minutos para ser concluídas. Você pode optar por instalá-las em uma janela de manutenção planejada conectando-se ao console serial do dispositivo.

Para instalar as atualizações de firmware de disco, siga as instruções abaixo.

1. Coloque o dispositivo no modo de Manutenção. Observe que você não deve usar a comunicação remota do Windows PowerShell ao se conectar a um dispositivo no Modo de manutenção. Você precisará executar esse cmdlet no controlador de dispositivo quando conectado por meio do console serial do dispositivo. Tipo:
   
    `Enter-HcsMaintenanceMode`
   
    Um exemplo de saída é mostrado abaixo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Em seguida, ambos os controladores são reiniciados no modo de manutenção.
2. Para instalar a atualização de firmware de disco, digite:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Um exemplo de saída é mostrado abaixo.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitore o progresso da instalação usando o comando `Get-HcsUpdateStatus` . A atualização é concluída quando o `RunInProgress` muda para `False`.
4. Depois que a instalação for concluída, o controlador no qual o hotfix do modo de manutenção foi instalado será reinicializado. Faça logon como opção 1 com acesso completo e verifique a versão de firmware de disco. Digite:
   
   `Get-HcsFirmwareVersion`
   
   As versões de firmware de disco esperados são:
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para verificar se a versão do software foi atualizada. Em seguida, você pode sair do modo de manutenção. Digite o comando a seguir para cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`
5. Os controladores são reiniciados quando você sai do modo de Manutenção. Depois que as atualizações do firmware de disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, retorne ao portal clássico do Azure. Observe que, por 24 horas, o portal poderá não mostrar que as atualizações do modo de manutenção foram instaladas.

