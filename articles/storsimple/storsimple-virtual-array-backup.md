---
title: Tutorial de backup da Matriz Virtual do Microsoft Azure StorSimple | Microsoft Docs
description: Descreve como fazer backup de volumes e compartilhamentos do StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581188"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Fazer backup de volumes ou compartilhamentos no StorSimple Virtual Array

## <a name="overview"></a>Visão geral

O StorSimple Virtual Array é um dispositivo virtual local de armazenamento em nuvem híbrida que pode ser configurado como servidor de arquivos ou um servidor iSCSI. A matriz virtual permite ao usuário criar backups agendados e manuais de todos os volumes ou compartilhamentos no dispositivo. Quando configurado como um servidor de arquivos, ele também permite a recuperação em nível de item. Este tutorial descreve como criar backups agendados e manuais e realizar a recuperação em nível de item para restaurar um arquivo excluído na matriz virtual.

Este tutorial se aplica somente a Matrizes Virtuais StorSimple. Para obter informações sobre a série 8000, acesse [Criar um backup para dispositivo da série 8000](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Fazer backup de compartilhamentos e volumes

Backups oferecem proteção pontual, melhoram a capacidade de recuperação e minimizam os tempos de compartilhamentos e volumes. Você pode fazer backup de um compartilhamento ou volume em seu dispositivo StorSimple de duas maneiras: **Agendado** ou **Manual**. Cada um desses métodos é discutido nas seções a seguir.

## <a name="change-the-backup-start-time"></a>Alterar a hora de início do backup

> [!NOTE]
> Nesta versão, os backups agendados são criados por uma política padrão que é executada diariamente em um período especificado e faz backup de todos os volumes ou compartilhamentos no dispositivo. Não é possível criar políticas personalizadas para backups agendados no momento.


a Matriz Virtual StorSimple tem uma política de backup padrão que é iniciada em uma hora específica do dia (22:30) e faz o backup de todos os volumes ou compartilhamentos no dispositivo uma vez por dia. Você pode alterar a hora de início do backup, mas a frequência e a retenção (que especifica o número de backups a serem retidos) não podem ser alteradas. Durante esses backups, o backup é feito em todo o dispositivo virtual. Isso poderia afetar o desempenho do dispositivo e afetar as cargas de trabalho implantadas no dispositivo. Portanto, recomendamos agendar esses backups fora do horário de pico.

 Para alterar o horário de início do backup padrão, execute as etapas a seguir no [portal do Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar o horário de início para a política de backup padrão

1. Vá para **Dispositivos**. A lista de dispositivos registrados no serviço do Gerenciador de Dispositivos do StorSimple será exibida. 
   
    ![navegar até os dispositivos](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecione e clique em seu dispositivo. A folha **Configurações** é exibida. Vá para **Gerenciar > Políticas de backup**.
   
    ![selecionar o dispositivo](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Na folha **Políticas de backup**, a hora de início padrão é 22:30. Você pode especificar a nova hora de início da agenda diária no fuso horário do dispositivo.
   
    ![navegar até as políticas de backup](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Clique em **Salvar**.

### <a name="take-a-manual-backup"></a>Fazer um backup manual

Além de backups agendados, você pode fazer um backup manual (sob demanda) dos dados do dispositivo a qualquer momento.

#### <a name="to-create-a-manual-backup"></a>Para criar um backup manual

1. Vá para **Dispositivos**. Selecione seu dispositivo e clique com o botão direito do mouse em **...** na extremidade direita da linha selecionada. No menu de contexto, selecione **Fazer backup**.
   
    ![navegar até fazer backup](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Na folha **Fazer backup**, clique em **Fazer backup**. Isso fará backup de todos os compartilhamentos no servidor de arquivos ou de todos os volumes no servidor iSCSI. 
   
    ![backup iniciando](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Um backup sob demanda é iniciado e você verá que um trabalho de backup foi iniciado.
   
    ![backup iniciando](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Depois que o trabalho é concluído com êxito, você é notificado novamente. O processo de backup é iniciado.
   
    ![trabalho de backup criado](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Para acompanhar o progresso dos backups e examinar os detalhes do trabalho, clique na notificação. Isso levará você para os **Detalhes do trabalho**.
   
     ![detalhes do trabalho de backup](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Quando o backup for concluído, acesse **Gerenciamento > Catálogo de backup**. Você verá um instantâneo de nuvem de todos os compartilhamentos (ou volumes) em seu dispositivo.
   
    ![Backup concluído](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Exibir os backups existentes
Execute as etapas a seguir no portal do Azure para exibir os backups existentes.

#### <a name="to-view-existing-backups"></a>Para exibir os backups existentes

1. Vá para a página **Dispositivos**. Selecione e clique em seu dispositivo. Na folha **Configurações**, acesse **Gerenciamento > Catálogo de Backup**.
   
    ![Navegar até o catálogo de backup](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Especifique os seguintes critérios para uso na filtragem:
   
   - **Intervalo de tempo** – pode ser **Última hora**, **Últimas 24 horas**, **Últimos 7 dias**, **Últimos 30 dias**, **Ano passado** e **Data personalizada**.
    
   - **Dispositivos** – selecione na lista de servidores de arquivos ou servidores iSCSI registrados no serviço do Gerenciador de Dispositivos do StorSimple.
   
   - **Iniciado** – pode ser **Agendado** automaticamente (por uma política de backup) ou iniciado **Manualmente** (por você).
   
     ![Backups de filtro](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Clique em **Aplicar**. A lista filtrada de backups é exibida na folha **Catálogo de backup**. Observe que apenas 100 elementos de backup podem ser exibidos de cada vez.
   
    ![Catálogo de backup atualizado](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

