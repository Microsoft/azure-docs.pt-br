---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d6bad1ec584b28fee77bc654eba8a2d0c7b5df30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632527"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento na mesma assinatura do Azure que o serviço StorSimple Device Manager

1. Vá até o seu serviço do Gerenciador de Dispositivos StorSimple. Na seção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. Na folha **Credenciais da conta de armazenamento**, clique em **+ Adicionar**.

    ![Adicionar uma credencial de conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. Na folha **Adicionar credencial de uma conta de armazenamento**, execute estas etapas:

    1. Como você está adicionando uma credencial de conta de armazenamento à mesma assinatura do Azure de seu serviço, certifique-se de que **Atual** esteja selecionado.

    2. Na lista suspensa **conta de armazenamento**, selecione uma conta de armazenamento existente.

    3. Com base na conta de armazenamento selecionada, o **local** será exibido (esmaecido, e não poderá ser alterado aqui).

    4. Selecione **Habilitar modo SSL** para criar um canal seguro para comunicação de rede entre o dispositivo e a nuvem. Desabilite **Habilitar SSL** somente se você estiver operando em uma nuvem privada.

        ![Folha Adicionar credenciais da conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. Clique em **Adicionar** para iniciar a criação do trabalho para a credencial de conta de armazenamento. Você receberá uma notificação após a criação da credencial de conta de armazenamento.

        ![Notificação de êxito para as credenciais de conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

A credencial da conta de armazenamento recém-criada será exibida na lista de **Credenciais de conta de armazenamento**.

![Lista de credenciais de conta de armazenamento](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

