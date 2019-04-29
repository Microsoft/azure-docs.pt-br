---
title: Atualizar o dispositivo StorSimple | Microsoft Docs
description: Explica como usar o recurso de atualização do StorSimple para instalar hotfixes e atualizações regulares e no modo de manutenção.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: d973a16c121a1e8ebee10826d135bcbb33ef748c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409893"
---
# <a name="update-your-storsimple-8000-series-device"></a>Atualizar seu dispositivo do StorSimple série 8000
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os Gerenciadores de Dispositivos do StorSimple migrarão automaticamente para o novo portal do Azure, seguindo o agendamento definido para preteri-los. Você receberá um email e uma notificação do portal para essa mudança. Este documento também será desativado em breve. Para dúvidas sobre a migração, consulte [perguntas Frequentes: Mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Visão geral
Os recursos de atualização do StorSimple permitem que você mantenha seu dispositivo StorSimple atualizado com facilidade. Dependendo do tipo de atualização, você poderá aplicar as atualizações ao dispositivo por meio do Portal Clássico do Azure ou por meio da interface do Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Você pode aplicar dois tipos de atualização de dispositivo: 

* Atualizações regulares (ou modo Normal)
* Atualizações no modo de Manutenção

Você pode instalar atualizações regulares por meio do Portal Clássico do Azure ou do Windows PowerShell; no entanto, você deve usar o Windows PowerShell para instalar atualizações do modo de Manutenção. 

Cada tipo de atualização será descrito separadamente a seguir.

### <a name="regular-updates"></a>Atualizações regulares
As atualizações regulares são atualizações sem interrupções que podem ser instaladas quando o dispositivo estiver em modo Normal. Essas atualizações são aplicadas por meio do site Microsoft Update para cada controlador de dispositivo. 

> [!IMPORTANT]
> Um failover de controlador pode ocorrer durante o processo de atualização. No entanto, isso não afetará a disponibilidade do sistema ou a operação.
> 
> 

* Para obter detalhes sobre como instalar atualizações regulares por meio do portal clássico o Azure, consulte [Instalar atualizações regulares por meio do portal clássico do Azure](#install-regular-updates-via-the-azure-classic-portal).
* Você também pode instalar atualizações regulares por meio do Windows PowerShell para StorSimple. Para obter detalhes, consulte [Instalar atualizações regulares por meio do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Atualizações no modo de Manutenção
As atualizações do Modo de Manutenção são atualizações com interrupção, como atualizações de firmware de disco. Essas atualizações exigem que o dispositivo seja colocado em modo de Manutenção. Para obter detalhes, consulte [etapa 2: Entrar no modo de manutenção](#step2). Você não pode usar o Portal clássico do Azure para instalar atualizações do modo de Manutenção. Em vez disso, você deverá usar o Windows PowerShell para StorSimple. 

Para obter detalhes sobre como instalar atualizações do modo de Manutenção, consulte [Instalar atualizações do modo de Manutenção instalar por meio do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> As atualizações do modo de Manutenção devem ser aplicadas separadamente para cada controlador. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalar atualizações regulares por meio do Portal clássico do Azure
Você pode usar o Portal clássico do Azure para aplicar atualizações ao dispositivo StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações regulares por meio do Windows PowerShell para StorSimple
Como alternativa, você pode usar o Windows PowerShell para StorSimple para aplicar atualizações regulares (modo Normal).

> [!IMPORTANT]
> Embora seja possível instalar atualizações regulares usando o Windows PowerShell para StorSimple, é altamente recomendável que você instale atualizações regulares pelo Portal clássico do Azure. Começando da Atualização 1, serão executadas verificações prévias antes da instalação de atualizações pelo portal. Essas verificações evitarão falhas e garantirão uma experiência mais uniforme. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Instalar atualizações do modo de Manutenção instalar por meio do Windows PowerShell para StorSimple
Você pode usar o Windows PowerShell para StorSimple para aplicar atualizações do modo de Manutenção ao dispositivo StorSimple. Todas as solicitações de E/S são pausadas neste modo. Serviços como NVRAM (memória de acesso aleatório não volátil) ou o serviço de cluster também são interrompidos. Ambos os controladores são reiniciados quando você entra ou sai desse modo. Quando você sair desse modo, todos os serviços serão retomados e deverão estar íntegros. (Isso pode levar alguns minutos).

Se você precisar aplicar atualizações do modo de Manutenção, receberá um alerta por meio do Portal clássico do Azure indicando que há atualizações que devem ser instaladas. Esse alerta incluirá instruções para o uso do Windows PowerShell para StorSimple para instalar as atualizações. Depois de atualizar o dispositivo, use o mesmo procedimento para alterar o dispositivo para o modo Normal. Para obter instruções passo a passo, consulte [etapa 4: Sair do modo de manutenção](#step4).

> [!IMPORTANT]
> * Antes de entrar no modo de Manutenção, verifique se ambos os controladores de dispositivo estão íntegros, verificando o **Status de Hardware** na página **Manutenção** no Portal clássico do Azure. Se o controlador não estiver íntegro, contate o Suporte da Microsoft para as próximas etapas. Para saber mais, acesse Contatar Suporte da Microsoft. 
> * Quando você estiver no modo de Manutenção, precisará aplicar a atualização primeiro em um controlador e então no outro controlador.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Etapa 1: Conectar-se ao console serial <a name="step1">
Primeiro, use um aplicativo como o PuTTY para acessar o console serial. O procedimento a seguir explica como usar o PuTTY para se conectar ao console serial.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Etapa 2: Entrar no modo de manutenção <a name="step2">
Depois de se conectar ao console, determine se há atualizações a serem instaladas e entre no modo de manutenção para instalá-las.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Etapa 3: Instalar as atualizações <a name="step3">
Em seguida, instale as atualizações.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Etapa 4: Sair do modo de manutenção <a name="step4">
Por fim, saia do modo de Manutenção.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instale correções por meio do Windows PowerShell para StorSimple
Ao contrário de atualizações para o Microsoft Azure StorSimple, os hotfixes são instalados de uma pasta compartilhada. Assim como acontece com as atualizações, há dois tipos de hotfixes: 

* Hotfixes regulares 
* Hotfixes no modo de Manutenção  

Os procedimentos a seguir explicam como usar o Windows PowerShell para StorSimple para instalar hotfixes regulares e no modo de Manutenção.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>O que acontece com as atualizações se você executar uma redefinição de fábrica do dispositivo?
Se um dispositivo for redefinido para as configurações de fábrica, então todas as atualizações serão perdidas. Depois que o dispositivo com a redefinição de fábrica for registrado e configurado, você precisará instalar manualmente as atualizações por meio do Portal clássico do Azure e/ou do Windows PowerShell para StorSimple. Para saber mais sobre as redefinições de fábrica, consulte [Redefinir o dispositivo para as configurações padrão de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [como usar o Windows PowerShell para StorSimple a fim de administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

