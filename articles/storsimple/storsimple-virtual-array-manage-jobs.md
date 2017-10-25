---
title: Exibir e gerenciar trabalhos do StorSimple Virtual Array | Microsoft Docs
description: "Descreve a página de Trabalhos do serviço do Gerenciador de Dispositivos StorSimple e como usá-la para controlar trabalhos recentes e atuais para a Matriz Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Use o serviço do Gerenciador de Dispositivos StorSimple para exibir os trabalhos para a Matriz Virtual StorSimple
## <a name="overview"></a>Visão geral
A folha **Trabalhos** fornece um único portal central para exibir e gerenciar trabalhos iniciados em matrizes virtuais que estão conectadas ao serviço do Gerenciador de Dispositivos StorSimple. Você pode exibir os trabalhos em execução, concluídos e com falha para vários dispositivos virtuais. Os resultados são apresentados em um formato tabular.

![Folha de trabalhos](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Você pode localizar rapidamente os trabalhos nos quais está interessado filtrando os campos, como:

* **Intervalo de tempo** – Os trabalhos podem ser filtrados com base no intervalo de data e hora.
* **Dispositivos** – os trabalhos são iniciados em um dispositivo específico conectado ao seu serviço. Os trabalhos filtrados são então tabulados com base nos seguintes atributos:
  
  * **Nome** – O nome do trabalho pode ser **Todos**, **Backup**, **Clone**, **Failover**, **Baixar atualizações** ou **Instalar atualizações**.
  * **Status** – Os trabalhos podem ser **Todos**, **Em andamento**, **Concluídos com sucesso**, **Com falha**, ou **Cancelado**.
  * **Entidade** – os trabalhos podem ser associados a um volume, compartilhamento ou dispositivo.
  * **Dispositivo** – o nome do dispositivo no qual o trabalho foi iniciado.
  * **Iniciado em** – a hora em que o trabalho foi iniciado.
  * **Duração** – a duração de execução do trabalho.
* **Status** – você pode pesquisar todos os trabalhos em execução, concluídos ou com falha.
* **Tipo de trabalho** – o tipo de trabalho pode ser todos, backup, restauração, failover, baixar atualizações ou instalar atualizações.

A lista de trabalhos é atualizada a cada 30 segundos.

## <a name="view-job-details"></a>Exibir detalhes do trabalho
Execute as etapas a seguir para exibir os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para exibir detalhes do trabalho
1. Na folha **Trabalhos** , exiba o(s) trabalho(s) no(s) qual(is) está interessado executando uma consulta com os filtros apropriados. Você pode pesquisar trabalhos concluídos ou em execução.
2. Selecione um trabalho na lista tabular dos trabalhos.
   
    ![Folha de trabalho](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Na parte inferior da página, clique em **Detalhes**.
4. Na caixa de diálogo **Detalhes** , você pode exibir o status, os detalhes e as estatísticas de tempo. A ilustração a seguir mostra um exemplo da caixa de diálogo **Detalhes do trabalho de Backup** .
   
    ![Detalhes do trabalho](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas de trabalho quando a máquina virtual está em pausa no hipervisor
Quando um trabalho estiver em andamento na sua Matriz Virtual StorSimple e o dispositivo (a máquina virtual provisionada no hipervisor) estiver em pausa há mais de 15 minutos, o trabalho falhará. Isso ocorre devido ao tempo StorSimple Virtual Array estar fora de sincronia com a hora do Microsoft Azure. 

Você verá o seguinte erro: "A hora do dispositivo está fora de sincronia com a hora do Microsoft Azure por mais de 15 minutos. Certifique-se de que os horários do hipervisor e do dispositivo são sincronizados com um servidor NTP. Certifique-se de que não haja nenhum problema de conectividade. Para solucionar problemas de conectividade, execute testes de diagnóstico da interface do usuário da Web local do seu dispositivo virtual."

Essas falhas aplicam-se aos trabalhos de backup, restauração, atualização e failover. Se sua máquina virtual for provisionada no Hyper-V, ela eventualmente sincroniza a hora com o hipervisor. Depois que isso acontece, você pode reiniciar seu trabalho.

## <a name="next-steps"></a>Próximas etapas
[Saiba como usar a interface do usuário da Web local para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

