---
title: Gerenciar credenciais de conta de armazenamento da StorSimple Virtual Array | Microsoft Docs
description: "Explica como você pode usar a página Configurar do StorSimple Device Manager para adicionar, editar, excluir ou girar as chaves de segurança para credenciais de conta de armazenamento associadas à Matriz Virtual StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: a4ce2d329d0e1399cffaf886adf2b95e34b9cd7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Usar o Gerenciador de Dispositivos do StorSimple para gerenciar credenciais da conta de armazenamento para o StorSimple Virtual Array

## <a name="overview"></a>Visão geral
A seção **Configuração** da folha do serviço Gerenciador de Dispositivos do StorSimple da sua Matriz Virtual StorSimple apresenta os parâmetros globais do serviço que podem ser criados no serviço StorSimple Manager. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Registros de controle de acesso
  
  ![Painel do serviço Gerenciador de Dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Este tutorial explica como você pode adicionar, editar ou excluir credenciais de conta de armazenamento para sua Matriz Virtual StorSimple. As informações neste tutorial aplicam-se apenas à Matriz Virtual StorSimple. Para obter informações sobre como gerenciar contas de armazenamento na série 8000, veja [Usar o serviço StorSimple Manager para gerenciar sua conta de armazenamento](storsimple-manage-storage-accounts.md).

As credenciais de conta de armazenamento contêm as credenciais que o dispositivo usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para contas de armazenamento do Microsoft Azure, essas credenciais podem ser o nome da conta e a chave de acesso primário, por exemplo.

Na folha **Credenciais da conta de armazenamento** , todas as credenciais de conta de armazenamento criadas para a assinatura de cobrança são exibidas em formato de tabela contendo as seguintes informações:

* **Nome** – O nome exclusivo atribuído à conta quando a mesma foi criada.
* **SSL habilitado** – Se o SSL está ativado e a comunicação do dispositivo para a nuvem é feita pelo canal seguro.
  
  ![Seção de configuração](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

As tarefas mais comuns relacionadas a credenciais de conta de armazenamento que podem ser executadas na folha **Credenciais de conta de armazenamento** são:

* Adicionar uma credencial de conta de armazenamento
* Editar uma credencial de conta de armazenamento
* Excluir uma credencial de conta de armazenamento

## <a name="types-of-storage-account-credentials"></a>Tipos de credenciais da conta de armazenamento
Há três tipos de credenciais de conta de armazenamento que podem ser usados com o dispositivo StorSimple.

* **Credenciais de conta de armazenamento geradas automaticamente** – como o nome sugere, esse tipo de credencial de conta de armazenamento é gerada automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como essa credencial de conta de armazenamento é criada, confira [Criar um novo serviço](storsimple-virtual-array-manage-service.md#create-a-service).
* **Credenciais de conta de armazenamento na assinatura do serviço** – essas são as credenciais de conta de armazenamento do Azure que estão associadas com a mesma assinatura que o serviço. Para saber mais sobre como essas credenciais de conta de armazenamento são criadas, consulte [Sobre contas do Armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* **Credenciais de conta de armazenamento fora do serviço de assinatura** – essas são as contas do armazenamento do Azure que não estão associadas ao seu serviço e que provavelmente existiam antes da criação do serviço.

## <a name="add-a-storage-account-credential"></a>Adicionar uma credencial de conta de armazenamento
Você pode adicionar uma credencial de conta de armazenamento à configuração de serviço Gerenciador de Dispositivos StorSimple fornecendo um nome amigável exclusivo e credenciais de acesso que são vinculadas à conta de armazenamento. Você também tem a opção de habilitar o modo secure sockets layer (SSL) para criar um canal seguro para comunicação de rede entre o dispositivo e a nuvem.

Você pode criar várias contas para um provedor de serviços de nuvem específico. Enquanto a credencial de conta de armazenamento está sendo salvo, o serviço tenta se comunicar com o seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu são autenticados neste momento. Uma credencial de conta de armazenamento será criada somente se a autenticação for bem-sucedida. Se a autenticação falhar, será exibida uma mensagem de erro apropriada.

Use os procedimentos a seguir para adicionar as credenciais de conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento com a mesma assinatura do Azure como o serviço do Gerenciador de Dispositivos
* Para adicionar uma credencial de conta de armazenamento do Azure que esteja fora da assinatura do serviço Gerenciador de Dispositivos

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento com a mesma assinatura do Azure como o serviço do Gerenciador de Dispositivos

1. Navegue até seu serviço Gerenciador de Dispositivos, selecione e clique duas vezes nele. Isso abre a folha **Visão geral**.
2. Selecione **Credenciais da conta de armazenamento** na seção **Configuração**.
3. Clique em **Adicionar**.
4. Na folha **Adicionar uma conta de armazenamento**, faça o seguinte:
   
    1. Para **Assinatura**, selecione **Atual**.
    2. Forneça o nome da sua conta de armazenamento do Azure.
    3. Selecione **Habilitar** para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a nuvem. Selecione **Desabilitar** somente se você estiver operando em uma nuvem privada.
    4. Clique em **Adicionar**. Você será notificado depois que a conta de armazenamento tiver sido criada com êxito.<br></br>
   
        ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure que esteja fora da assinatura do serviço Gerenciador de Dispositivos

1. Navegue até seu serviço Gerenciador de Dispositivos, selecione e clique duas vezes nele. Isso abre a folha **Visão geral**.
2. Selecione **Credenciais da conta de armazenamento** na seção **Configuração**. Isso lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço Gerenciador de Dispositivos StorSimple.
3. Clique em **Adicionar**.
4. Na folha **Adicionar uma conta de armazenamento**, faça o seguinte:
   
    1. Para **Assinatura**, selecione **Outras**.
   
    2. Forneça o nome da sua credencial de conta de armazenamento do Azure.
   
    3. Na caixa de texto **Chave de acesso da conta de armazenamento**, forneça a chave de acesso primária para suas credenciais de conta de armazenamento do Azure. Para obter essa chave, vá para o serviço de Armazenamento do Azure, escolha sua credencial de conta de armazenamento e clique em **Gerenciar chaves de conta**. Agora você pode copiar a chave de acesso primária.
   
    4. Para habilitar o SSL, clique no botão **Habilitar** para criar um canal seguro para comunicação de rede entre o serviço Gerenciador de Dispositivos do StorSimple e a nuvem. Clique no botão **Desabilitar** somente se você estiver operando em uma nuvem privada.
   
    5. Clique em **Adicionar**. Você será notificado depois que a credencial de conta de armazenamento tiver sido criada com êxito.

5. A credencial de conta de armazenamento criada recentemente é exibida na folha do serviço Gerenciador de Dispositivos do StorSimple em **Credenciais de conta de armazenamento**.
   
    ![Adicionar uma credencial de conta de armazenamento fora da assinatura do serviço Gerenciador de Dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Editar uma credencial de conta de armazenamento
Você pode editar uma credencial de conta de armazenamento usada pelo seu dispositivo. Se você editar uma credencial de conta de armazenamento que esteja atualmente em uso, os campos disponíveis a serem modificados são a chave de acesso e o modo SSL da credencial de conta. Você pode fornecer a nova chave de acesso de armazenamento ou modificar a seleção de **Habilitar modo SSL** e salvar as configurações atualizadas.

#### <a name="to-edit-a-storage-account-credential"></a>Para editar uma credencial de conta de armazenamento
1. Navegue até seu serviço Gerenciador de Dispositivos, selecione e clique duas vezes nele. Isso abre a folha **Visão geral**.
2. Selecione **Credenciais da conta de armazenamento** na seção **Configuração**. Isso lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço Gerenciador de Dispositivos StorSimple.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique duas vezes na conta que você deseja modificar.
4. Na folha **Propriedades** da credencial de conta de armazenamento, faça o seguinte:
   
   1. Se necessário, você poderá modificar a seleção **Habilitar modo SSL** .
   2. Você pode escolher gerar novamente as chaves de acesso da credencial de conta de armazenamento. Para obter mais informações, confira [Regenerar as chaves da conta de armazenamento](../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys). Forneça a nova chave de credencial de conta de armazenamento. Para uma conta de armazenamento do Azure, essa é a chave de acesso principal.
   3. Clique em **Salvar** na parte superior da folha **Propriedades** para salvar as configurações. As configurações são atualizadas na folha **Credenciais de conta de armazenamento**.
      
      ![Editar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Excluir uma credencial de conta de armazenamento
> [!IMPORTANT]
> Você só poderá excluir uma credencial de conta de armazenamento se ela não estiver em uso. Se uma credencial de conta de armazenamento estiver em uso, você será notificado.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Para excluir uma credencial de conta de armazenamento
1. Navegue até seu serviço Gerenciador de Dispositivos, selecione e clique duas vezes nele. Isso abre a folha **Visão geral**.
2. Selecione **Credenciais da conta de armazenamento** na seção **Configuração**. Isso lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço Gerenciador de Dispositivos StorSimple.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique duas vezes na conta que você deseja excluir.
4. Na folha **Propriedades** da credencial de conta de armazenamento, faça o seguinte:
   
   1. Clique em **Excluir** para excluir as credenciais.
   2. Quando solicitado a confirmar, clique em **Sim** para continuar com a exclusão. A listagem de tabela é atualizada para refletir as alterações.
      
      ![Excluir uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Sincronização de chaves de credenciais de conta de armazenamento
Por motivos de segurança, a rotação de chaves é normalmente um requisito em datacenters. Um administrador do Microsoft Azure pode gerar novamente ou alterar a chave primária ou secundária, acessando diretamente a credencial de conta de armazenamento (por meio do serviço Armazenamento do Microsoft Azure). O serviço Gerenciador de Dispositivos StorSimple não vê essa alteração automaticamente.

Para informar o serviço Gerenciador de Dispositivos StorSimple da alteração, será necessário acessar o serviço Gerenciador de Dispositivos StorSimple, acessar a credencial de conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo de qual chave foi alterada). Em seguida, o serviço obtém a chave mais recente, criptografa as chaves e envia a chave criptografada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar chaves para credenciais de conta de armazenamento na mesma assinatura que o serviço (somente Azure)
1. Na folha de aterrissagem do serviço, selecione seu serviço, clique duas vezes no nome do serviço e na seção **Configuração**, clique em **Credenciais da conta de armazenamento**.
2. Na folha **Credenciais e conta de armazenamento**, na lista de Credenciais de conta de armazenamento, selecione a credencial de conta de armazenamento cujas chaves você deseja sincronizar.
3. Na folha **Propriedades** para a credencial de conta de armazenamento selecionada, faça o seguinte:
   
    1. Clique em **Mais** e então clique em **Sincronizar chave de acesso**.
   
    2. Quando a confirmação for solicitada, clique em **Sincronizar chave** para concluir a sincronização.
    
4. No serviço Gerenciador de Dispositivos StorSimple, você precisa atualizar a chave que foi alterada anteriormente no serviço Armazenamento do Microsoft Azure. Na folha **Sincronizar chave de conta de armazenamento**, se a chave de acesso primária tiver sido alterada (regenerada), clique em Primária e em **Sincronizar Chave**. Se a chave secundária tiver sido alterada, clique em **Secundária** e então clique em **Sincronizar Chave**.
   
    ![Sincronizar chave de acesso](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-acess-key.png)

## <a name="next-steps"></a>Próximas etapas
* Aprenda como [administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

