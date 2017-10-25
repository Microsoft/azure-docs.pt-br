---
title: "Gerenciar catálogo de backup do StorSimple | Microsoft Docs"
description: "Explica como usar a página do catálogo de backup do serviço do Gerenciador de Dispositivos do StorSimple para listar, selecionar e excluir os conjuntos de backup."
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
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Usar o serviço do Gerenciador de Dispositivos StorSimple para gerenciar o catálogo de backup
## <a name="overview"></a>Visão geral
A folha **Catálogo de backup** do serviço do Gerenciador de Dispositivos do StorSimple exibe todos os conjuntos de backup criados após a realização de backups manuais ou agendados. Você pode usar esta página para listar todos os backups para uma política de backup ou volume, selecionar ou excluir os backups, ou usar um backup para restaurar ou clonar um volume.

Este tutorial explica como listar, selecionar e excluir um conjunto de backup. Para saber como restaurar seu dispositivo de backup, vá para [Restaurar seu dispositivo por meio de um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md). Para saber como clonar um volume, vá para [Clonar um volume do StorSimple](storsimple-8000-clone-volume-u2.md).

![Catálogo de Backup](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A folha **Catálogo de Backup** fornece uma consulta para restringir sua seleção de conjunto de backup. Você pode filtrar os conjuntos de backup recuperados com base nos seguintes parâmetros:

* **Dispositivo** – O dispositivo no qual o conjunto de backup foi criado.
* **Política de backup ou Volume** – A política de backup ou volume associado a este conjunto de backup.
* **De e Para** – o intervalo de datas e horas em que o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou do volume associada a este conjunto de backup.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado em** – O intervalo de data e hora quando os backups foram criados. 
* **Tipo** – Conjuntos de Backup podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é um backup de todos os dados do volume armazenadas localmente no dispositivo, enquanto um instantâneo de nuvem refere-se ao backup dos dados do volume que residem na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciada por** – os backups podem ser iniciados de forma automática, de acordo com uma agenda ou de forma manual por um usuário. Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção **Fazer backup** para fazer um backup manual.

## <a name="list-backup-sets-for-a-backup-policy"></a>Listar conjuntos de backup para uma política de backup
Conclua as etapas a seguir para listar todos os backups de uma política de backup.

#### <a name="to-list-backup-sets"></a>Para listar conjuntos de backup
1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Catálogo de backup**.

2. Filtre as seleções da seguinte maneira:
   
   1. Especifique o intervalo de tempo.
   2. Selecione o dispositivo adequado.
   3. Filtre por **Política de backup** para exibir os backups correspondentes.
   3. Na lista suspensa de política de backup, escolha **Tudo** para exibir todos os backups no dispositivo selecionado.
   4. Clique em **Aplicar** para executar esta consulta.
      
      Os backups associados à política de backup selecionada devem aparecer na lista de conjuntos de backup.

      ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Selecione um conjunto de backup
Conclua as seguintes etapas para selecionar um conjunto de backup para uma política de volume ou backup.

#### <a name="to-select-a-backup-set"></a>Para selecionar um conjunto de backups
1. Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Catálogo de backup**.
2. Filtre as seleções da seguinte maneira:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo adequado. 
   3. Filtre por volume ou política de backup para o backup que você deseja selecionar.
   4. Clique em **Aplicar** para executar esta consulta.
      
      Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.

      ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de backups. Agora você pode ver os conjuntos de backup divididos pelos volumes que ele contém. As opções **Restaurar** e **Excluir** estão disponíveis por meio do menu de contexto (clique com o botão direito do mouse) para o conjunto de backup. Você pode executar qualquer uma dessas ações no conjunto de backup selecionado.

    ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Excluir um conjunto de backups
Exclua um backup quando você não quiser mais manter os dados associados a ele. Execute as etapas a seguir para excluir um conjunto de backups.

#### <a name="to-delete-a-backup-set"></a>Para excluir um conjunto de backups
 Acesse o serviço do Gerenciador de Dispositivos do StorSimple e clique em **Catálogo de backup**.
2. Filtre as seleções da seguinte maneira:
   
   1. Especifique o intervalo de tempo. 
   2. Selecione o dispositivo adequado. 
   3. Filtre por volume ou política de backup para o backup que você deseja selecionar.
   4. Clique em **Aplicar** para executar esta consulta.
      
      Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.

      ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Selecione e expanda um conjunto de backups. Agora você pode ver os conjuntos de backup divididos pelos volumes que ele contém. As opções **Restaurar** e **Excluir** estão disponíveis por meio do menu de contexto (clique com o botão direito do mouse) para o conjunto de backup. Clique com botão direito do mouse no conjunto de backup selecionado e selecione **Excluir** no menu de contexto.

    ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Quando sua confirmação for solicitada, leia as informações exibidas e clique em **Excluir**. O backup selecionado será excluído permanentemente.

    ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Você será notificado quando a exclusão estiver em andamento e quando ela for concluída com êxito. Após a exclusão, atualize a consulta nesta página. O conjunto de backup excluído não aparecerá mais na lista de conjuntos de backup.

    ![Acesse o catálogo de backup](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o catálogo de backup para restaurar seu dispositivo de um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md).
* Saiba como [usar o serviço Gerenciador de Dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

