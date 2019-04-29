---
title: Instalar a Atualização 4 no dispositivo StorSimple da série 8000 | Microsoft Docs
description: Explica como instalar a Atualização 4 do StorSimple série 8000 em seu dispositivo StorSimple série 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860271"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalar a Atualização 4 em seu dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como instalar a Atualização 4 em um dispositivo StorSimple que executa uma versão de software anterior pelo Portal do Azure e usando o método de hotfix. O método de hotfix é usado quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple e quando você está tentando atualizar de uma versão de software anterior à Atualização 1.

A Atualização 4 inclui atualizações de segurança do SO, do software do dispositivo, do firmware USM, de driver e firmware LSI, do Storport e Spaceport, bem como um host de outras atualizações do SO.  As atualizações do software do dispositivo, do firmware USM, do Spaceport, Storport e outras atualizações do SO não são interruptivas. As atualizações não interruptivas ou regulares podem ser aplicadas pelo Portal do Azure ou por meio do método de hotfix. As atualizações de firmware de disco são interruptivas e podem ser aplicadas somente pelo método de hotfix usando a interface do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Um conjunto de verificações prévias manuais e automáticas para são realizadas antes da instalação para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações serão executadas somente se você aplicar as atualizações no Portal do Azure.
> * É recomendável instalar a atualização do software e outras atualizações regulares pelo Portal do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no portal. Dependendo da versão da qual você está atualizando, as atualizações podem levar 4 horas (ou mais) para serem instaladas. As atualizações do modo de manutenção também devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.
> * Se estiver executando o Snapshot Manager do StorSimple opcional, verifique se você atualizou a versão do Snapshot Manager para a Atualização 4 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalar a Atualização 4 pelo Portal do Azure
Conclua as etapas a seguir para atualizar o dispositivo para a [Atualização 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft efetua pull de informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos com problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Verifique se o dispositivo está executando a **Atualização 4 do StorSimple série 8000 (6.3.9600.17820)**. A **Data da última atualização** também deve ser modificada.

* Agora, você também verá que as atualizações do modo de Manutenção estão disponíveis (essa mensagem pode continuar sendo exibida por até 24 horas após a instalação das atualizações). As atualizações do modo de manutenção são atualizações interrompidas que resultam em tempo de inatividade do dispositivo e podem ser aplicadas apenas por meio da interface do Windows PowerShell de seu dispositivo.

* Baixe as atualizações do modo de manutenção usando as etapas listadas em [para baixar os hotfixes](#to-download-hotfixes) a fim de pesquisar e baixar a KB4011837, que instala atualizações de firmware de disco (a essa altura, as outras atualizações já devem estar instaladas). Siga as etapas listadas em [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações do modo de manutenção.

## <a name="install-update-4-as-a-hotfix"></a>Instalar a Atualização 4 como um hotfix
O método recomendado para instalar a Atualização 4 é pelo Portal do Azure.

Use este procedimento se a verificação de gateway falhar ao tentar instalar as atualizações por meio do Portal do Azure. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são:

* Atualização 0.1, 0.2, 0.3
* Atualização 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1


O método de hotfix envolve as três etapas a seguir:

1. Baixe os hotfixes do Catálogo do Microsoft Update.
2. Instale e verifique os hotfixes do modo normal.
3. Instale e verifique o hotfix do modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Baixar atualizações para seu dispositivo

Você deve baixar e instalar os seguintes hotfixes na ordem descrita e as pastas sugeridas:

| Classificar | KB | DESCRIÇÃO | Tipo de atualização | Hora da instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Atualização de software |Regular <br></br>Não interruptiva |Aproxim. 25 minutos |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Atualizações de firmware e driver LSI <br> Atualização de firmware USM (versão 3.38) |Regular <br></br>Não interruptiva |Aproxim. 3 horas <br> (inclui 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pacote de atualizações de segurança do SO <br> Baixar o Windows Server 2012 R2 |Regular <br></br>Não interruptiva |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Baixar o Windows Server 2012 R2 |Regular <br></br>Não interruptiva |- |SecondOrderUpdate|

Você também precisa instalar as atualizações de firmware de disco além de todas as atualizações mostradas nas tabelas anteriores. Você pode verificar se precisa de atualizações de firmware de disco executando o cmdlet `Get-HcsFirmwareVersion` . Se estiver executando as versões de firmware `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, você não precisará instalar essas atualizações.

| Classificar | KB | DESCRIÇÃO | Tipo de atualização | Hora da instalação | Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmware de disco |Manutenção  <br></br>Interruptiva |~ 30 Min. | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 4. É possível usar o Portal do Azure para aplicar atualizações subsequentes.
> * Se você estiver atualizando da Atualização 3 ou 3.1, o tempo total de instalação será próximo de 4 horas.
> * Antes de usar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores de dispositivo estão online e todos os componentes de hardware estão íntegros.

Execute as seguintes etapas para baixar e instalar os hotfixes.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [versão da Atualização 4](storsimple-update4-release-notes.md).

