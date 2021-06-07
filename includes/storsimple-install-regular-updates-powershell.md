---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67171955"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações regulares através do Windows PowerShell para StorSimple
1. Abra o console serial do dispositivo e selecione a opção 1, **Efetuar login com acesso total**. Digite a senha. A senha padrão é *Senha1*. 
2. No prompt de comando, digite:
   
     `Get-HcsUpdateAvailability`
   
    Você será notificado se houver atualizações disponíveis e se as atualizações são interruptivas ou não interruptivas.
3. No prompt de comando, digite:
   
     `Start-HcsUpdate`
   
    O processo de atualização será iniciado.

> [!IMPORTANT]
> * Este comando se aplica somente a hotfixes regulares. Executar esse comando em apenas um controlador, mas ambos os controladores serão atualizados. 
> * Você pode perceber um failover de controlador durante o processo de atualização; no entanto, o failover não afetará a disponibilidade ou operação do sistema.
> 
> 

