---
title: Restaurar um volume do StorSimple de um backup | Microsoft Docs
description: "Explica como usar a página Catálogo de Backup do serviço StorSimple Manager para restaurar um volume do StorSimple de um conjunto de backup."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 12484338f5b4d489604d70a657ef0992b6267297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Restaurar um volume do StorSimple de um conjunto de backup
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Visão geral
A página **Catálogo de Backup** exibe todos os conjuntos de backup criados após a realização de backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou volume, selecionar ou excluir os backups, ou usar um backup para restaurar ou clonar um volume.

 ![Página Catálogo de Backup](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Este tutorial explica como usar a página **Catálogo de Backup** para restaurar um volume em seu dispositivo a partir de um conjunto de backup.

## <a name="how-to-use-the-backup-catalog"></a>Como usar o catálogo de backup
A página **Catálogo de Backup** oferece uma consulta que ajuda a restringir sua seleção de conjuntos de backup. Você pode filtrar os conjuntos de backup recuperados com base nos seguintes parâmetros:

* **Dispositivo** – O dispositivo no qual o conjunto de backup foi criado.
* **Política** ou **volume** de Backup – A política ou volume de backup associado a este conjunto de backup.
* **De** e **Para** – O intervalo de datas e horas em que o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

* **Nome** – O nome da política de backup ou do volume associada a este conjunto de backup.
* **Tamanho** – O tamanho real do conjunto de backup.
* **Criado em** – O intervalo de data e hora quando os backups foram criados. 
* **Tipo** – Conjuntos de Backup podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é um backup de todos os dados do volume armazenadas localmente no dispositivo, enquanto um instantâneo de nuvem refere-se ao backup dos dados do volume que residem na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto os instantâneos de nuvem são escolhidos para resiliência de dados.
* **Iniciada por** – Os backups podem ser iniciados de forma automática, de acordo com uma agenda, ou de forma manual por um usuário. (Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção **Fazer backup** para fazer um backup interativo).

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Como restaurar o volume StorSimple de um backup
É possível usar a página **Catálogo de Backup** para restaurar o volume do StorSimple de um backup específico. 

> [!WARNING]
> A restauração de um backup substituirá os volumes existentes do backup. Isso pode causar a perda de todos os dados gravados depois que o backup tiver sido feito.
> 
> 

Antes de iniciar uma restauração em um volume, verifique se o volume está offline. Você precisará colocar o volume offline no host primeiro e, em seguida, no dispositivo. Siga as etapas em [Colocar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline). Execute as etapas a seguir para restaurar um volume de um conjunto de backup.

### <a name="to-restore-from-a-backup-set"></a>Para restaurar de um conjunto de backup
1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup** .
   
    ![Catálogo de Backup](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. Selecione um conjunto de backup desta maneira:
   
   1. Selecione o dispositivo adequado.
   2. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.
   3. Especifique o intervalo de tempo.
   4. Clique no ícone de verificação  ![ícone de verificação](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) para executar esta consulta.
      
      Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.
3. Expanda o conjunto de backup para exibir os volumes associados. Esses volumes devem ficar offline no host e no dispositivo antes que você possa restaurá-los. Siga as etapas em [Colocar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline).
   
   > [!IMPORTANT]
   > Verifique se você colocou os volumes offline primeiro no host antes de colocar os volumes offline no dispositivo. Se você não colocar os volumes offline no host, poderá ocorrer corrupção nos dados.
   > 
   > 
4. Selecione um conjunto de backup. Clique em **Restaurar** na parte inferior da página.
5. Será solicitada a sua confirmação. 
   
    ![Página de confirmação](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. Examine as informações de restauração e clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Isso iniciará um trabalho de restauração que poderá ser exibido por meio do acesso à página **Trabalhos** . 
7. Após a conclusão da restauração, você poderá verificar se o conteúdo de seus volumes foi substituído pelos volumes do backup.

![Vídeo disponível](./media/storsimple-restore-from-backup-set/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como você pode usar os recursos de clonagem e restauração no StorSimple para recuperar arquivos excluídos, clique [aqui](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Próximas etapas
* Saiba como [Gerenciar volumes do StorSimple](storsimple-manage-volumes.md).
* Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

