---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172112"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Para adicionar uma política de backup do StorSimple

1. Acesse seu dispositivo StorSimple e clique em **Política de backup**.

2. Na folha **Política de backup**, clique em **+ Adicionar política** na barra de comandos.
   
    ![Adicionar uma política de backup](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Na folha **Criar política de backup**, execute as seguintes etapas:
   
   1. **Selecionar dispositivo** é preenchido automaticamente com base no dispositivo selecionado.
   
   2. Especifique um **Nome de política** de backup que contenha de três a 150 caracteres. Depois que a política é criada, você não pode renomeá-la.
       
   3. Para atribuir volumes a essa política de backup, selecione **Adicionar volumes** e na lista tabular de volumes, clique nas caixas de seleção para atribuir um ou mais volumes a essa política de backup.

       ![Adicionar uma política de backup](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Para definir uma agenda para essa política de backup, clique em **Primeiro agenda** e modifique os seguintes parâmetros:

       ![Adicionar uma política de backup](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. Para **Tipo de instantâneo**, selecione **Nuvem** ou **Local**.

       2. Indique a frequência dos backups (Especifique um número e escolha **Dias** ou **Semanas** na lista suspensa.

       3. Insira um agendamento de retenção.

       4. Insira o dia e a hora de início da política de backup.

       5. Clique em **OK** para definir a agenda.

   5. Clique em **Criar** para criar uma política de backup.

       ![Adicionar uma política de backup](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Você receberá uma notificação quando a política de backup for criada. A política recém-adicionada será exibida no modo de exibição tabular, na folha **Políticas de Backup**.

       ![Adicionar uma política de backup](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

