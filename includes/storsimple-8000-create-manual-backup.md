---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171997"
---
#### <a name="to-create-a-manual-backup"></a>Para criar um backup manual

1. Acesse o serviço StorSimple Device Manager e clique em **Dispositivos**. Na lista tabular de dispositivos, selecione seu dispositivo. Acesse **Configurações > Gerenciar > Políticas de backup**.

2. A folha **Políticas de backup** lista todas as políticas de backup em um formato tabular, incluindo a política para o volume do qual você deseja fazer backup. Selecione a política associada ao volume do qual você deseja fazer backup e clique com o botão direito para invocar o menu de contexto. Na lista suspensa, selecione **Fazer backup agora**.

    ![Criar o backup manual](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. Na folha **Fazer backup agora**, execute as seguintes etapas:

    1. Escolha o **tipo de Instantâneo** adequado no menu suspenso: Instantâneo **local** ou instantâneo **De nuvem**. Selecione instantâneo local para backups ou restaurações rápidas, e instantâneo de nuvem para resiliência de dados.

        ![Criar o backup manual](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Clique em **OK** para iniciar um trabalho a fim de criar um instantâneo. Você verá uma notificação na parte superior da página após a criação do trabalho.

        ![Criar o backup manual](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Para monitorar o trabalho, clique na notificação. Isso levará você até a folha **Trabalhos** onde poderá exibir o andamento do trabalho.


5. Depois que o trabalho de backup for concluído, vá para a guia **Catálogo de backup** .

6. Defina as seleções de filtro para o dispositivo apropriado, a política de backup e o intervalo de tempo. O backup deve aparecer na lista de conjuntos de backup que é exibida no catálogo.

