---
title: "Ativar e desativar o dispositivo StorSimple série 8000 | Microsoft Docs"
description: "Explica como ativar um novo dispositivo StorSimple, ativar um dispositivo que foi desligado ou teve a energia interrompida e desativar um dispositivo em execução."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0577c837e0c47ba37a4f586603b0f5b951f1b549
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Ativar ou desativar seu dispositivo StorSimple série 8000
## <a name="overview"></a>Visão geral
Desligar um dispositivo Microsoft Azure StorSimple não é necessário como parte da operação normal do sistema. No entanto, talvez seja necessário ativar um novo dispositivo ou um dispositivo que tenha sido desligado. Em geral, um desligamento é necessário em casos em que você deve substituir o hardware com falha, mover uma unidade fisicamente ou retirar um dispositivo de serviço. Este tutorial descreve o procedimento necessário para ligar e desligar o dispositivo StorSimple em cenários diferentes.

## <a name="turn-on-a-new-device"></a>Ativar um novo dispositivo
As etapas para ativar um dispositivo StorSimple pela primeira vez são diferentes, dependendo se o dispositivo é um modelo 8100 ou 8600. O 8100 tem um único compartimento primário, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento primário e um compartimento EBOD. As etapas detalhadas para os dois modelos são abordadas nas seções a seguir.

* [Novo dispositivo com apenas o compartimento primário](#new-device-with-primary-enclosure-only)
* [Novo dispositivo com o compartimento EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Novo dispositivo com apenas o compartimento primário
O modelo StorSimple 8100 é um dispositivo de compartimento único. Seu dispositivo inclui PCMs (Módulos de Energia e Refrigeração) redundantes. Ambos os PCMs devem ser instalados e conectados a diferentes fontes de energia para garantia de alta disponibilidade.

Execute as etapas a seguir para cabear o dispositivo para recebimento de energia.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Para obter instruções completas de cabeamento e configuração do dispositivo, acesse [Instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md). Certifique-se de seguir as instruções exatamente.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Novo dispositivo com o compartimento EBOD
O modelo StorSimple 8600 tem um compartimento primário e um compartimento EBOD. Isso requer que as unidades sejam conectadas juntas para a conectividade e energia do SAS (Serial Attached SCSI).

Ao configurar este dispositivo pela primeira vez, execute as etapas para o cabeamento SAS primeiro e, em seguida, conclua as etapas do cabeamento de energia.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Para obter instruções completas de cabeamento e configuração do dispositivo, acesse [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md). Certifique-se de seguir as instruções exatamente.

## <a name="turn-on-a-device-after-shutdown"></a>Ativar um dispositivo após o desligamento
As etapas para ativar um dispositivo StorSimple após seu desligamento são diferentes, dependendo se o dispositivo é um modelo 8100 ou 8600. O 8100 tem um único compartimento primário, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento primário e um compartimento EBOD.

* [Dispositivo com apenas o compartimento primário](#device-with-primary-enclosure-only)
* [Dispositivo com o compartimento EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Dispositivo com apenas o compartimento primário
Após um desligamento, use o procedimento a seguir para ativar um dispositivo StorSimple com um compartimento primário e sem compartimento EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Para ativar um dispositivo com apenas um compartimento primário
1. Certifique-se de que os interruptores de energia nos PCMs (Módulos de Energia e Refrigeração) estão na posição DESLIGADO. Se os interruptores não estiverem na posição DESLIGADO, mude-os para a posição DESLIGADO e aguarde as luzes apagarem.
2. Ative o dispositivo invertendo os interruptores de energia em ambos os PCMs para a posição LIGADO. O dispositivo deve ser ativado.
3. Examine o seguinte para verificar se o dispositivo está totalmente ligado:
   
   1. Os LEDs OK em ambos os módulos PCM estão verdes.
   2. Os LEDs de status em ambos os controladores estão verdes sólidos.
   3. O LED azul em um dos controladores de está piscando, o que indica que o controlador está ativo.
      
      Se alguma dessas condições não for atendida, o dispositivo não está íntegro. [Entre em contato com o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Dispositivo com o compartimento EBOD
Após um desligamento, use o procedimento a seguir para ativar um dispositivo StorSimple com um compartimento primário e um compartimento EBOD. Execute cada etapa na sequência exatamente conforme descrito. Não fazer isso pode resultar em perda de dados.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Para ativar um dispositivo com um compartimento primário e um EBOD
1. Certifique-se de que o compartimento EBOD está conectado ao compartimento primário. Para obter mais informações, consulte [Instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Certifique-se de que os PCMs (Módulos de Energia e Refrigeração) nos compartimentos primário e EBOD estão na posição DESLIGADO. Se os interruptores não estiverem na posição DESLIGADO, mude-os para a posição DESLIGADO e aguarde as luzes apagarem.
3. Ative o compartimento EBOD primeiro invertendo os interruptores de energia em ambos os PCMs para a posição LIGADO. Os LEDs do PCM devem estar verdes. Um LED do controlador EBOD verde nesta unidade indica que o compartimento EBOD está ligado.
4. Ative o compartimento primário invertendo os interruptores de energia em ambos os PCMs para a posição LIGADO. Agora todo o sistema deve estar ligado.
5. Verifique se os LEDs do SAS estão verdes, o que garante que a conexão entre o compartimento EBOD e o compartimento primário é boa.

## <a name="turn-on-a-device-after-a-power-loss"></a>Ativar um dispositivo após uma queda de energia
Uma interrupção de energia pode desligar um dispositivo StorSimple. A queda de energia pode ocorrer em uma das fontes de alimentação ou nas duas. As etapas de recuperação são diferentes dependendo se o dispositivo é um modelo 8100 ou um 8600. O 8100 tem um único compartimento primário, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento primário e um compartimento EBOD. Esta seção descreve o procedimento de recuperação para cada cenário.

* [Dispositivo com apenas o compartimento primário](#8100)
* [Dispositivo com o compartimento EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Dispositivo com apenas o compartimento primário <a name="8100">
O sistema pode continuar sua operação normal se houver perda de energia para uma de suas fontes de alimentação. No entanto, para garantir a alta disponibilidade do dispositivo, restaure a energia para a fonte de alimentação assim que possível.

Se houver uma interrupção ou queda de energia em ambas as fontes de alimentação, o sistema será desligado de forma controlada e ordenada. Quando a energia for restaurada, o sistema será automaticamente ligado.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Dispositivo com o compartimento EBOD <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Perda de energia em uma fonte de alimentação
O sistema pode continuar sua operação normal se houver perda de energia para uma de suas fontes de alimentação no compartimento primário ou no EBOD. No entanto, para garantir a alta disponibilidade do dispositivo, restaure a energia para a fonte de alimentação assim que possível.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Perda de energia em ambas as fontes de alimentação nos compartimentos primário e EBOD
Se houver uma interrupção ou queda de energia em ambas as fontes de alimentação, o compartimento EBOD será desligado imediatamente e o compartimento primário será desligado de forma controlada e ordenada. Quando a energia for restaurada, o dispositivo será iniciado automaticamente.

Se a energia for desligada manualmente, siga estas etapas para restaurar a energia para o sistema.

1. Ative o compartimento EBOD.
2. Após o compartimento EBOD ser ativado, ative o compartimento primário.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Perda de energia em ambas as fontes de alimentação no compartimento EBOD
Ao configurar os cabos, você deve garantir que o EBOD nunca esteja conectado sozinho a uma PDU separada. Se o compartimento primário e o EBOD falharem ao mesmo tempo, o sistema vai se recuperar.

Se apenas o compartimento EBOD falhar em ambas as fontes de alimentação, o sistema não vai se recuperar automaticamente. Execute as etapas a seguir para ativar o sistema e restaurá-lo em um estado íntegro:

1. Se o compartimento primário for ativado, desative os dois PCMs (Módulos de Energia e Refrigeração).
2. Aguarde alguns minutos para que o sistema seja desligado.
3. Ative o compartimento EBOD.
4. Após o compartimento EBOD ser ativado, ative o compartimento primário.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Ativar um dispositivo após a conexão do compartimento primário e EBOD ser perdida
Se a conexão for perdida entre o controlador em espera e o controlador EBOD correspondente, o dispositivo continuará a funcionar. Se a conexão entre o controlador ativo do sistema e o controlador EBOD correspondente for perdida, o failover deverá ocorrer e o dispositivo deverá continuar a funcionar normalmente.

Quando os cabos SAS (Serial Attached SCSI) forem removidos ou a conexão entre o compartimento EBOD e o compartimento primário for interrompida, o dispositivo vai parar de funcionar. Nesse ponto, execute as etapas a seguir.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Para ativar o dispositivo após a conexão ser perdida
1. Acesse a parte traseira do dispositivo.
2. Se a conexão do cabo SAS entre o compartimento EBOD e o compartimento primário for interrompida, todos os LEDs da pista do SAS no compartimento EBOD serão desligados.
3. Desligue os dois PCMs (Módulos de Energia e Refrigeração) no compartimento EBOD e no primário.
4. Aguarde até que todas as luzes na parte de trás de ambos os compartimentos desliguem.
5. Reinsira os cabos SAS e certifique-se de que haja uma boa conexão entre o compartimento primário e o compartimento EBOD.
6. Ative o compartimento EBOD primeiro invertendo os dois interruptores do PCM para a posição LIGADO.
7. Certifique-se de que o compartimento EBOD esteja ligado verificando se o LED verde está LIGADO.
8. Ligue o compartimento primário.
9. Certifique-se de que o compartimento primário esteja ligado verificando se o LED verde do controlador está LIGADO.
10. Verifique se a conexão do compartimento EBOD com o compartimento primário é boa verificando se os LEDs da pista do SAS (quatro por controlador EBOD) estão todos LIGADOS.

> [!IMPORTANT]
> Se os cabos SAS estiverem com defeito ou a conexão entre o compartimento EBOD e o compartimento primário não for boa, quando você ligar o sistema, ele entrará no modo de recuperação. [Entre em contato com o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) se isso ocorrer.


## <a name="turn-off-a-running-device"></a>Desativar um dispositivo em execução
Um dispositivo StorSimple em execução poderá precisar ser desligado se estiver sendo movido, retirado de serviço ou contiver um componente defeituoso que precisa ser substituído. As etapas são diferentes, dependendo se o dispositivo StorSimple é um modelo 8100 ou um 8600. O 8100 tem um único compartimento primário, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento primário e um compartimento EBOD. Esta seção detalha as etapas para desligar um dispositivo em execução.

* [Dispositivo com o compartimento primário](#8100a)
* [Dispositivo com o compartimento EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Dispositivo com o compartimento primário <a name="8100a">
Você pode desligar o dispositivo de forma ordenada e controlada por meio do Portal Clássico do Azure ou do Windows PowerShell para o StorSimple. 

> [!IMPORTANT]
> Não desligue um dispositivo em execução usando o botão de energia na parte posterior do dispositivo.
> 
> Antes de desligar o dispositivo, certifique-se de que todos os componentes de dispositivo estejam íntegros. No Portal Clássico do Azure, navegue até **Dispositivos** > **Manutenção** > **Status de Hardware** e verifique se o status de todos os componentes está verde. Isso se aplica apenas a um sistema íntegro. Se o sistema estiver sendo desligado para substituir um componente com funcionamento incorreto, você verá um status de falha (vermelho) ou degradado (amarelo) para o respectivo componente no **Status de Hardware**.
> 
> 

Depois de acessar o Windows PowerShell para o StorSimple ou o Portal Clássico do Azure, siga as etapas em [Desligar um dispositivo StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Dispositivo com o compartimento EBOD <a name="8600a">
> [!IMPORTANT]
> Antes de desligar o compartimento primário e o compartimento EBOD, verifique se todos os componentes do dispositivo estão íntegros. No portal do Azure, navegue até **Dispositivos** > **Monitor** > **Integridade do hardware**e verifique se todos os componentes estão íntegros.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Para desligar um dispositivo em execução com o compartimento EBOD
1. Siga as etapas listadas em [Desligar um dispositivo StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) para o compartimento principal.
2. Depois que o compartimento primário for desligado, desligue o EBOD invertendo os dois interruptores PCM (Módulo de Energia e Refrigeração).
3. Para verificar se o EBOD foi desligado, verifique se todas as luzes na parte traseira do compartimento EBOD estão desligadas.

> [!NOTE]
> Os cabos SAS que são usados para conectar o compartimento EBOD ao compartimento primário não devem ser removidos até depois de o sistema ser desligado.

## <a name="next-steps"></a>Próximas etapas
[Contate o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) se você encontrar problemas ao ativar ou desligar um dispositivo StorSimple.

