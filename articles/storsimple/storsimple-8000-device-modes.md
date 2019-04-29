---
title: Alterar o modo do dispositivo StorSimple | Microsoft Docs
description: Descreve os modos de dispositivo StorSimple e explica como usar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576081"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Alterar o modo do dispositivo StorSimple

Este artigo fornece uma breve descrição dos diversos modos em que o dispositivo StorSimple pode operar. O dispositivo StorSimple pode funcionar em três modos: normal, manutenção e recuperação.

Após ler este artigo, você conhecerá:

* O que são os modos de dispositivo StorSimple
* Como descobrir em qual modo o dispositivo StorSimple está
* Como alterar do modo normal para o modo de manutenção e *vice-versa*

As tarefas de gerenciamento acima só podem ser executadas por meio da interface do Windows PowerShell do dispositivo StorSimple.

## <a name="about-storsimple-device-modes"></a>Sobre os modos do dispositivo StorSimple

O dispositivo StorSimple pode operar nos modos normal, de manutenção ou de recuperação. Cada um desses modos é descrito resumidamente abaixo.

### <a name="normal-mode"></a>Modo Normal

É definido como o modo operacional normal para um dispositivo StorSimple totalmente configurado. Por padrão, o dispositivo deve estar no modo normal.

### <a name="maintenance-mode"></a>Modo de Manutenção

Às vezes, o dispositivo StorSimple pode precisar ser colocado no modo de manutenção. Esse modo permite realizar a manutenção no dispositivo e instalar atualizações sem interrupção, como aquelas relacionadas ao firmware do disco.

Você pode colocar o sistema no modo de manutenção somente por meio do Windows PowerShell para StorSimple. Todas as solicitações de E/S são pausadas neste modo. Serviços como NVRAM (memória de acesso aleatório não volátil) ou o serviço de cluster também são interrompidos. Ambos os controladores são reiniciados quando você entra ou sai desse modo. Quando você sair do modo de manutenção, todos os serviços continuarão e deverão estar íntegros. Isso pode levar alguns minutos.

> [!NOTE]
> **Só há suporte ao modo de Manutenção em um dispositivo que esteja funcionando corretamente. Não há suporte para esse modo em um dispositivo no qual um ou ambos os controladores não estejam funcionando.**


### <a name="recovery-mode"></a>Modo de recuperação

O modo de recuperação pode ser descrito como "Modo Seguro do Windows com suporte de rede". O modo de recuperação envolve a equipe de Suporte da Microsoft e permite que ela execute o diagnóstico do sistema. O objetivo principal do modo de recuperação é recuperar os logs do sistema.

Se o sistema entrar no modo de recuperação, você deverá contatar o Suporte da Microsoft para obter as próximas etapas. Para obter mais informações, vá para [Contatar o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Você não pode colocar o dispositivo no modo de recuperação. Se o dispositivo estiver em um estado inválido, o modo de recuperação tentará colocá-lo em um estado em que a equipe de Suporte da Microsoft possa examiná-lo.**

## <a name="determine-storsimple-device-mode"></a>Determinar o modo do dispositivo StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo atual do dispositivo

1. Faça logon no console serial do dispositivo seguindo as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Examine a mensagem do cabeçalho no menu do console serial do dispositivo. A mensagem indica explicitamente se o dispositivo está no modo de manutenção ou de recuperação. Se a mensagem não contiver informações específicas sobre o modo do sistema, o dispositivo está no modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo StorSimple

Você pode colocar o dispositivo StorSimple no modo de manutenção (do modo normal) para realizar a manutenção ou instalar atualizações do modo de manutenção. Execute os seguintes procedimentos para entrar ou sair do modo de manutenção.

> [!IMPORTANT]
> Antes de entrar no modo de manutenção, verifique se ambos os controladores de dispositivo estão íntegros acessando **Configurações do dispositivo > Integridade do hardware** do dispositivo no Portal do Azure. Se um ou ambos os controladores não estiverem íntegros, contate o Suporte da Microsoft para as próximas etapas. Para obter mais informações, vá para [Contatar o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção

1. Faça logon no console serial do dispositivo seguindo as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é: `Password1`.
3. No prompt de comando, digite 
   
    `Enter-HcsMaintenanceMode`
4. Você verá uma mensagem de aviso informando que o modo de manutenção interromperá todas as solicitações de E/S e a conexão com o Portal do Azure e sua confirmação será solicitada. Digite **Y** para entrar no modo de manutenção.
5. Ambos os controladores serão reiniciados. Quando a reinicialização for concluída, outra faixa do console serial será exibida indicando que o dispositivo está em modo de manutenção. Um exemplo de saída é mostrado abaixo.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Para sair do modo de manutenção

1. Faça logon no console serial do dispositivo. Verifique na mensagem do cabeçalho se o dispositivo está no modo de manutenção.
2. No prompt de comando, digite:
   
    `Exit-HcsMaintenanceMode`
3. Uma mensagem de aviso e uma mensagem de confirmação serão exibidas. Digite **Y** para sair do modo de manutenção.
4. Ambos os controladores serão reiniciados. Quando a reinicialização for concluída, a faixa do console serial indicará que o dispositivo está no modo normal. Um exemplo de saída é mostrado abaixo.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [aplicar atualizações do modo normal e de manutenção](storsimple-update-device.md) no dispositivo StorSimple.

