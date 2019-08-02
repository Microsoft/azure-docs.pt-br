---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172109"
---
#### <a name="to-create-a-new-service"></a>Para criar um novo serviço

1.  Usando suas credenciais de conta da Microsoft, entre no Portal do Azure na seguinte URL: <https://portal.azure.com/>. Se for implantar o dispositivo no Portal do governo, faça entre em: <https://portal.azure.us/>

2.  No Portal do Azure **+ Criar um recurso** &gt; **Armazenamento** &gt; **Série Virtual StorSimple**.

    ![Criar novo serviço](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Na folha **StorSimple Device Manager** que é aberta, faça o seguinte:

    1.  Forneça um **Nome do recurso** exclusivo para o serviço. O nome do recurso é um nome amigável que pode ser usado para identificar o serviço. O nome pode ter entre 2 e 50 caracteres que podem ser letras, números e hífens. O nome deve começar e terminar com uma letra ou um número.

    2.  Escolha uma **Assinatura** na lista suspensa. A assinatura está vinculada à sua conta de cobrança. Este campo não estará presente se você tiver apenas uma assinatura.

    3.  Para **Grupo de recursos**, selecione um grupo de recursos existente ou crie um novo. Para obter mais informações, veja [Grupos de recursos do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Fornecer um **Local** para o serviço. Veja [Regiões do Azure](https://azure.microsoft.com/regions/#services) para saber mais sobre quais serviços estão disponíveis em qual região. Em geral, escolha uma **Localização** próxima à região geográfica onde quer implantar o seu dispositivo. Você talvez queira considerar o seguinte:

        -   Se você tiver cargas de trabalho existentes no Azure que também planeja implantar com seu dispositivo StorSimple, recomendamos o uso daquele datacenter.

        -   O StorSimple Device Manager e o Armazenamento do Azure podem estar em duas localizações separadas. Nesse caso, é necessário criar a conta de armazenamento do Azure e do StorSimple Device Manager separadamente. Para criar uma conta de armazenamento do Azure, navegue até o portal do Azure e siga as etapas descritas em [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). Após criar essa conta, adicione-a ao serviço StorSimple Device Manager seguindo as etapas em [Configurar uma nova conta de armazenamento para o serviço](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Se você for implantar o dispositivo virtual no Portal do Governo, o serviço StorSimple Device Manager estará disponível nas localizações de Iowa e Virgínia, nos EUA.

    5.  Selecione **Criar uma nova conta de armazenamento do Azure** para criar automaticamente uma conta de armazenamento com o serviço. Especifique um **Nome de conta de armazenamento**. Se você precisar de seus dados em um local diferente, desmarque essa caixa.

    6.  Marque **Fixar no painel** caso você deseje um link rápido para esse serviço em seu painel.

    7.  Clique em **Criar** para criar o StorSimple Device Manager.

        ![Criar novo serviço](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Você é direcionado para a página inicial **Serviço**. A criação do serviço leva alguns minutos. Depois que o serviço foi criado com êxito, você será notificado adequadamente e o status do serviço será alterado para **Ativo**.


