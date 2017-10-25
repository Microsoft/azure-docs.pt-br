---
title: Gerenciar os controladores de dispositivo StorSimple | Microsoft Docs
description: Saiba como parar, reiniciar, desligar ou redefinir os controladores de dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4ee989d0-956f-4c14-951e-fd4e490ea09d
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli
ms.openlocfilehash: 67dbb0c4066002256efbab6061157c641527e441
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerenciar controladores de dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este tutorial descreve as diferentes operações que podem ser executadas em seus controladores de dispositivo StorSimple. Os controladores em seu dispositivo StorSimple são controladores redundantes (par) em uma configuração ativo-passivo. Em um determinado momento, somente um controlador está ativo e está processando todas as operações de disco e rede. O outro controlador está em um modo passivo. Se o controlador ativo falhar, o controlador passivo ficará ativo automaticamente.

Este tutorial inclui instruções passo a passo para gerenciar os controladores de dispositivo usando a:

* Seção **Controladores** na página **Manutenção** no serviço StorSimple Manager
* Windows PowerShell para StorSimple.

É recomendável gerenciar os controladores de dispositivo por meio do serviço StorSimple Manager. Se só for possível executar uma ação usando o Windows PowerShell para StorSimple, o tutorial fará uma observação sobre isso.

Depois de ler este tutorial, você poderá:

* Reiniciar ou desligar um controlador de dispositivo StorSimple
* Desligar um dispositivo StorSimple
* Redefinir o dispositivo StorSimple para os padrões de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou desligar um único controlador
Uma reinicialização ou um desligamento de controlador não é necessário como parte da operação normal do sistema. As operações de desligamento para um controlador de dispositivo único são comuns apenas em casos em que um componente de hardware de dispositivo com falha requer substituição. Uma reinicialização de controlador também pode ser necessária em uma situação em que o desempenho é afetado pelo uso excessivo de memória ou controlador com defeito. Se você deseja ativar e testar o controlador substituído também convém reiniciar um controlador após a sua substituição com êxito.

Reiniciar um dispositivo que não é interrompido para os iniciadores conectados, supondo que o controlador passivo está disponível. Se um controlador passivo não está disponível ou está desligado, reiniciar o controlador ativo pode resultar na interrupção do serviço e em tempo de inatividade.

> [!IMPORTANT]
> * **Um controlador em execução nunca deve ser fisicamente removido, pois isso resultaria em uma perda de redundância e maior risco de tempo de inatividade.**
> * O procedimento a seguir aplica-se somente ao dispositivo físico StorSimple. Para saber mais sobre como iniciar, parar e reiniciar o dispositivo virtual, confira [Trabalhar com o dispositivo virtual](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).
> 
> 

Você pode reiniciar ou desligar um controlador de dispositivo único usando o portal clássico do Azure do serviço StorSimple Manager ou do Windows PowerShell para StorSimple

Para gerenciar os controladores de dispositivo no Portal Clássico do Azure, execute as seguintes etapas.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Para reiniciar ou desligar um controlador no portal clássico
1. Navegue até **Dispositivos > Manutenção**.
2. Vá para **Status de Hardware** e verifique se o status de ambos os controladores em seu dispositivo está **Íntegro**.
   
    ![Verificar a integridade dos controladores do dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766017.png)
3. Na parte inferior da página **Manutenção**, clique em **Gerenciar Controladores**.
   
    ![Gerenciar controladores de dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > Se você não conseguir ver **Gerenciar Controladores**, é preciso instalar atualizações. Para obter mais informações, consulte [Atualizar seu dispositivo StorSimple](storsimple-update-device.md).
   > 
   > 
4. Na caixa de diálogo **Alterar as Configurações do Controlador** , faça o seguinte:
   
   1. Na lista suspensa **Selecionar Controlador** , selecione o controlador que deseja gerenciar. As opções são Controlador 0 e Controlador 1. Esses controladores também são identificados como ativo ou passivo.
      
      > [!NOTE]
      > Um controlador não pode ser gerenciado se estiver indisponível ou desligado e não aparecerá na lista suspensa.
      > 
      > 
   2. Na lista suspensa **Selecionar Ação**, escolha **Reiniciar controlador** ou **Desligar controlador**.
      
       ![Reiniciar o controlador passivo do dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766020.png)
   3. Clique no ícone de verificação  ![Ícone de verificação](./media/storsimple-manage-device-controller/IC740895.png).

Isso irá reiniciar ou desligar o controlador. A tabela a seguir resume os detalhes do que acontece dependendo das seleções feitas na caixa de diálogo **Alterar Configurações do Controlador** .  

| Seleção # | Se você optar por... | Acontecerá isso. |
| --- | --- | --- |
| 1. |Reinicie o controlador passivo. |Um trabalho será criado para reiniciar o controlador e você será notificado depois que o trabalho tiver sido criado com êxito. Isso iniciará a reinicialização do controlador. Você pode monitorar o processo de reinicialização acessando **Serviço > Painel de controle > Exibir logs de operação** e filtrando por parâmetros específicos do seu serviço. |
| 2. |Reinicie o controlador ativo. |Você verá o seguinte aviso: "Se você reiniciar o controlador ativo, o dispositivo realizará failover para o controlador passivo. Deseja continuar?" </br>Se você optar por continuar com essa operação, as etapas seguintes serão idênticas àquelas usadas para reiniciar o controlador passivo (confira a seleção 1). |
| 3. |Desligue o controlador passivo. |Você verá a seguinte mensagem: "Após a conclusão do desligamento, será necessário pressionar o botão de energia em seu controlador para ativá-lo. Tem certeza de que deseja desligar este controlador?" </br>Se você optar por continuar com essa operação, as etapas seguintes serão idênticas àquelas usadas para reiniciar o controlador passivo (confira a seleção 1). |
| 4. |Desligue o controlador ativo. |Você verá a seguinte mensagem: "Após a conclusão do desligamento, será necessário pressionar o botão de energia em seu controlador para ativá-lo. Tem certeza de que deseja desligar este controlador?" </br>Se você optar por continuar com essa operação, as etapas seguintes serão idênticas àquelas usadas para reiniciar o controlador passivo (confira a seleção 1). |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou desligar um controlador no Windows PowerShell para StorSimple
Execute as seguintes etapas para desligar ou reiniciar um único controlador no seu dispositivo StorSimple a partir do Portal Clássico do Azure.

1. Acesse o dispositivo por meio do console serial ou uma sessão de telnet de um computador remoto. Conecte-se ao Controlador 0 ou Controlador 1 seguindo as etapas em [Usar PuTTY para se conectar ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.
3. Na mensagem da faixa, anote o controlador ao qual você está conectado (Controlador 0 ou Controlador 1) e se ele é o controlador passivo (em espera) ou ativo.
   
   * Para desligar um único controlador no prompt, digite:
     
       `Stop-HcsController`
     
       Esse procedimento desligará o controlador ao qual você está conectado. Se você parar o controlador ativo, em seguida, ele realizará failover para o controlador passivo antes de desligar.
   * Para reiniciar um controlador, no prompt, digite:
     
       `Restart-HcsController`
     
       Isso reiniciará o controlador ao qual você está conectado. Se você reiniciar o controlador ativo, ele realizará failover para o controlador passivo antes da reinicialização.

## <a name="shut-down-a-storsimple-device"></a>Desligar um dispositivo StorSimple
Esta seção explica como desligar um dispositivo StorSimple com falha ou em execução a partir de um computador remoto. Um dispositivo é desativado após desligar ambos os controladores do dispositivo. Um desligamento de dispositivo é feito quando o dispositivo está sendo movido fisicamente ou se for retirado de serviço.

> [!IMPORTANT]
> Antes de desligar o dispositivo, verifique a integridade dos componentes do dispositivo. Navegue até **Dispositivos > Manutenção > Status de Hardware** e verifique se o LED de status de todos os componentes está verde. Somente um dispositivo íntegro terá um status em verde. Se o dispositivo está sendo desligado para substituir um componente com funcionamento incorreto, você verá um status de falha (vermelho) ou degradado (amarelo) para os respectivos componentes.
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>Para desligar um dispositivo StorSimple
1. Use o procedimento de [reiniciar ou desligar um controlador](#restart-or-shut-down-a-single-controller) para identificar e desligar o controlador passivo em seu dispositivo. Você pode executar essa operação no portal de clássico do Azure ou no Windows PowerShell para StorSimple.
2. Repita a etapa acima para desligar o controlador ativo.
3. Agora, você precisará examinar o plano de apoio do dispositivo. Depois que os dois controladores são completamente desligados, os LEDs de status em ambos os controladores devem estar piscando em vermelho. Se você precisar desativar o dispositivo completamente neste momento, coloque os interruptores de energia nos Módulos de energia e resfriamento (PCMs) na posição OFF. Isso deve desativar o dispositivo.

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Redefinir o dispositivo para as configurações padrões de fábrica
> [!IMPORTANT]
> Se você precisar redefinir o dispositivo para as configurações padrão de fábrica, entre em contato com o Suporte da Microsoft. O procedimento descrito a seguir deve ser usado apenas em conjunto com o Suporte da Microsoft.
> 
> 

Este procedimento descreve como redefinir o dispositivo Microsoft Azure StorSimple para as configurações padrão de fábrica usando o Windows PowerShell para StorSimple.
A redefinição de um dispositivo remove todos os dados e todas as configurações do cluster inteiro por padrão.

Execute as etapas a seguir para redefinir o dispositivo Microsoft Azure StorSimple para as configurações padrões de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para redefinir o dispositivo para as configurações padrões do Windows PowerShell para StorSimple
1. Acesse o dispositivo por meio do seu console serial. Verifique a mensagem do cabeçalho para garantir que você está conectado ao controlador Ativo.
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.
3. No prompt, digite o seguinte comando para redefinir todo o cluster, removendo todas as configurações de dados, metadados e controlador:
   
    `Reset-HcsFactoryDefault`
   
    Em vez disso, para redefinir um único controlador, use o cmdlet [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) com o parâmetro `-scope`.)
   
    O sistema será reiniciado várias vezes. Você será notificado quando a reinicialização for concluída com êxito. Dependendo do modelo do sistema, podem ser necessários de 45 a 60 minutos para um dispositivo 8100 e de 60 a 90 minutos para um 8600 concluir esse processo.
   
   > [!TIP]
   > * Se você estiver usando a Atualização 1.2 ou anterior, use o parâmetro `–SkipFirmwareVersionCheck` para ignorar a verificação de versão de firmware (caso contrário, você verá um erro de incompatibilidade de firmware: A restauração de fábrica não pode continuar devido a uma incompatibilidade nas versões do firmware. ).
   > * O procedimento de redefinição de fábrica pode falhar para dispositivos StorSimple que executam a Atualização 1 ou 1.1 no Portal do Governo e que executaram uma substituição de controlador único ou duplo com êxito (com os controladores de substituição que foram enviados com software anterior à Atualização 1). Isso acontece quando a imagem de redefinição de fábrica é validada para verificar a presença de um arquivo SHA1 no controlador, o qual não existe para um software anterior à Atualização 1. Se você encontrar esta falha de redefinição dos parâmetros originais, entre em contato com o Suporte da Microsoft para que ele possa auxiliar nas próximas etapas. Esse problema não é encontrado em controladores de substituição que foram enviados de fábrica com o software de Atualização 1 ou mais recente.
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre como gerenciar controladores de dispositivo
Nesta seção, resumimos algumas das perguntas frequentes sobre o gerenciamento de controladores de dispositivo StorSimple.

**P.** O que acontece se ambos os controladores em meu dispositivo estão íntegros e ativados e eu reiniciar ou desligar o controlador ativo?

**A.** Se ambos os controladores em seu dispositivo estão íntegros e ativados, será solicitada a sua confirmação. Você pode optar por:

* **Reiniciar o controlador ativo** – você será notificado de que reiniciar um controlador ativo fará com que o dispositivo faça failover para o controlador passivo. O controlador será reiniciado.
* **Desligar um controlador ativo** – você será notificado de que desligar um controlador ativo resultará em tempo de inatividade. Você também precisará pressionar o botão de energia no dispositivo para ativar o controlador.

**P.** O que acontece se o controlador passivo em meu dispositivo estiver indisponível ou desligado e eu reiniciar ou desligar o controlador ativo?

**A.** Se o controlador passivo em seu dispositivo estiver indisponível ou desligado e você optar por:

* **Reiniciar o controlador ativo** – você será notificado de que continuar a operação resultará em uma interrupção temporária do serviço e será solicitada a sua confirmação.
* **Desligar um controlador ativo** – você será notificado de que continuar a operação resultará em tempo de inatividade e que você precisa pressionar o botão de energia em um ou ambos os controladores para ligar o dispositivo. Será solicitada a sua confirmação.

**P.** Quando o desligamento ou reinicialização do controlador falha durante a execução?

**A.** Reiniciar ou desligar um controlador pode falhar se:

* Uma atualização do dispositivo está em andamento.
* Uma reinicialização de controlador já está em andamento.
* Um desligamento do controlador já está em andamento.

**P.** Como saber se um controlador foi reiniciado ou desligado?

**A.** Você pode verificar o status do controlador na página de Manutenção. O status do controlador indicará se um controlador foi reiniciado ou desligado. Além disso, a página Alertas terá um alerta informativo, avisando se o controlador foi reiniciado ou desligado. As operações de reinicialização e desligamento do controlador também são registradas nos logs de operação. Para obter mais informações sobre logs de operação, vá para [Exibir os logs de operação](storsimple-service-dashboard.md#view-the-operations-logs).

**P.** Há algum impacto sobre as E/Ss como resultado de failover do controlador?

**A.** As conexões TCP entre iniciadores e controlador ativo serão redefinidas como resultado do failover do controlador, mas serão restabelecidas quando o controlador passivo assumir a operação. Pode haver uma pausa temporária (menos de 30 segundos) na atividade de E/S entre os iniciadores e o dispositivo no decorrer desta operação.

**P.** Como posso retornar meu controlador de serviço depois de ele ter sido desligado e retirado de serviço?

**A.** Para retornar um controlador ao serviço, insira-o no chassi conforme descrito em [Substituir um módulo do controlador em seu dispositivo StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Próximas etapas
* Se você tiver algum problema com os controladores de dispositivo StorSimple que não possam ser resolvidos usando os procedimentos listados neste tutorial, [contate o Suporte da Microsoft](storsimple-contact-microsoft-support.md).
* Para saber mais sobre como usar o serviço StorSimple Manager, acesse [Usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

