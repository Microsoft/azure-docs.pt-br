---
title: "Modificar as configurações de DATA 0 em um dispositivo StorSimple | Microsoft Docs"
description: Saiba como usar o Windows PowerShell para StorSimple para reconfigurar a interface de rede DATA 0 em seu dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 58e3d509-f425-4a7f-b650-d496a7c85193
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 3a47ff1eed220cede820e8698c3384300e94688d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>Modificar as configurações de interface de rede DADOS 0 em seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
O dispositivo Microsoft Azure StorSimple tem seis interfaces de rede de DADOS 0 a DADOS 5. A interface de DADOS 0 é sempre configurada por meio da interface do Windows PowerShell ou do console serial e é habilitada para a nuvem automaticamente. Observe que não é possível configurar a interface de rede de DATA 0 por meio do portal clássico do Azure. 

A interface de DADOS 0 é configurada primeiramente por meio do assistente de instalação durante a implantação inicial do dispositivo StorSimple. Quando o dispositivo está em modo operacional, talvez você precise redefinir as configurações de DADOS 0. Este tutorial fornece dois métodos para modificar as configurações de rede de DATA 0; os dois por meio do Windows PowerShell para StorSimple.

Depois de ler este tutorial, você poderá:

* Modificar a configuração de rede de DADOS 0 por meio do assistente de instalação
* Modificar as configurações de rede de DATA 0 por meio do cmdlet `Set-HcsNetInterface`

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modificar as configurações de rede de DADOS 0 por meio do assistente de instalação
Você pode reconfigurar as definições de rede de DADOS 0 conectando-se à interface do Windows PowerShell de seu dispositivo StorSimple e iniciando uma sessão do assistente de instalação. Execute as etapas a seguir para modificar as configurações de DADOS 0:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Para modificar as configurações de rede de DADOS 0 por meio do assistente de instalação
1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a **senha de administrador do dispositivo**. A senha padrão é `Password1`.
2. No prompt de comando, digite:
   
    `Invoke-HcsSetupWizard`
3. Um assistente de instalação aparecerá para lhe ajudar a configurar a interface dos DADOS 0 do seu dispositivo. Fornece novos valores para o endereço IP, gateway e máscara de rede.

> [!NOTE]
> Os IPs de controladores fixos precisarão ser reconfigurados por meio da página **Configurar** do dispositivo StorSimple no Portal clássico do Azure. Para obter mais informações, vá para [Modificar interfaces de rede](storsimple-modify-device-config.md#modify-network-interfaces).
> 
> 

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modificar as configurações de rede de DADOS 0 por meio do cmdlet Set-HcsNetInterface
Uma maneira alternativa de reconfigurar o adaptador de rede DADOS 0 é através do uso do cmdlet `Set-HcsNetInterface`. O cmdlet é executado na interface do Windows PowerShell do seu dispositivo StorSimple. Ao usar esse procedimento, os IPs fixos pelo controlador também podem ser configurados aqui. Execute as etapas a seguir para modificar as configurações de DADOS 0: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Para modificar as configurações de rede de DADOS 0 por meio do cmdlet Set-HcsNetInterface
1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**. Quando solicitado, forneça a senha de administrador do dispositivo. A senha padrão é `Password1`.
2. No prompt de comando, digite:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Entre os colchetes angulares, digite os seguintes valores para DATA 0:
   
   * Endereço IPv4
   * Gateway IPv4
   * Máscara da sub-rede IPv4
   * Endereço IPv4 fixo para o controlador 0
   * Endereço IPv4 fixo para o controlador 1
     
     Para obter mais informações sobre como usar esse cmdlet, vá para a [Referência de cmdlets do Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Próximas etapas
* Para configurar as interfaces de rede que não sejam DATA 0, você pode usar a [página Configurar no Portal clássico do Azure](storsimple-modify-device-config.md). 
* Se você tiver problemas ao configurar suas interfaces de rede, consulte [Solucionar problemas de implantação](storsimple-troubleshoot-deployment.md).

