---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171974"
---
### <a name="to-take-a-backup"></a>Para fazer um backup

1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Da listagem de tabela de dispositivos, selecione e clique em seu dispositivo e, em seguida, clique em **Todas as configurações**. Na folha **Configurações**, vá para **Configurações > Gerenciar > Política de backup**.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu1.png)

2. Na folha **Política de backup**, clique em **+ Adicionar política**.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu2.png)

3. Na folha **Criar política de backup**, forneça um nome que contenha entre três e 150 caracteres para sua política de backup.

4. Selecione os volumes a serem incluídos no backup. Se você selecionar mais de um volume, esses volumes serão agrupados para criar um backup com controle de falhas.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu4.png)

5. Na folha **Adicionar primeira agenda**:

    1. Selecione o tipo de backup. Para obter restaurações mais rápidas, selecione instantâneo **Local**. Para obter resiliência de dados, selecione instantâneo de **Nuvem**.
    2. Especifique a frequência de backup em minutos, em horas, em dias ou em semanas.
    3. Selecione um tempo de retenção. As opções de retenção dependem da frequência do backup. Por exemplo, para uma política diária, a retenção pode ser especificada em semanas, enquanto a retenção para uma política mensal é especificada em meses.
    4. Selecione a hora e a data de início para a política de backup.
    5. Clique em **OK** para criar a política de backup.

        ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Clique em **Criar** para iniciar a criação de política de backup. Você será notificado quando a política de backup for criada com êxito. A lista de políticas de backup também é atualizada.
      
      ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Agora você tem uma política de backup que cria backups agendados de seus dados de volume.




