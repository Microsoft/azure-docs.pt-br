---
title: Substituir o chassi no dispositivo StorSimple série 8000 | Microsoft Docs
description: Descreve como remover e substituir o chassi em seu compartimento StorSimple primário ou compartimento EBOD.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61312438"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substituir o chassi em seu dispositivo StorSimple
## <a name="overview"></a>Visão geral
Esse tutorial explica como remover e substituir um chassi em um dispositivo StorSimple 8000. O modelo StorSimple 8100 é um dispositivo de compartimento único (um chassi), enquanto o 8600 é um dispositivo de compartimento duplo (dois chassis). No modelo 8600, há potencialmente dois chassis que podem falhar no dispositivo: o chassi do compartimento primário ou o chassi do compartimento EBOD.

Em ambos os casos, o chassi de substituição enviado pela Microsoft estará vazio. Não será incluído nenhum módulos de energia e resfriamento (PCM), módulo de controlador, unidade de disco de estado sólido (SSD), unidade de disco rígido (HD) ou módulos EBOD.

> [!IMPORTANT]
> Antes de remover e substituir p chassi, examine as informações de segurança em [Substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Remover o chassi
Execute as seguintes etapas para remover o chassi em seu dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um chassi
1. Certifique-se de que o dispositivo StorSimple esteja desligado e desconectado de todas as fontes de alimentação.
2. Remova todos os cabos SAS e de rede, se houver.
3. Remova a unidade do rack.
4. Remova cada uma das unidades e observe os slots do quais são removidas. Para obter mais informações, consulte [Remover a unidade de disco](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. No compartimento EBOD (se for o chassi com falha), remova os módulos do controlador EBOD. Para obter mais informações, consulte [Remover um controlador EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    No compartimento primário (se for o chassi com falha), remova os controladores e observe os slots dos quais são removidos. Para obter mais informações, consulte [Remover um controlador](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalar o chassi
Execute as seguintes etapas para instalar o chassi em seu dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um chassi
1. Monte o chassi no rack. Para saber mais, confira [Montar em rack o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [Montar em rack o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Depois que o chassis estiver montado no rack, instale os módulos do controlador nas mesmas posições que eles estavam instalados anteriormente.
3. Instale as unidades nas mesmas posições e slots de que elas estavam instaladas anteriormente.
   
   > [!NOTE]
   > É recomendável instalar primeiro os SSDs nos slots e, em seguida, instalar os HDDs.
  
4. Com o dispositivo montado no rack e os componentes instalados, conecte o dispositivo nas fontes de alimentação apropriadas e ligue o dispositivo. Para obter detalhes, confira [Cabear o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [Cabear o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [substituição de componentes de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

