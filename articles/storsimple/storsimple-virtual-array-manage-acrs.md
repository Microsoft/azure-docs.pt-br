---
title: Gerenciar registros de controle de acesso para o StorSimple Virtual Array | Microsoft Docs
description: Descreve como gerenciar registros de controle de acesso (ACRs) para determinar quais hosts podem se conectar a um volume na matriz Virtual StorSimple.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Usar o Gerenciador de Dispositivos do StorSimple para gerenciar os registros de controle de acesso para o StorSimple Virtual Array

## <a name="overview"></a>Visão geral

Registros de controle de acesso (ACRs) permitem que você especifique quais hosts podem se conectar a um volume na matriz de StorSimple Virtual (também conhecida como o local no dispositivo virtual StorSimple). Os ACRs são definidos para um volume específico e contêm os IQNs (Nomes Qualificados iSCSI) dos hosts. Quando um host tenta se conectar a um volume, o dispositivo verifica o nome do IQN no ACR associado a esse volume e, se houver correspondência, a conexão será estabelecida. A folha **Registros de controle de acesso** da seção de **Configuração** de seu serviço Gerenciador de Dispositivo exibe todos os registros de controle de acesso com os IQN correspondentes dos hosts.

![Gerenciar registros de controle de acesso](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Este tutorial explica as seguintes tarefas comuns relacionadas ao ACR:

* Obter o IQN
* Adicionar um registro de controle de acesso
* Editar um registro de controle de acesso
* Excluir um registro de controle de acesso

> [!IMPORTANT]
> 
> * Ao atribuir um ACR a um volume, lembre-se que o volume não é acessado simultaneamente por mais de um host não clusterizado porque isso poderia corromper o volume.
> * Ao excluir um ACR de um volume, certifique-se de que o host correspondente não esteja acessando o volume porque a exclusão poderia resultar em uma interrupção de leitura/gravação.


## <a name="get-the-iqn"></a>Obter o IQN

Execute as seguintes etapas para obter o IQN de um host do Windows que está executando o Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adicionar um ACR

Use a folha **Registros de controle de acesso** da seção **Configuração** de seu serviço Gerenciador de Dispositivos do StorSimple para adicionar ACRs. Normalmente, você associa um ACR a um volume.

Para obter informações sobre como associar um ACR a um volume, vá para [Adicionar um volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Ao atribuir um ACR a um volume, lembre-se que o volume não é acessado simultaneamente por mais de um host não clusterizado porque isso poderia corromper o volume.


Execute as etapas a seguir para adicionar um ACR.

#### <a name="to-add-an-acr"></a>Para adicionar um ACR

1. Na página de aterrissagem do serviço, selecione seu serviço, clique duas vezes no nome do serviço e na seção **Configuração**e clique em **Registros de controle de acesso**.
2. Na folha **Registros de controle de acesso**, clique em **Adicionar**.
3. Na folha **Adicionar ACR**, faça o seguinte:
   
    1. Dê um **Nome** para o seu ACR.
    
    2. Forneça o nome IQN do host do Windows em **Nome do Iniciador iSCSI**. Para obter o IQN do host do Windows Server, siga este procedimento:
   
    3. Inicie o iniciador Microsoft iSCSI no host do Windows. Na janela Propriedades do Iniciador iSCSI, na guia **Configuração**, selecione e copie a cadeia de caracteres do campo **Nome do Iniciador**.
    Cole essa cadeia de caracteres no campo **IQN**, na folha **Adicionar ACR**.
   
    6. Clique em **Adicionar** para adicionar a ACR.  
   
        ![Adicionar registros de controle de acesso](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A listagem de tabela será atualizada para refletir essa adição.

## <a name="edit-an-acr"></a>Editar um ACR

Use a folha **Registros de controle de acesso** da seção **Configuração** de seu serviço Gerenciador de Dispositivos no Portal do Azure para editar ACRs.

> [!NOTE]
> Não modifique um ACR em uso no momento. Para editar um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.


Execute as etapas a seguir para editar um ACR.

#### <a name="to-edit-an-acr"></a>Para editar um ACR

1. Na página de aterrissagem do serviço, selecione seu serviço, clique duas vezes no nome do serviço e, na seção **Configuração**, **Registros de controle de acesso**.
2. Na folha **Registros de controle de acesso**, na listagem de tabela dos registros de controle de acesso, clique duas vezes no ACR que deseja modificar.
3. Na folha **Editar registros de controle de acesso**, faça o seguinte:
   
    1. Forneça o IQN do ACR.
   
    2. Clique em **Salvar** na parte superior da folha para salvar o ACR modificado. Você verá a seguinte mensagem de confirmação:
   
        ![Editar os registros de controle de acesso](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Excluir um registro de controle de acesso

Use a página **Configuração** no Portal do Azure para excluir ACRs.

> [!NOTE]
> 
> * Não exclua um ACR em uso no momento. Para excluir um ACR associado a um volume que esteja em uso no momento, primeiramente, você deverá colocar o volume no estado offline.
> * Ao excluir um ACR de um volume, certifique-se de que o host correspondente não esteja acessando o volume porque a exclusão poderia resultar em uma interrupção de leitura/gravação.


Execute as etapas a seguir para excluir um registro de controle de acesso.

#### <a name="to-delete-an-access-control-record"></a>Para excluir um registro de controle de acesso

1. Na página de aterrissagem do serviço, selecione seu serviço, clique duas vezes no nome do serviço e, na seção **Configuração**, **Registros de controle de acesso**.

2. Na folha **Registros de controle de acesso**, na listagem de tabela dos registros de controle de acesso, clique duas vezes no ACR que deseja excluir.

3. Na folha Editar registros de controle de acesso, clique em **Excluir**.
   
    ![Excluir ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Ao receber uma solicitação pela confirmação, clique em **Excluir** para continuar com a exclusão. A listagem de tabela é atualizada para refletir a exclusão.
   
   ![Mensagem de aviso](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Adicionar volumes e configurar ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

