---
title: Substituir um PCM em seu dispositivo StorSimple série 8000 | Microsoft Docs
description: Explica como remover e substituir módulo de energia e resfriamento (PCM) em seu dispositivo StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632281"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir um módulo de energia e resfriamento em seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
O módulo de energia e resfriamento (PCM) em seu dispositivo Microsoft Azure StorSimple consiste em uma fonte de alimentação e ventiladores que são controlados por meio de compartimentos primário e EBOD. Há apenas um modelo de PCM que é certificado para cada compartimento. O compartimento primário é certificado para um PCM de 764 W e o compartimento EBOD é certificado para um PCM de 580 W. Embora os PCMs do compartimento primário e do compartimento EBOD sejam diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

* Remover um PCM
* Instalar um PCM de reposição

> [!IMPORTANT]
> Antes de remover e substituir um PCM, examine as informações de segurança em [Substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Antes de substituir um PCM
Esteja ciente das seguintes questões importantes antes de substituir o PCM:

* Se a fonte de alimentação do PCM falhar, deixe o módulo com defeito instalado, mas remova o cabo de alimentação. O ventilador continuará a receber energia do compartimento e continuará a fornecer resfriamento adequado. Se o ventilador falhar, o PCM precisa ser trocado imediatamente.
* Antes de remover o PCM, desconecte o PCM desativando o interruptor principal (se houver) ou removendo fisicamente o cabo de alimentação. Isso avisa o sistema que um desligamento da energia é iminente.
* Certifique-se de que outros PCM estejam funcionando para que o sistema continue em operação antes de substituir o PCM defeituoso. Um PCM defeituoso deve ser substituído por um PCM totalmente operacional assim que possível.
* A substituição do módulo PCM leva apenas alguns minutos para ser concluída, mas precisa ser concluída em até 10 minutos após remover o PCM com falha para evitar superaquecimento.
* Observe que os módulos de substituição PCM de 764 W enviados da fábrica não contêm o módulo de bateria de backup. Você precisará remover a bateria do seu PCM com falha e, então, inseri-la no módulo de substituição antes de executar a substituição. Para obter mais informações, confira como [remover e inserir um módulo de bateria de backup](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Remover um PCM
Siga estas instruções quando estiver pronto para remover um módulo de energia e resfriamento (PCM) do dispositivo Microsoft Azure StorSimple.

> [!NOTE]
> Antes de remover o PCM, verifique se você tem uma peça de reposição correta (764 W para o compartimento primário ou 580 W para o compartimento EBOD).

#### <a name="to-remove-a-pcm"></a>Para remover um PCM
1. No Portal Clássico do Azure, clique em **Configurações > Monitor > Integridade do hardware**. Verifique o status dos componentes do PCM em **Componentes compartilhados** para identificar qual PCM falhou:
   
   * Se uma fonte de alimentação no PCM 0 tiver falhado, o status da **Fonte de Alimentação no PCM 0** ficará vermelho.
   * Se uma fonte de alimentação no PCM 1 tiver falhado, o status da **Fonte de Alimentação no PCM 1** ficará vermelho.
   * Se houve falha no ventilador do PCM 1, o status do **Resfriamento 0 do PCM 0** ou do **Resfriamento 1 do PCM 0** ficará vermelho.
2. Localize o PCM com falha na parte traseira do compartimento primário. Se você estiver executando um modelo 8600, identifique o compartimento primário examinando o número de identificação da unidade do sistema no display de LED do painel frontal. A ID de unidade padrão exibida no compartimento primário é **00**, enquanto que a ID de unidade padrão exibida no compartimento EBOD é **01**. O diagrama e a tabela a seguir explicam o painel frontal do display de LED.
   
    ![ID do sistema na no painel de operações frontal](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figura 1** Parte frontal do dispositivo  
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |Botão silenciar |
   | 2 |Energia do sistema |
   | 3 |Falha do módulo |
   | 4 |Falha lógica |
   | 5 |Exibição da ID da unidade |
3. Os LEDs indicadores de monitoramento na parte traseira do compartimento primário também podem ser usado para identificar o PCM defeituoso. Consulte o diagrama e a tabela a seguir para entender como usar os LEDs para localizar o PCM defeituoso. Por exemplo, se o LED correspondente à **Falha do Ventilador** estiver aceso, houve falha no ventilador. Da mesma forma, se o LED correspondente à **Falha de CA** estiver aceso, a fonte de alimentação falhou. 
   
    ![Backplane dos LEDs indicadores de monitoramento de PCM do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figura 2** Parte posterior do PCM com LEDs indicadores
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |Falha de energia CA |
   | 2 |Falha do ventilador |
   | 3 |Falha de bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia CC |
   | 6 |Bateria íntegra |
4. Consulte o diagrama a seguir da parte traseira do dispositivo StorSimple para localizar o módulo de PCM com falha. PCM 0 está à esquerda e PCM 1 está à direita. A tabela a seguir explica os módulos.
   
     ![Backplane dos módulos do compartimento primário do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figura 3** Parte traseira do dispositivo com módulos de plug-in 
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
5. Desative o PCM defeituoso e desconecte o cabo da fonte de alimentação. Agora você pode remover o PCM.
6. Segure a trava e o lado da alça do PCM entre o polegar e o dedo indicador e aperte-os para abrir a alça.
   
    ![Abrindo a alça do PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figura 4** Abertura da alça do PCM
7. Segure a alça e remova o PCM.
   
    ![Removendo o PCM do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figura 5** Removendo o PCM

## <a name="install-a-replacement-pcm"></a>Instalar um PCM de reposição
Siga estas instruções para instalar um PCM em seu dispositivo StorSimple. Certifique-se de que você inseriu o módulo de bateria de backup antes de instalar o PCM de substituição (aplicável somente aos PCMs de 764 W). Para obter mais informações, confira como [remover e inserir um módulo de bateria de backup](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar um PCM
1. Verifique se possui a peça de reposição do PCM para esse compartimento. O compartimento primário necessita de um PCM de 764 W e o compartimento EBOD necessita de um PCM de 580 W. Você não deve tentar usar o PCM de 580 W no compartimento primário nem o PCM de 764 W no compartimento EBOD. A imagem a seguir mostra onde identificar essas informações na etiqueta do PCM.
   
    ![Etiqueta do PCM do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figura 6** Etiqueta do PCM
2. Verifique se há danos no compartimento, prestando muita atenção aos conectores. 
   
   > [!NOTE]
   > **Não instale o módulo se algum pino do conector estiver torto.**
   > 
   > 
3. Com a alça do PCM na posição aberta, deslize o módulo para dentro do compartimento.
   
    ![Instalando o PCM do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figura 7** Instalando o PCM
4. Feche manualmente a alça do PCM. Você deve ouvir um clique ao encaixar a trava da alça.
   
   > [!NOTE]
   > Para garantir que os pinos do conector tenham encaixado, com cuidado, puxe a alça sem soltar a trava. Se o PCM deslizar, isso significa que a trava foi fechada antes que os conectores encaixassem.
   
5. Conecte os cabos de energia na fonte de alimentação e no PCM.
6. Proteja os ganchos de alívio de tensão.
7. Ligue o PCM.
8. Verifique se a substituição foi bem-sucedida: no Portal do Azure do seu serviço Gerenciador de Dispositivos do StorSimple, navegue até seu dispositivo e, em seguida, até **Configurações > Monitor > Integridade do hardware**. Em **Componentes compartilhados**, o status do PCM deverá ser verde.
   
   > [!NOTE]
   > Pode levar alguns minutos até que o PCM de reposição esteja completamente inicializado.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

