---
title: Gerenciar registros de controle de acesso no StorSimple | Microsoft Docs
description: Descreve como usar os ACRs (registros de controle de acesso) para determinar quais hosts podem se conectar a um volume no dispositivo StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Usar o serviço StorSimple Manager para gerenciar registros de controle de acesso

## <a name="overview"></a>Visão geral
Os ACRs (registros de controle de acesso) permitem especificar quais hosts podem se conectar a um volume no dispositivo StorSimple. Os ACRs são definidos para um volume específico e contêm os IQNs (Nomes Qualificados iSCSI) dos hosts. Quando um host tenta se conectar a um volume, o dispositivo verifica o nome do IQN no ACR associado a esse volume e, se houver correspondência, a conexão será estabelecida. Os registros de controle de acesso da seção **Configuração** da folha do serviço do Gerenciador de Dispositivos do StorSimple exibe todos os registros de controle de acesso com os IQNs correspondentes dos hosts.

Este tutorial explica as seguintes tarefas comuns relacionadas ao ACR:

* Adicionar um registro de controle de acesso
* Editar um registro de controle de acesso
* Excluir um registro de controle de acesso

> [!IMPORTANT]
> * Ao atribuir um ACR a um volume, lembre-se que o volume não é acessado simultaneamente por mais de um host não clusterizado porque isso poderia corromper o volume.
> * Ao excluir um ACR de um volume, certifique-se de que o host correspondente não esteja acessando o volume porque a exclusão poderia resultar em uma interrupção de leitura/gravação.

## <a name="get-the-iqn"></a>Obter o IQN

Execute as seguintes etapas para obter o IQN de um host do Windows que está executando o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Adicionar um registro de controle de acesso
Use a seção **Configuração** na folha do serviço do Gerenciador de Dispositivos do StorSimple para adicionar ACRs. Normalmente, você associará um ACR a um volume.

Execute as etapas a seguir para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Acesse seu serviço do Gerenciador de Dispositivos do StorSimple, clique duas vezes no nome do serviço e, na seção **Configuração**, clique em **Registros de controle de acesso**.
2. Na folha **Registros de controle de acesso**, clique em **+ Adicionar ACR**.

    ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Na folha **Adicionar ACR**, execute as seguintes etapas:

    1. Informe um nome para o ACR.
    
    2. Forneça o nome IQN do host do Windows Server em **Nome do Iniciador iSCSI (IQN)**.

    3. Clique em **Adicionar** para criar o ACR.

        ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  O ACR recém-adicionado será exibido na listagem tabular de ACRs.

    ![Clique em Adicionar ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Editar um registro de controle de acesso
Use a seção **Configuração** na folha do serviço do Gerenciador de Dispositivos do StorSimple para editar ACRs.

> [!NOTE]
> É recomendável modificar somente os ACRs que não estão em uso no momento. Para editar um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.

Execute as etapas a seguir para editar um ACR.

#### <a name="to-edit-an-access-control-record"></a>Para editar um registro de controle de acesso
1.  Acesse seu serviço do Gerenciador de Dispositivos do StorSimple, clique duas vezes no nome do serviço e, na seção **Configuração**, clique em **Registros de controle de acesso**.

    ![Acessar os registro de controle de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na listagem tabular dos registros de controle de acesso, clique e selecione o ACR que você deseja modificar.

    ![Editar os registros de controle de acesso](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Na folha **Editar registro de controle de acesso**, forneça um IQN diferente que corresponde a outro host.

    ![Editar os registros de controle de acesso](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Clique em **Salvar**. Quando solicitado a confirmar, clique em **Sim**. 

    ![Editar os registros de controle de acesso](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Você será notificado quando o ACR for atualizado. A listagem tabular também é atualizada para refletir as alterações.

   
## <a name="delete-an-access-control-record"></a>Excluir um registro de controle de acesso
Use a seção **Configuração** na folha do serviço do Gerenciador de Dispositivos do StorSimple para excluir ACRs.

> [!NOTE]
> Você pode excluir somente os ACRs que não estejam em uso no momento. Para excluir um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.

Execute as etapas a seguir para excluir um registro de controle de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para excluir um registro de controle de acesso
1.  Acesse seu serviço do Gerenciador de Dispositivos do StorSimple, clique duas vezes no nome do serviço e, na seção **Configuração**, clique em **Registros de controle de acesso**.

    ![Acessar os registro de controle de acesso](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Na listagem tabular dos registros de controle de acesso, clique e selecione o ACR que você deseja excluir.

    ![Acessar os registro de controle de acesso](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Clique com o botão direito do mouse para invocar o menu de contexto e selecione **Excluir**.

    ![Acessar os registro de controle de acesso](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Quando sua confirmação for solicitada, leia as informações e clique em **Excluir**.

    ![Acessar os registro de controle de acesso](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Você será notificado quando a exclusão for concluída. A listagem de tabela é atualizada para refletir a exclusão.

    ![Acessar os registro de controle de acesso](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [como gerenciar volumes do StorSimple](storsimple-8000-manage-volumes-u2.md).
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

