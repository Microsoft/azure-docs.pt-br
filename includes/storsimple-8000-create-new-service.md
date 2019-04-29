---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 54a1bc9ce04cf10575b26cda7861c1dd458dc831
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506458"
---
#### <a name="to-create-a-new-service"></a>Para criar um novo serviço

1. Use suas credenciais de conta da Microsoft para iniciar sessão no [portal do Azure](https://portal.azure.com/).

2. No portal do Azure, clique em **Criar um recurso** e, no marketplace, clique em **Ver tudo**.

    ![Criar Gerenciador de Dispositivo StorSimple](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Procure _StorSimple Físico_. Selecione e clique em **Série de Dispositivos Físicos StorSimple** e então clique em **Criar**. Como alternativa, no portal do Azure, clique em **+** e, em **Armazenamento**, clique em **Série de Dispositivos Físicos StorSimple**.

    ![Criar Gerenciador de Dispositivo StorSimple](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. Na folha **StorSimple Device Manager**, siga estas etapas:
   
   1. Forneça um **Nome do recurso** exclusivo para o serviço. Este é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 2 e 50 caracteres que podem ser letras, números e hífens. O nome deve começar e terminar com uma letra ou um número.

   2. Escolha uma **Assinatura** na lista suspensa. A assinatura está vinculada à sua conta de cobrança. Este campo não estará presente se você tiver apenas uma assinatura.

   3. Para **Grupo de recursos**, **Usar existente** ou **Criar novo** grupo. Para obter mais informações, veja [Grupos de recursos do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).
   
   4. Fornecer um **Local** para o serviço. Em geral, escolha um local próximo à região geográfica onde quer implantar os seus serviços. Você talvez queira considerar o seguinte: 
      
      * Se você tiver cargas de trabalho existentes no Azure que também planeja implantar com seu dispositivo StorSimple, use aquele datacenter.
      * Seu serviço Gerenciador de Dispositivos StorSimple e o Armazenamento do Azure podem estar em dois locais separados. Nesse caso, é necessário criar a conta de armazenamento do Azure e do StorSimple Device Manager separadamente. Para criar uma conta de armazenamento do Azure, acesse o serviço Armazenamento do Azure no Portal do Azure e siga as etapas em [Criar uma conta de Armazenamento do Azure](../articles/storage/common/storage-quickstart-create-account.md). Após criar essa conta, adicione-a ao serviço StorSimple Device Manager seguindo as etapas em [Configurar uma nova conta de armazenamento para o serviço](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service).

   5. Selecione **Criar uma nova conta de armazenamento** para criar automaticamente uma conta de armazenamento com o serviço. Especifique um nome para essa conta de armazenamento. Se você precisar de seus dados em um local diferente, desmarque essa caixa.

   6. Marque **Fixar no painel** caso você deseje um link rápido para esse serviço em seu painel.
      
   7. Clique em **Criar** para criar o StorSimple Device Manager.

       ![Criar Gerenciador de Dispositivo StorSimple](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
A criação do serviço leva alguns minutos. Depois que o serviço for criado com êxito, você verá uma notificação e a nova folha de serviço será aberta.
   
![Criar Gerenciador de Dispositivo StorSimple](./media/storsimple-8000-create-new-service/createssdevman5.png)


