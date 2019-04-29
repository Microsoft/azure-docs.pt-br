---
title: Substituir uma unidade de disco em um dispositivo StorSimple da série 8000 | Microsoft Docs
description: Explica como substituir uma unidade de disco em um compartimento primário StorSimple ou em um compartimento EBOD.
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
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576769"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Substituir uma unidade de disco em um dispositivo StorSimple da série 8000

## <a name="overview"></a>Visão geral
Este tutorial explica como remover e substituir uma unidade de disco rígido com defeito ou com falha em um dispositivo Microsoft Azure StorSimple. Para trocar uma unidade de disco, é necessário:

* Soltar o bloqueio antiviolação
* Remover a unidade de disco
* Instalar a unidade de disco de reposição

> [!IMPORTANT]
> Antes de remover e substituir uma unidade de disco, examine as informações de segurança em [Substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Soltar o bloqueio antiviolação
Este procedimento explica como os bloqueios antiviolação em seu dispositivo StorSimple podem ser ativados ou desativados ao substituir as unidades de disco. Os bloqueios antiviolação são ajustados nas alças do suporte da unidade e são acessados por meio de uma abertura pequena na parte da trava da alça. As unidades são fornecidas com os bloqueios na posição travada.

#### <a name="to-unlock-the-antitamper-lock"></a>Para destravar o bloqueio antiviolação
1. Cuidadosamente, insira a chave de bloqueio (uma chave de fenda T10 "à prova de violações" fornecidos pela Microsoft) no encaixe da abertura da alça. 
   
   Se o bloqueio antiviolação estiver ativado, o indicador vermelho ficará visível na abertura.
  
    ![Unidade de disco bloqueada](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Figura 1** Bloqueio antiviolação ativado
   
   | Rótulo | DESCRIÇÃO |
   |:--- |:--- |
   | 1 |Abertura do indicador |
   | 2 |Bloqueio antiviolação |
2. Gire a chave no sentido anti-horário até que o indicador vermelho não esteja visível na abertura acima da chave.
3. Remova a chave.
   
    ![ Unidade de disco desbloqueada](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Figura 2** Unidade de disco desbloqueada
4. A unidade de disco agora pode ser removida.

Siga as etapas na ordem inversa para ativar o bloqueio.

## <a name="remove-the-disk-drive"></a>Remover a unidade de disco
O dispositivo StorSimple dá suporte a uma configuração de espaços de armazenamento similar a RAID 10. Isso significa que ele pode operar normalmente com um disco com falha, unidade de estado sólido (SSD) ou unidade de disco rígido (HD).

> [!IMPORTANT]
> * Se o seu sistema tiver mais de um disco com falha, nunca remova mais de um SSD ou HDD do sistema. Isso pode resultar em perda de dados.
> * Lembre-se de colocar um SSD de reposição em um slot que continha anteriormente um SSD. Da mesma forma, coloque um HDD de reposição em um slot que continha anteriormente um HDD.
> * No Portal do Azure, os slots são numerados de 0 a 11. Portanto, se o portal mostra que um disco no slot 2 falhou, no dispositivo, localize o disco com falha no terceiro slot da parte superior esquerda.
> 
> 

As unidades podem ser removidas e substituídas enquanto o sistema estiver funcionando.

#### <a name="to-remove-a-drive"></a>Para remover uma unidade
1. Para identificar o disco com falha, no Portal do Azure, acesse **Configurações > Integridade do hardware** no seu dispositivo. Como um disco pode falhar no compartimento primário e/ou em um compartimento EBOD (se você estiver usando um modelo 8600), observe o status dos discos em **Componentes compartilhados** e em **Componentes compartilhados de EBOD**. Um disco com falha em um compartimento será mostrado com  status vermelho.
2. Localize as unidades na frente do compartimento primário ou do compartimento EBOD. 
3. Se o disco estiver desbloqueado, prossiga para a próxima etapa. Se o disco estiver bloqueado, desbloqueie-o seguindo o procedimento [Desativar o bloqueio antiviolação](#disengage-the-antitamper-lock).
4. Pressione a trava preta no módulo do suporte da unidade e puxe a alça do suporte da unidade para fora da parte frontal do chassi.
   
    ![Liberando a alça da unidade de disco](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Figura 3** Liberação da alça da unidade
5. Quando a alça do suporte da unidade estiver totalmente estendida, deslize o suporte da unidade para fora do chassi. 
   
    ![Deslizando o disco para fora da unidade de disco](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Figura 4** Deslizando a unidade de disco para fora do suporte

## <a name="install-the-replacement-disk-drive"></a>Instalar a unidade de disco de reposição
Após uma falha de unidade em seu dispositivo StorSimple e depois que você removê-la, siga este procedimento para substituí-la por uma nova unidade.

#### <a name="to-insert-a-drive"></a>Para inserir uma unidade
1. Verifique se que a alça do suporte da unidade está totalmente estendida, conforme mostrado na imagem a seguir.
   
    ![Unidade de disco com alça estendida](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Figura 5** Unidade com alça estendida
2. Deslize o suporte da unidade completamente para dentro do chassi.
   
    ![Deslizando o disco para dentro do suporte da unidade de disco](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Figura 6** Deslizando o suporte da unidade para dentro do chassi
3. Com o suporte da unidade inserido, feche a alça do suporte enquanto continua a empurrar o suporte da unidade para dentro do chassi, até que a alça se encaixe na posição travada.
4. Use a chave de bloqueio que foi fornecida pela Microsoft (chave de fenda Torx à prova de violações) para prender a alça do suporte no lugar girando os parafusos um quarto de volta no sentido horário.
5. Verifique se a substituição foi bem-sucedida e se a unidade está funcionando. Acesse o Portal do Azure e navegue para **Configurações do dispositivo** > **Integridade do hardware**. Em **Componentes compartilhados** ou **Componentes compartilhados de EBOD**, o status da unidade deverá ficar verde, indicando que ela está íntegra.

   
   > [!NOTE]
   > Pode levar várias horas até que o status do disco fique verde após a troca.
  
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

