---
title: "Gerenciar os controladores de dispositivo do StorSimple da série 8000 | Microsoft Docs"
description: Saiba como parar, reiniciar, desligar ou redefinir os controladores de dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerenciar controladores de dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este tutorial descreve as diferentes operações que podem ser executadas em seus controladores de dispositivo StorSimple. Os controladores em seu dispositivo StorSimple são controladores redundantes (par) em uma configuração ativo-passivo. Em um determinado momento, somente um controlador está ativo e está processando todas as operações de disco e rede. O outro controlador está em um modo passivo. Se o controlador ativo falhar, o controlador passivo ficará ativo automaticamente.

Este tutorial inclui instruções passo a passo para gerenciar os controladores de dispositivo usando a:

* A folha **Controladores** do dispositivo no serviço do Gerenciador de Dispositivos do StorSimple.
* Windows PowerShell para StorSimple.

É recomendável gerenciar os controladores de dispositivo por meio do serviço do Gerenciador de Dispositivos do StorSimple. Se só for possível executar uma ação usando o Windows PowerShell para StorSimple, o tutorial fará uma observação sobre isso.

Depois de ler este tutorial, você poderá:

* Reiniciar ou desligar um controlador de dispositivo StorSimple
* Desligar um dispositivo StorSimple
* Redefinir o dispositivo StorSimple para os padrões de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou desligar um único controlador
Uma reinicialização ou um desligamento de controlador não é necessário como parte da operação normal do sistema. As operações de desligamento para um controlador de dispositivo único são comuns apenas em casos em que um componente de hardware de dispositivo com falha requer substituição. Uma reinicialização de controlador também pode ser necessária em uma situação em que o desempenho é afetado pelo uso excessivo de memória ou controlador com defeito. Se você deseja ativar e testar o controlador substituído também convém reiniciar um controlador após a sua substituição com êxito.

Reiniciar um dispositivo que não é interrompido para os iniciadores conectados, supondo que o controlador passivo está disponível. Se um controlador passivo não está disponível ou está desligado, reiniciar o controlador ativo pode resultar na interrupção do serviço e em tempo de inatividade.

> [!IMPORTANT]
> * **Um controlador em execução nunca deve ser fisicamente removido, pois isso resultaria em uma perda de redundância e maior risco de tempo de inatividade.**
> * O procedimento a seguir aplica-se somente ao dispositivo físico StorSimple. Para saber mais sobre como iniciar, parar e reiniciar o Dispositivo de Nuvem StorSimple, consulte [Trabalhar com o dispositivo de nuvem](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Você pode reiniciar ou desligar um único controlador de dispositivo usando o Portal do Azure do serviço do Gerenciador de Dispositivos do StorSimple ou o Windows PowerShell para StorSimple.

Para gerenciar os controladores de dispositivo no Portal do Azure, execute as seguintes etapas.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Para reiniciar ou desligar um controlador no Portal do Azure
1. Em seu serviço do Gerenciador de Dispositivos do StorSimple, acesse **Dispositivos**. Selecione seu dispositivo na lista de dispositivos. 

    ![Escolha um dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Acesse **Configurações > Controladores**.
   
    ![Verificar a integridade dos controladores do dispositivo StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Na folha **Controladores**, verifique se o status de ambos os controladores em seu dispositivo está **Íntegro**. Selecione um controlador, clique com botão direito do mouse e escolha **Reiniciar** ou **Desligar**.

    ![Selecione reiniciar ou desligar controladores do dispositivo StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Um trabalho é criado para reiniciar ou desligar o controlador e você verá os avisos aplicáveis, caso existam. Para monitorar a reinicialização ou desligamento, acesse **Serviço > Logs de atividade** e filtre por parâmetros específicos do seu serviço. Se um controlador tiver sido desligado, será necessário pressionar o botão de energia para ligá-lo.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou desligar um controlador no Windows PowerShell para StorSimple
Execute as seguintes etapas para desligar ou reiniciar um único controlador no seu dispositivo StorSimple pelo Windows PowerShell para StorSimple.

1. Acesse o dispositivo por meio do console serial ou uma sessão de telnet de um computador remoto. Para conectar-se ao Controlador 0 ou ao Controlador 1, siga as etapas em [Usar PuTTY para se conectar ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.
3. Na mensagem da faixa, anote o controlador ao qual você está conectado (Controlador 0 ou Controlador 1) e se ele é o controlador passivo (em espera) ou ativo.
   
   * Para desligar um único controlador no prompt, digite:
     
       `Stop-HcsController`
     
       Esse procedimento desligará o controlador ao qual você está conectado. Se você parar o controlador ativo, o dispositivo fará failover para o controlador passivo.

   * Para reiniciar um controlador, no prompt, digite:
     
       `Restart-HcsController`
     
       Isso reiniciará o controlador ao qual você está conectado. Se você reiniciar o controlador ativo, ele fará failover para o controlador passivo antes da reinicialização.

## <a name="shut-down-a-storsimple-device"></a>Desligar um dispositivo StorSimple

Esta seção explica como desligar um dispositivo StorSimple com falha ou em execução a partir de um computador remoto. Um dispositivo é desativado após ambos os controladores do dispositivo serem desligados. Um desligamento de dispositivo ocorre quando o dispositivo é movido fisicamente ou se ele for retirado do serviço.

> [!IMPORTANT]
> Antes de desligar o dispositivo, verifique a integridade dos componentes do dispositivo. Navegue até seu dispositivo e clique em **Configurações > Integridade do hardware**. Na folha **Status e integridade do hardware**, verifique se o status do LED de todos os componentes está verde. Apenas dispositivos íntegros mostram status verde. Se o dispositivo está sendo desligado para substituir um componente com funcionamento incorreto, você verá um status de falha (vermelho) ou degradado (amarelo) para os respectivos componentes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para desligar um dispositivo StorSimple

1. Use o procedimento de [reiniciar ou desligar um controlador](#restart-or-shut-down-a-single-controller) para identificar e desligar o controlador passivo em seu dispositivo. Você pode executar essa operação no Portal do Azure ou no Windows PowerShell para StorSimple.
2. Repita a etapa acima para desligar o controlador ativo.
3. Agora, você precisará examinar o painel traseiro do dispositivo. Depois que os dois controladores são completamente desligados, os LEDs de status em ambos os controladores devem estar piscando em vermelho. Se você precisar desativar o dispositivo completamente neste momento, coloque os interruptores de energia nos Módulos de energia e resfriamento (PCMs) na posição OFF. Isso deve desativar o dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Redefinir o dispositivo para as configurações padrões de fábrica

> [!IMPORTANT]
> Se você precisar redefinir o dispositivo para as configurações padrão de fábrica, entre em contato com o Suporte da Microsoft. O procedimento descrito a seguir deve ser usado apenas em conjunto com o Suporte da Microsoft.

Este procedimento descreve como redefinir o dispositivo Microsoft Azure StorSimple para as configurações padrão de fábrica usando o Windows PowerShell para StorSimple.
A redefinição de um dispositivo remove todos os dados e todas as configurações do cluster inteiro por padrão.

Execute as etapas a seguir para redefinir o dispositivo Microsoft Azure StorSimple para as configurações padrões de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para redefinir o dispositivo para as configurações padrões do Windows PowerShell para StorSimple
1. Acesse o dispositivo por meio do seu console serial. Verifique a mensagem da faixa para garantir que você está conectado ao controlador **Ativo**.
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.
3. No prompt, digite o seguinte comando para redefinir todo o cluster, removendo todas as configurações de dados, metadados e controlador:
   
    `Reset-HcsFactoryDefault`
   
    Em vez disso, para redefinir um único controlador, use o cmdlet [Reset-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) com o parâmetro `-scope`.)
   
    O sistema será reiniciado várias vezes. Você será notificado quando a reinicialização for concluída com êxito. Dependendo do modelo do sistema, podem ser necessários de 45 a 60 minutos para um dispositivo 8100 e de 60 a 90 minutos para um 8600 concluir esse processo.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre como gerenciar controladores de dispositivo
Nesta seção, resumimos algumas das perguntas frequentes sobre o gerenciamento de controladores de dispositivo StorSimple.

**P.** O que acontece se ambos os controladores em meu dispositivo estão íntegros e ativados e eu reiniciar ou desligar o controlador ativo?

**A.** Se ambos os controladores em seu dispositivo estiverem íntegros e ativados, sua confirmação será solicitada. Você pode optar por:

* **Reiniciar o controlador ativo** – Você será notificado de que reiniciar um controlador ativo fará com que o dispositivo faça failover para o controlador passivo. O controlador é reiniciado.
* **Desligar um controlador ativo** – Você será notificado de que desligar um controlador ativo resultará em tempo de inatividade. Você também precisará pressionar o botão de energia no dispositivo para ligar o controlador.

**P.** O que acontece se o controlador passivo em meu dispositivo estiver indisponível ou desligado e eu reiniciar ou desligar o controlador ativo?

**A.** Se o controlador passivo em seu dispositivo estiver indisponível ou desligado e você optar por:

* **Reiniciar o controlador ativo** – Você será notificado de que continuar a operação resultará em uma interrupção temporária do serviço e sua confirmação será solicitada.
* **Desligar um controlador ativo** – Você será notificado de que prosseguir com essa operação resultará em tempo de inatividade. Você também precisará pressionar o botão de energia em um ou ambos os controladores para ligar o dispositivo. Você recebe uma solicitação para fornecer sua confirmação.

**P.** Quando o desligamento ou reinicialização do controlador falha durante a execução?

**A.** Reiniciar ou desligar um controlador pode falhar se:

* Uma atualização do dispositivo está em andamento.
* Uma reinicialização de controlador já está em andamento.
* Um desligamento do controlador já está em andamento.

**P.** Como saber se um controlador foi reiniciado ou desligado?

**A.** Você pode verificar o status do controlador na folha Controlador. O status do controlador indicará se um controlador está em processo de reinicialização ou desligamento. Além disso, a folha **Alertas** conterá um alerta informativo indicando se o controlador foi reiniciado ou desligado. As operações de reinicialização e desligamento do controlador também são registradas nos logs de atividade. Para obter mais informações sobre os logs de atividade, acesse [Exibir os logs de atividade](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**P.** Há algum impacto sobre as E/Ss decorrente do failover do controlador?

**A.** As conexões TCP entre iniciadores e controlador ativo serão redefinidas como resultado do failover do controlador, mas serão restabelecidas quando o controlador passivo assumir a operação. Pode haver uma pausa temporária (menos de 30 segundos) na atividade de E/S entre os iniciadores e o dispositivo no decorrer desta operação.

**P.** Como posso retornar meu controlador de serviço depois de ele ter sido desligado e retirado de serviço?

**A.** Para retornar um controlador ao serviço, insira-o no chassi conforme descrito em [Substituir um módulo do controlador em seu dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Próximas etapas
* Se você tiver algum problema com os controladores de dispositivo StorSimple que não possam ser resolvidos usando os procedimentos listados neste tutorial, [contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre como usar o serviço do Gerenciador de Dispositivos do StorSimple, acesse [Usar o serviço do Gerenciador de Dispositivos do StorSimple para administrar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

