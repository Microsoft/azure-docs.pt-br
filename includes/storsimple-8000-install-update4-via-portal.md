---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a3ccf76b2722c04a9353fcc7020ff1387bc454c6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172125"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Para instalar uma atualização por meio do portal do Azure

1. Na página do serviço StorSimple, selecione seu dispositivo.

    ![Selecionar dispositivo](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Navegue até **Configurações do dispositivo** > **Atualizações de dispositivo**.

    ![Clique em Atualizações de dispositivo](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Uma notificação será exibida se houver novas atualizações disponíveis. Como alternativa, na folha **Atualizações de dispositivo**, clique em **Verificar Atualizações**. Um trabalho é criado para verificar se há atualizações disponíveis. Você será notificado quando o trabalho for concluído com êxito.

    ![Clique em Atualizações de dispositivo](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. É recomendável examinar as notas de versão antes de aplicar uma atualização em seu dispositivo. Para aplicar atualizações, clique em **Instalar atualizações**. Na folha **Confirmar atualizações regulares**, examine os pré-requisitos a serem concluídos antes de aplicar as atualizações. Marque a caixa de seleção para indicar que você está pronto para atualizar o dispositivo e, em seguida, clique em **Instalar**.

    ![Clique em Atualizações de dispositivo](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Um conjunto de verificações de pré-requisito é iniciado. Essas verificações incluem:
   
   * **Verificações de integridade do controlador** para verificar se ambos os controladores do dispositivo estão em boas condições e online.
   * **Verificações de integridade do componente de hardware** para verificar se todos os componentes de hardware no dispositivo StorSimple estão em boas condições.
   * **Verificações de DATA 0** para verificar se DATA 0 está habilitada no dispositivo. Se essa interface não estiver habilitada, você deverá habilitá-la e repetir a ação.

     A atualização só será baixada e instalada se todas as verificações forem concluídas com êxito. Você será notificado quando as verificações estiverem em andamento. Se as verificações prévias falharem, os motivos da falha serão fornecidos. Resolva esses problemas e então repita a operação. Talvez seja necessário entrar em contato com o Suporte da Microsoft se você não conseguir resolver esses problemas por conta própria.

7. Depois que as pré-verificações tiverem sido concluídas com êxito, será criado um trabalho de atualização. Você será notificado quando o trabalho de atualização for criado com êxito.
   
    ![Criação do trabalho de atualização](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    Em seguida, a atualização será aplicada ao seu dispositivo.

9. A atualização demora algumas horas para ser concluída. Selecione o trabalho de atualização e clique em **Detalhes** para exibir os detalhes do trabalho a qualquer momento.

    ![Criação do trabalho de atualização](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Você também pode monitorar o andamento do trabalho de atualização de **Configurações do dispositivo > Trabalhos**. Na folha **Trabalhos**, você pode ver o andamento da atualização.

     ![Criação do trabalho de atualização](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. Após a conclusão do trabalho, navegue até **Configurações do dispositivo > Atualizações do dispositivo**. A versão do software deve ser atualizada.

   ![Criação do trabalho de atualização](./media/storsimple-8000-install-update4-via-portal/update9.png)

