---
title: Altere o modo de dispositivo StorSimple | Microsoft Docs
description: Descreve os modos de dispositivo StorSimple e explica como usar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2016
ms.author: alkohli
ms.openlocfilehash: 33c65bf2eecff3914f3227c76f7d638a4507e1f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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
> **Só há suporte ao modo de Manutenção em um dispositivo que esteja funcionando corretamente. Não há suporte para esse modo em um dispositivo no qual um dos controladores ou ambos não estejam funcionando.**
> </br>
> 
> 

### <a name="recovery-mode"></a>Modo de recuperação
O modo de recuperação pode ser descrito como "Modo Seguro do Windows com suporte de rede". O modo de recuperação envolve a equipe de Suporte da Microsoft e permite que ela execute o diagnóstico do sistema. O objetivo principal do modo de recuperação é recuperar os logs do sistema.

Se o sistema entrar no modo de recuperação, você deverá contatar o Suporte da Microsoft para obter as próximas etapas. Para obter mais informações, vá para [Contatar o Suporte da Microsoft](storsimple-contact-microsoft-support.md).

> [!NOTE]
> **Você não pode colocar o dispositivo no modo de recuperação. Se o dispositivo estiver em um estado inválido, o modo de recuperação tentará colocá-lo em um estado em que a equipe de Suporte da Microsoft possa examiná-lo.**
> 
> 

## <a name="determine-storsimple-device-mode"></a>Determinar o modo do dispositivo StorSimple
#### <a name="to-determine-the-current-device-mode"></a>Para determinar o modo atual do dispositivo
1. Faça logon no console serial do dispositivo seguindo as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Examine a mensagem do cabeçalho no menu do console serial do dispositivo. A mensagem indica explicitamente se o dispositivo está no modo de manutenção ou de recuperação. Se a mensagem não contiver informações específicas sobre o modo do sistema, o dispositivo está no modo normal.

## <a name="change-the-storsimple-device-mode"></a>Alterar o modo de dispositivo StorSimple
Você pode colocar o dispositivo StorSimple no modo de manutenção (do modo normal) para realizar a manutenção ou instalar atualizações do modo de manutenção. Execute os seguintes procedimentos para entrar ou sair do modo de manutenção.

> [!IMPORTANT]
> Antes de entrar no modo de manutenção, verifique se ambos os controladores de dispositivo estão íntegros, acessando o **Status de Hardware** na página **Manutenção** no Portal clássico do Azure. Se um ou ambos os controladores não estiverem íntegros, contate o Suporte da Microsoft para as próximas etapas. Para obter mais informações, vá para [Contatar o Suporte da Microsoft](storsimple-contact-microsoft-support.md).
> 
> 

#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de manutenção
1. Faça logon no console serial do dispositivo seguindo as etapas em [Usar o PuTTY para conectar-se ao console serial do dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é: `Password1`.
3. No prompt de comando, digite 
   
    `Enter-HcsMaintenanceMode`
4. Você verá uma mensagem de aviso informando que o modo de Manutenção interromperá todas as solicitações de E/S e a conexão com o portal clássico do Azure, e você será solicitado a confirmar. Digite **Y** para entrar no modo de manutenção.
5. Ambos os controladores serão reiniciados. Quando a reinicialização estiver concluída, outra mensagem será exibida indicando que o dispositivo está em modo de manutenção.

#### <a name="to-exit-maintenance-mode"></a>Para sair do modo de manutenção
1. Faça logon no console serial do dispositivo. Verifique na mensagem do cabeçalho se o dispositivo está no modo de manutenção.
2. No prompt de comando, digite:
   
    `Exit-HcsMaintenanceMode`
3. Uma mensagem de aviso e uma mensagem de confirmação serão exibidas. Digite **Y** para sair do modo de manutenção.
4. Ambos os controladores serão reiniciados. Quando a reinicialização estiver concluída, outra mensagem será exibida indicando que o dispositivo está em modo normal.

## <a name="next-steps"></a>Próximas etapas
Saiba como [aplicar atualizações do modo normal e de manutenção](storsimple-update-device.md) no dispositivo StorSimple.

