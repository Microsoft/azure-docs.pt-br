---
title: "Instalar a Atualização 3 no dispositivo StorSimple | Microsoft Docs"
description: "Explica como instalar a Atualização 3 do StorSimple série 8000 em seu dispositivo StorSimple série 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72b004a6c2604e0fc20b71b4b69217622f8f9ea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>Instalar a Atualização 3 em seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral

Este tutorial explica como instalar a Atualização 3 em um dispositivo StorSimple que executa uma versão de software anterior por meio do portal clássico do Azure e usando o método de hotfix. O método de hotfix é usado quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple e quando você está tentando atualizar de uma versão de software anterior à Atualização 1.

A Atualização 3 inclui atualizações de software de dispositivo, firmware e driver LSI, Storport e Spaceport. Se estiver atualizando da Atualização 2 ou de uma versão anterior, também será necessário aplicar atualizações de iSCSI, WMI e, em alguns casos, de firmware de disco. O software do dispositivo e as correções de WMI, iSCSI, LSI driver, Spaceport e Storport são atualizações sem interrupções e podem ser aplicadas por meio do Portal Clássico do Azure. As atualizações de firmware de disco são as atualizações de interrupção e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo. 

> [!IMPORTANT]
> * Um conjunto de verificações prévias manuais e automáticas para são realizadas antes da instalação para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações são executadas somente se você aplicar as atualizações no portal clássico do Azure.
> * Recomendamos que você instale as atualizações de software e driver através do Portal clássico do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no portal. Dependendo da versão da qual você está atualizando, as atualizações podem levar de 1,5 a 2,5 horas para serem instaladas. As atualizações do modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.
> * Se estiver executando o StorSimple Snapshot Manager opcional, certifique-se de ter atualizado sua versão Snapshot Manager para a Atualização 2 antes de atualizar o dispositivo.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Instalar a Atualização 3 por meio do portal clássico do Azure
Realize as etapas a seguir para atualizar seu dispositivo para a [Atualização 3](storsimple-update3-release-notes.md).

> [!NOTE]
> Se você estiver aplicando a Atualização 2 ou posterior (incluindo a Atualização 2.1), a Microsoft poderá receber informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos com problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas. Ao aceitar a Atualização 2 ou posterior, você nos permite oferecer esse suporte proativo.
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Verifique se o dispositivo está executando a **Atualização 3 do StorSimple 8000 Series (6.3.9600.17759)**. A **Data da última atualização** também deve ser modificada. 
   - Se você estiver atualizando de uma versão anterior à Atualização 2, você também verá que as atualizações do modo de manutenção estarão disponíveis (essa mensagem poderá continuar a ser exibida por até 24 horas após a instalação das atualizações).
     As atualizações do modo de manutenção são atualizações interrompidas que resultam em tempo de inatividade do dispositivo e podem ser aplicadas apenas por meio da interface do Windows PowerShell de seu dispositivo. Em alguns casos quando a Atualização 1.2 estiver sendo executada, o firmware de disco poderá já estar atualizado; nesse caso, não é necessário instalar quaisquer atualizações do modo de manutenção.
   - Se você estiver atualizando da Atualização 2 ou posterior, seu dispositivo agora deverá estar atualizado. Você pode ignorar a próxima etapa.

Baixe as atualizações do modo de manutenção usando as etapas listadas em [Para baixar os hotfixes](#to-download-hotfixes) para pesquisar e baixar a KB3121899, que instala atualizações de firmware de disco (as outras atualizações já devem estar instaladas agora). Siga as etapas listadas em [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações do modo de manutenção. 

## <a name="install-update-3-as-a-hotfix"></a>Instalar a Atualização 3 como um hotfix
Use este procedimento se a verificação de gateway falhar ao tentar instalar as atualizações por meio do portal clássico do Azure. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são:

* Atualização 0.1, 0.2, 0.3
* Atualização 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2 

> [!IMPORTANT]
> * Se o dispositivo estiver executando a versão de lançamento (GA), contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.
> 
> 

O método de hotfix envolve as três etapas a seguir:

1. Baixe os hotfixes do Catálogo do Microsoft Update.
2. Instale e verifique os hotfixes do modo normal.
3. Instale e verifique o hotfix do modo de manutenção (somente ao atualizar o software anterior à Atualização 2).

#### <a name="download-updates-for-your-device"></a>Baixar atualizações para seu dispositivo
**Se o dispositivo estiver executando a Atualização 2.1 ou 2.2**, baixe e instale os seguintes hotfixes na ordem indicada:

| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |Atualização de software &#42; |Regular  <br></br>Não interruptiva |~ 45 Min. |
| 2. |KB3186859 |Driver LSI e firmware |Regular  <br></br>Não interruptiva |~ 20 Min. |
| 3. |KB3121261 |Correção do Storport e do Spaceport  </br> Windows Server 2012 R2 |Regular  <br></br>Não interruptiva |~ 20 Min. |

&#42; *Observe que a atualização de software consiste em dois arquivos binários: atualização de software do dispositivo precedida por `all-hcsmdssoftwareupdate` e o agente Cis e Mds precedido por `all-cismdsagentupdatebundle`. A atualização de software do dispositivo deve ser instalada antes do agente CIS e MDS. Também será preciso reiniciar o controlador ativo por meio do cmdlet `Restart-HcsController` após aplicar a atualização de agente Cis e Mds (e antes de aplicar as atualizações restantes).* 

**Se o dispositivo estiver executando a atualização 0.1, 0.2, 0.3, 1.0, 1.1, 1.2 ou 2.0**, você deverá baixar e instalar os seguintes hotfixes além das atualizações de software, do driver de LSI e de firmware (mostradas na tabela anterior), na ordem indicada:

| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |Pacote iSCSI |Regular  <br></br>Não interruptiva |~ 20 Min. |
| 5. |KB3103616 |Pacote WMI |Regular  <br></br>Não interruptiva |~ 12 Min. |

<br></br>

**Se o dispositivo estiver executando as versões 0.2, 0.3, 1.0, 1.1 e 1.2**, você também precisará instalar as atualizações de firmware de disco além de todas as atualizações mostradas nas tabelas anteriores. Você pode verificar se precisa de atualizações de firmware de disco executando o cmdlet `Get-HcsFirmwareVersion` . Se estiver executando as versões de firmware `XMGG`, `XGEG`, `KZ50`, `F6C2` ou `VR08`, você não precisará instalar essas atualizações.

| Classificar | KB | Descrição | Tipo de atualização | Hora da instalação |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |Firmware de disco |Manutenção  <br></br>Interruptiva |~ 30 Min. |

<br></br>

> [!IMPORTANT]
> * Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 3. É possível usar o portal clássico do Azure para aplicar atualizações subsequentes.
> * Se você estiver atualizando da Atualização 2.2, o tempo total de instalação será próximo de 1,1 hora.
> * Antes de usar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores de dispositivo estão online e todos os componentes de hardware estão íntegros.
> 
> 

Execute as seguintes etapas para baixar e instalar os hotfixes.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [versão de Atualização 3](storsimple-update3-release-notes.md).

