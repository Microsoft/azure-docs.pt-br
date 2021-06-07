---
title: Instalar a atualização 5,1 no dispositivo StorSimple da série 8000 | Microsoft Docs
description: Explica como instalar a atualização 5,1 do StorSimple 8000 Series em seu dispositivo StorSimple da série 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91575955"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Instalar a atualização 5,1 em seu dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este tutorial explica como instalar a atualização 5,1 em um dispositivo StorSimple que executa uma versão de software anterior por meio do portal do Azure. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

A atualização 5,1 inclui atualizações de segurança sem interrupções. As atualizações sem interrupção ou regulares podem ser aplicadas por meio do portal do Azure <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * A atualização 5,1 é uma atualização obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte [Atualizar notas de versão 5,1](storsimple-update51-release-notes.md).
> * Um conjunto de verificações prévias manuais e automáticas para são realizadas antes da instalação para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas verificações prévias serão executadas somente se você aplicar as atualizações do portal do Azure.
> * Se você quiser instalar o usando o método de hotfix, entre em contato com [suporte da Microsoft](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Instalar a atualização 5,1 por meio do portal do Azure

Execute as etapas a seguir para atualizar seu dispositivo para a [atualização 5,1](storsimple-update51-release-notes.md).

> [!NOTE]
> Microsoft efetua pull de informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos com problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização por meio do portal do Azure

1. Na página do serviço StorSimple, selecione seu dispositivo.

    ![Selecionar dispositivo](./media/storsimple-8000-install-update-51/update1.png)

2. Navegue até **configurações do dispositivo**  >  **atualizações do dispositivo**.

    ![Captura de tela da folha configurações com a opção atualizações de dispositivo chamada out.](./media/storsimple-8000-install-update-51/update2.png)

3. Uma notificação será exibida se houver novas atualizações disponíveis. Como alternativa, na folha **Atualizações de dispositivo**, clique em **Verificar Atualizações**. Um trabalho é criado para verificar se há atualizações disponíveis. Você será notificado quando o trabalho for concluído com êxito.

    ![Captura de tela da folha configurações com a opção atualizações de dispositivo chamada saída e a folha atualizações de dispositivo com as novas atualizações regulares são mensagens disponíveis, chamadas.](./media/storsimple-8000-install-update-51/update3.png)

4. É recomendável examinar as notas de versão antes de aplicar uma atualização em seu dispositivo. Para aplicar atualizações, clique em **Instalar atualizações**. Na folha **Confirmar atualizações regulares**, examine os pré-requisitos a serem concluídos antes de aplicar as atualizações. Marque a caixa de seleção para indicar que você está pronto para atualizar o dispositivo e, em seguida, clique em **Instalar**.

    ![Captura de tela da folha atualizações do dispositivo com a opção instalar atualizações denominada e confirmar atualizações regulares com a opção de concordar e a opção instalar chamada out.](./media/storsimple-8000-install-update-51/update4.png)

5. Um conjunto de verificações de pré-requisito é iniciado. Essas verificações incluem:
   
   * **Verificações de integridade do controlador** para verificar se ambos os controladores do dispositivo estão em boas condições e online.
   * **Verificações de integridade do componente de hardware** para verificar se todos os componentes de hardware no dispositivo StorSimple estão em boas condições.
   * **Verificações de DATA 0** para verificar se DATA 0 está habilitada no dispositivo. Se essa interface não estiver habilitada, você deverá habilitá-la e repetir a ação.

     A atualização só será baixada e instalada se todas as verificações forem concluídas com êxito. Você será notificado quando as verificações estiverem em andamento. Se as verificações prévias falharem, os motivos da falha serão fornecidos. Resolva esses problemas e então repita a operação. Talvez seja necessário entrar em contato com o Suporte da Microsoft se você não conseguir resolver esses problemas por conta própria.

7. Depois que as pré-verificações tiverem sido concluídas com êxito, será criado um trabalho de atualização. Você será notificado quando o trabalho de atualização for criado com êxito.
   
    ![Captura de tela da notificação que diz "Iniciando o trabalho de atualizações de software".](./media/storsimple-8000-install-update-51/update6.png)
   
    Em seguida, a atualização será aplicada ao seu dispositivo.

9. A atualização demora algumas horas para ser concluída. Selecione o trabalho de atualização e clique em **Detalhes** para exibir os detalhes do trabalho a qualquer momento.

    ![Captura de tela da folha atualizações do dispositivo com a opção baixar e instalar atualizações de software em andamento chamada e a folha instalar atualizações.](./media/storsimple-8000-install-update-51/update8.png)

     Você também pode monitorar o andamento do trabalho de atualização de **Configurações do dispositivo > Trabalhos**. Na folha **Trabalhos**, você pode ver o andamento da atualização.

     ![Captura de tela da folha configurações com trabalhos chamados e a folha trabalhos que mostra o progresso da atualização.](./media/storsimple-8000-install-update-51/update7.png)

10. Após a conclusão do trabalho, navegue até **Configurações do dispositivo > Atualizações do dispositivo**. A versão do software deve ser atualizada.


Verifique se o dispositivo está executando a **atualização 5,1 da série 8000 do StorSimple (6.3.9600.17885)**. A **Data da última atualização** deve ser modificada.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [versão da atualização 5,1](storsimple-update51-release-notes.md).
