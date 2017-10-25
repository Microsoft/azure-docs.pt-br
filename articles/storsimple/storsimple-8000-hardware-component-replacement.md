---
title: "Substituição de componentes de hardware do StorSimple da série 8000 | Microsoft Docs"
description: "Descreve como substituir com segurança PCMs, bateria, módulos de controlador, controladores do EBOD, unidades de disco e chassis de um dispositivo StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: 
ms.openlocfilehash: 6de50c5031db59176bdf17ecc69b934559220f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Substituir um componente de hardware no dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral
Os tutoriais de substituição de componentes de hardware descrevem os componentes de hardware do seu dispositivo da série 8000 do Microsoft Azure StorSimple e as etapas necessárias para removê-los e substituí-los. Este artigo descreve os ícones de segurança, fornece ponteiros para os tutoriais detalhados e lista os componentes que podem ser substituíveis.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente do StorSimple, lembre-se de consultar as [convenções de ícones de segurança](#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Convenções de ícones de segurança
A tabela a seguir descreve os ícones de segurança usados nestes tutoriais. Preste muita atenção a esses ícones de segurança ao percorrer as etapas para remover e substituir componentes do dispositivo.

| ícone | Texto | Informações adicionais |
|:--- |:--- |:--- |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**PERIGO!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou lesões graves. Essa palavra de sinalização limita-se às situações mais graves. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**AVISO!** |Indica uma situação perigosa que, se não for evitada, pode causar lesões graves ou de morte. |
| ![Ícone de cuidado](./media/storsimple-hardware-component-replacement/Caution.png) |**CUIDADO!** |Indica uma situação perigosa que, se não for evitada, pode em uma lesão pequena ou moderada. |
| ![Ícone de observação](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**OBSERVAÇÃO:** |Indica informações consideradas importantes, mas não são relacionadas a riscos. |
| ![Ícone de choque elétrico](./media/storsimple-hardware-component-replacement/Electric.png) |**Risco de choque elétrico** |Indica alta tensão. |
| ![ícone de peso pesado](./media/storsimple-hardware-component-replacement/Weight.png) |**Peso pesado** | |
| ![Ícone de nenhuma peça operada pelo usuário](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Nenhuma peça é operada pelo usuário** |Não acesse a menos que seja devidamente treinado. |
| ![Ícone de leia as instruções](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Leia todas as instruções primeiro** | |
| ![Ícone de risco de tombamento](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Risco de tombamento** | |

### <a name="before-you-begin"></a>Antes de começar
Familiarize-se com as informações de segurança sobre o seu dispositivo e com os ícones de segurança usados neste tutorial. Vá para [Instalar e operar seu dispositivo StorSimple com segurança](storsimple-safety.md) para obter informações completas. Examine as [Precauções de segurança](storsimple-safety.md#handling-precautions) antes manusear o seu dispositivo StorSimple.

Antes de tentar substituir um componente, considere as seguintes informações.

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Electrical Shock Icon](./media/storsimple-hardware-component-replacement/Electric.png) **AVISO!**

* Proteja-se corretamente usando um tapete antiestático ao lidar com módulos e componentes do seu dispositivo StorSimple.
* Não toque em nenhum circuito. Use as alças e os guias ao manusear componentes que possam ter circuitos expostos.

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Notice Icon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **OBSERVAÇÃO:**

Ao substituir um módulo, **NUNCA deixe um compartimento vazio na parte traseira do compartimento**. Obtenha uma peça de reposição ou um módulo vazio antes de remover a peça com problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimentos de substituição de componentes de hardware
Seu dispositivo StorSimple da série 8000 consiste de vários módulos de plug-in nos compartimentos primário e/ou EBOD. O 8100 tem um único compartimento primário, enquanto o 8600 é um dispositivo de compartimento duplo com um compartimento primário e um compartimento EBOD.

Os principais componentes de hardware do dispositivo são resumidos nas tabelas a seguir. Clique no link na coluna **Procedimento de substituição** para ir para o tutorial relacionado.

| Componentes | Quantidade presente | Módulo plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Não |[Substituir o chassi em seu dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores principais |2 |Sim |[Substituir um módulo de controlador em seu dispositivo StorSimple](storsimple-8000-controller-replacement.md) |
| Módulos de energia e resfriamento (PCMs) de 764W |2 |Sim |[Substituir um módulo de energia e resfriamento em seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Bateria de backup |2 |Sim |[Substituir o módulo de bateria de backup em seu dispositivo StorSimple](storsimple-8000-battery-replacement.md) |
| Unidades de disco |12 |Sim |[Substituir uma unidade de disco em seu dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** Componentes de hardware no compartimento primário

O compartimento primário e o compartimento EBOD têm módulos de E/S diferentes. Além disso, os PCMs têm potências diferentes. Os PCMs no compartimento primário são de 764 W, enquanto aqueles no compartimento EBOD são de 580 W. Os PCMs no compartimento primário também contêm um módulo de bateria de backup.

| Componentes | Quantidade presente | Módulo plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassi |1 |Não |[Substituir o chassi em seu dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores do EBOD |2 |Sim |[Substituir um controlador EBOD em seu dispositivo StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| Módulos de energia e resfriamento (PCMs) de 580W |2 |Sim |[Substituir um módulo de energia e resfriamento em seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Unidades de disco |12 |Sim |[Substituir uma unidade de disco em seu dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** Componentes de hardware no compartimento EBOD

Os módulos plug-in no dispositivo são realçados nos diagramas frontal e traseiro a seguir. É possível usar esses diagramas para determinar a localização de vários módulos plug-in caso uma substituição seja necessária. O diagrama frontral mostra as unidades de disco e os diagramas traseiros do compartimento EBOD e do compartimento primário mostram os módulos plug-in.

![Frontplane do dispositivo com unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Parte frontal do dispositivo

| Rótulo | Descrição |
|:--- |:--- |
| 0 - 11 |Unidades de disco (total de 12) |

Tanto compartimento primário como o compartimento EBOD têm módulos de suporte de unidade. O chassi abriga 12 unidades de disco de 3,5" organizadas em um formato de 3 por 4.

![Backplane dos módulos do compartimento primário do dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Parte posterior do compartimento primário

| Rótulo | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador 0 |
| 4 |Controlador 1 |

![Backplane dos módulos plug-in do compartimento EBOD do dispositivo](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Parte posterior do compartimento EBOD

| Rótulo | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador 0 do EBOD |
| 4 |Controlador 1 do EBOD |

## <a name="field-replaceable-units"></a>Unidades renováveis
As seguintes unidades renováveis (FRUs) estão disponíveis para seu dispositivo StorSimple:

* Chassis (incluindo o painel de operações integrado)
* PCM de CA de 764 W
* PCM de CA de 580 W
* Unidade de disco rígido com módulo de suporte de unidade
* Módulo de controlador
* Módulo de controlador do EBOD
* Módulo de bateria de backup
* Kit do trilho de montagem em rack

[Entre em contato com o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para pedir qualquer uma dessas unidades de reposição.

## <a name="next-steps"></a>Próximas etapas
Examine todas as [informações de segurança](storsimple-safety.md) antes de tentar substituir um componente de hardware do StorSimple.

