---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0755c01fe8e13e8e39c0b453198f2b67c51a2bc4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172121"
---
#### <a name="to-download-hotfixes"></a>Para baixar os hotfixes

Execute as etapas a seguir para baixar a atualização do software do Catálogo do Microsoft Update.

1. Inicie o Internet Explorer e navegue até [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com).
2. Caso esta seja a primeira vez que você usa o Catálogo do Microsoft Update neste computador, clique em **Instalar** quando a instalação do complemento do Catálogo do Microsoft Update for solicitada.

    ![Instalar o catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Na caixa de pesquisa do Catálogo do Microsoft Update, insira o número do KB (Base de Dados de Conhecimento) do hotfix que você quer baixar, por exemplo, **4011839** e clique em **Pesquisar**.
   
    A listagem de hotfixes aparece, por exemplo, **Atualização de pacote cumulativo de software 4.0 para a série 8000 do StorSimple**.
   
    ![Pesquisar o catálogo](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Clique em **Download**. Especifique ou **Navegue** até o local em que deseja que o download apareça. Clique nos arquivos para baixar no local e pasta especificados. A pasta também pode ser copiada para um compartilhamento de rede que seja acessível do dispositivo.
5. Procure quaisquer hotfixes adicionais listados na tabela acima (**4011841**) e baixe os arquivos correspondentes às pastas específicas, conforme listado na tabela anterior.

> [!NOTE]
> Os hotfixes devem estar acessíveis nos dois controladores para detectar mensagens de erro potenciais do controlador em par.
>
> Os hotfixes devem ser copiados em pastas separadas 3. Por exemplo, a atualização do software do dispositivo/agente Cis/MDS pode ser copiada na pasta _FirstOrderUpdate_, todas as outras atualizações sem interrupção devem ser copiadas na pasta _SecondOrderUpdate_ e as atualizações do modo de manutenção devem ser copiadas na pasta _ThirdOrderUpdate_.

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Para instalar e verificar os hotfixes do modo normal

Siga as etapas abaixo para instalar e verificar os hotfixes do modo normal. Caso você já os tenha instalado usando o portal clássico do Azure, ignore essa etapa e vá para [Instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar os hotfixes, acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Siga as instruções detalhadas em [Usar o PuTTy para se conectar ao console serial do dispositivo](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). No prompt de comando, pressione **Enter**.
2. Selecione a Opção 1, **Fazer logon com acesso completo**. Recomendamos que você instale o hotfix no controlador passivo primeiro.
3. Para instalar o hotfix, no prompt de comando, digite:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Use o IP em vez do DNS no caminho de compartilhamento no comando acima. O parâmetro credential é usado somente para acessar um compartilhamento autenticado.
   
    É recomendável usar o parâmetro de credencial para acessar compartilhamentos. Até mesmo os compartilhamentos abertos para "todos" geralmente não são abertos para usuários não autenticados.
   
    Forneça a senha, quando solicitado.
   
    Abaixo está uma saída de exemplo para instalar as atualizações de ordem primeiro. Para obter a primeira atualização da ordem, aponte para o arquivo específico.
   
        ```
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ```
4. Digite **Y** quando solicitado a confirmar a instalação do hotfix.
5. Monitore a atualização usando o cmdlet `Get-HcsUpdateStatus` . Primeiro, a atualização será concluída no controlador passivo. Depois que o controlador passivo for atualizado, haverá um failover e a atualização será, então, aplicada no outro controlador. A atualização é concluída quando ambos os controladores são atualizados.
   
    A saída de exemplo a seguir mostra a atualização em andamento. O `RunInprogress` será `True` quando a atualização estiver em andamento.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 02/03/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     A saída de exemplo a seguir indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização estiver concluída.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 02/03/2017 9:15:55 AM
    LastUpdateTimestamp : 02/03/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Ocasionalmente, o cmdlet relatará `False` quando a atualização ainda estiver em andamento. Para garantir que o hotfix seja concluído, aguarde alguns minutos, execute esse comando novamente e verifique se `RunInProgress` é `False`. Em caso positivo, o hotfix foi concluído.

6. Depois que a atualização do software estiver concluída, verifique as versões de software do sistema. Digite:
   
    `Get-HcsSystem`
   
    Você deverá ver as seguintes versões:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 4.0`
   * `HcsSoftwareVersion: 6.3.9600.17820`
   
     Se o número de versão não mudar após a aplicação da atualização, isso indica que houve falha na aplicação do hotfix. Caso isso aconteça, entre em contato com o [Suporte da Microsoft](../articles/storsimple/storsimple-contact-microsoft-support.md) para obter mais ajuda.
     
     > [!IMPORTANT]
     > Você deve reiniciar o controlador ativo via cmdlet `Restart-HcsController` antes de aplicar a próxima atualização.
     
7. Repita as etapas 3 a 5 para instalar o agente Cis/MDS baixado para a pasta _FirstOrderUpdate_. 
8. Repita as etapas 3 a 5 para instalar as atualizações de ordem de segundo. **Para atualizações da segunda ordem, várias atualizações podem ser instaladas executando apenas o `Start-HcsHotfix cmdlet` e apontando para a pasta onde as atualizações da segunda ordem estão localizadas. O cmdlet executará todas as atualizações disponíveis na pasta.** Se uma atualização já estiver instalada, a lógica de atualização detectar que e não aplicar essa atualização. 

Depois que todos os hotfixes são instalados, use o `Get-HcsSystem` cmdlet. As versões devem ser:

   * `CisAgentVersion:  1.0.9441.0`
   * `MdsAgentVersion: 35.2.2.0`
   * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Para instalar e verificar hotfixes do modo de manutenção
Use o KB4011837 para instalar atualizações de firmware de disco. Estas são atualizações com interrupção e levam cerca de 30 minutos para ser concluídas. Você pode optar por instalá-las em uma janela de manutenção planejada conectando-se ao console serial do dispositivo.

Observe que, se o firmware de disco já estiver atualizado, você não precisará instalar essas atualizações. Execute o cmdlet `Get-HcsUpdateAvailability` no console serial do dispositivo para verificar se as atualizações estão disponíveis e se elas são interruptivas (modo de manutenção) ou não interruptivas (modo normal).

Para instalar as atualizações de firmware de disco, siga as instruções abaixo.

1. Coloque o dispositivo no modo de manutenção. **Observe que você não deve usar a comunicação remota do Windows PowerShell ao se conectar a um dispositivo no modo de manutenção. Em vez disso, execute esse cmdlet no controlador do dispositivo quando conectado por meio do console serial do dispositivo.** Digite:
   
    `Enter-HcsMaintenanceMode`
   
    Um exemplo de saída é mostrado abaixo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
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
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitore o progresso da instalação usando o comando `Get-HcsUpdateStatus` . A atualização é concluída quando o `RunInProgress` muda para `False`.
4. Depois que a instalação for concluída, o controlador no qual o hotfix do modo de manutenção foi instalado será reiniciado. Faça logon como opção 1, **Faça logon com acesso completo**, e verifique a versão de firmware de disco. Digite:
   
   `Get-HcsFirmwareVersion`
   
   As versões de firmware de disco esperados são:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N002, 0106`
   
   Um exemplo de saída é mostrado abaixo.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17820
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para verificar se a versão do software foi atualizada. Em seguida, você pode sair do modo de manutenção. Para isso, digite o comando a seguir para cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`

5. Os controladores são reiniciados quando você sai do modo de manutenção. Depois que as atualizações do firmware de disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, retorne ao portal clássico do Azure. Observe que, por 24 horas, o portal poderá não mostrar que as atualizações do modo de manutenção foram instaladas.

