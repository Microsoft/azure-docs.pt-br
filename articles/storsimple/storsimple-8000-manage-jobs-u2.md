---
title: "Exibir e gerenciar trabalhos para o StorSimple série 8000 | Microsoft Docs"
description: "Descreve a folha Trabalhos do serviço do Gerenciador de Dispositivos do StorSimple e como usá-la para acompanhar os trabalhos de backup recentes, atuais e agendados."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Use o serviço do Gerenciador de Dispositivos do StorSimple para exibir e gerenciar trabalhos (Atualização 3 e posterior)

## <a name="overview"></a>Visão geral
A folha **Trabalhos** fornece um portal central único para exibir e gerenciar trabalhos que foram iniciados em dispositivos conectados ao serviço do Gerenciador de Dispositivos do StorSimple. É possível exibir os trabalhos agendados, em execução, concluídos, cancelados e com falha para vários dispositivos. Os resultados são apresentados em um formato tabular.

![Folha de trabalhos](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Você pode localizar rapidamente os trabalhos nos quais está interessado filtrando os campos, como:

* **Status** – Os trabalhos podem estar em andamento, ser bem-sucedidos, cancelados ou concluídos com falha.
* **Intervalo de tempo** – Os trabalhos podem ser filtrados com base no intervalo de data e hora. Os intervalos são última hora, últimas 24 horas, últimos 7 dias, últimos 30 dias, último ano ou datas personalizadas.
* **Tipo** – O tipo do trabalho pode ser backup agendado, backup manual, backup de restauração, clonar volume, fazer failover de contêineres de volume, criar volume fixado localmente, modificar volume, instalar atualizações, coletar logs de suporte e criar dispositivo de nuvem.
* **Dispositivos** – os trabalhos são iniciados em um determinado dispositivo conectado ao seu serviço.
  
Os trabalhos filtrados são então tabulados com base nos seguintes atributos:
  
* **Nome** – Backup agendado, backup manual, backup de restauração, clonar volume, fazer failover de contêineres de volume, criar volume fixado localmente, modificar volume, instalar atualizações, coletar logs de suporte ou criar dispositivo de nuvem.
* **Status** – em execução, concluídos, cancelados, com falha, em cancelamento ou concluídos com erros.
* **Entidade** – os trabalhos podem ser associados a um volume, uma política de backup ou um dispositivo. Por exemplo, um trabalho de clone é associado a um volume, enquanto um trabalho de backup agendado é associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma recuperação de desastres (DR) ou uma operação de restauração.
* **Dispositivo** – o nome do dispositivo no qual o trabalho foi iniciado.
* **Iniciado em** – a hora em que o trabalho foi iniciado.
* **Duração** – O tempo necessário para concluir o trabalho.

A lista de trabalhos é atualizada a cada 30 segundos.

É possível executar as seguintes ações relacionadas a trabalho nesta página:

* Exibir detalhes do trabalho
* Cancelar um trabalho

## <a name="view-job-details"></a>Exibir detalhes do trabalho
Execute as etapas a seguir para exibir os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para exibir detalhes do trabalho
1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Trabalhos**.

2. Na folha **Trabalhos**, exiba os trabalhos de seu interesse executando uma consulta com os filtros apropriados. É possível pesquisar trabalhos concluídos, em execução ou cancelados.

    ![Folha de trabalho](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecione e clique em um trabalho.

    ![Folha de trabalho](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. Na folha de detalhes do trabalho, é possível exibir o status, os detalhes, as estatísticas de tempo e as estatísticas de dados.
   
    ![Detalhes do trabalho](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Cancelar um trabalho
Realize as etapas a seguir para cancelar um trabalho em execução.

> [!NOTE]
> Alguns trabalhos, como modificar um volume para alterar o tipo de volume ou expandir um volume, não podem ser cancelados.


### <a name="to-cancel-a-job"></a>Para cancelar um trabalho
1. Na página **Trabalhos** , exiba os trabalhos em execução que você deseja cancelar executando uma consulta com os filtros apropriados. Selecione o trabalho.

2. Clique com botão direito do mouse sobre o trabalho selecionado para invocar o menu de contexto e clique em **Cancelar**.

    ![Detalhes do trabalho](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Quando solicitado a confirmar, clique em **Sim**. Este trabalho agora está cancelado.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [gerenciar as políticas de backup do StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

