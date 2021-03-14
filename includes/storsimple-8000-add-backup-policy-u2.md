---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551549"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Para adicionar uma política de backup do StorSimple

1. Acesse seu dispositivo StorSimple e clique em **Política de backup**.

2. Na folha **Política de backup**, clique em **+ Adicionar política** na barra de comandos.
   
    ![Adicionar uma política de backup](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. Na folha **Criar política de backup**, execute as seguintes etapas:
   
   1. **Selecionar dispositivo** é preenchido automaticamente com base no dispositivo selecionado.
   
   2. Especifique um **nome de política** de backup que tenha de 3 a 150 caracteres. Depois que a política é criada, você não pode renomeá-la.
       
   3. Para atribuir volumes a essa política de backup, selecione **Adicionar volumes** e na lista tabular de volumes, clique nas caixas de seleção para atribuir um ou mais volumes a essa política de backup.

       ![Adicionar uma política 2 de backup](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Para definir uma agenda para essa política de backup, clique em **Primeiro agenda** e modifique os seguintes parâmetros:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Adicionar uma política de backup 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Para **Tipo de instantâneo**, selecione **Nuvem** ou **Local**.

       2. Indique a frequência dos backups (especifique um número e escolha **dias** ou **semanas** na lista suspensa.

       3. Insira um agendamento de retenção.

       4. Insira o dia e a hora de início da política de backup.

       5. Clique em **OK** para definir a agenda.

   5. Clique em **Criar** para criar uma política de backup.
   
   6. Você receberá uma notificação quando a política de backup for criada. A política recém-adicionada será exibida no modo de exibição tabular, na folha **Políticas de Backup**.

       ![Adicionar uma política de backup 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
