---
title: Gerenciamento de compartilhamento do Azure Data Box Edge | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar compartilhamentos no Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 73bff460db8428332a92d8deb68bf062ca4134ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758857"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Usar o portal do Azure para gerenciar compartilhamentos no Azure Data Box Edge

Este artigo descreve como gerenciar compartilhamentos no Azure Data Box Edge. Você pode gerenciar o Azure Data Box Edge por meio do portal do Azure ou da IU da Web local. Use o portal do Azure para adicionar, excluir, atualizar compartilhamentos ou sincronizar a chave de armazenamento para a conta de armazenamento associada aos compartilhamentos.

## <a name="about-shares"></a>Sobre compartilhamentos

Para transferir dados para o Azure, você precisará criar compartilhamentos no Azure Data Box Edge. Os compartilhamentos adicionados ao dispositivo Data Box Edge podem ser compartilhamentos locais ou compartilhamentos que enviam dados por push para a nuvem.

 - **Compartilhamentos locais**: Use esses compartilhamentos quando desejar que os dados sejam processados localmente no dispositivo.
 - **Compartilhamentos**: Use esses compartilhamentos quando desejar que os dados do dispositivo sejam enviados automaticamente por push para a sua conta de armazenamento na nuvem. Todas as funções de nuvem, como **Atualizar** e **Sincronizar chaves de armazenamento**, se aplicam a esses compartilhamentos.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Excluir um compartilhamento
> * Atualizar compartilhamentos
> * Sincronizar chave de armazenamento


## <a name="add-a-share"></a>Adicionar um compartilhamento

Execute as etapas a seguir no portal do Azure para criar um compartilhamento.

1. No portal do Azure, acesse seu recurso do Data Box Edge e, em seguida, acesse **Gateway > Compartilhamentos**. Selecione **+ Adicionar compartilhamento** na barra de comandos.

    ![Selecionar Adicionar compartilhamento](media/data-box-edge-manage-shares/add-share-1.png)

2. Em **Adicionar compartilhamento**, especifique as configurações de compartilhamento. Forneça um nome exclusivo para seu compartilhamento.
    
    Os nomes de compartilhamentos só podem conter números, letras minúsculas e hifens. O nome do compartilhamento deve ter entre 3 e 63 caracteres e começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen.

3. Escolha um **Tipo** para o compartilhamento. O tipo pode ser **SMB** ou **NFS**, sendo SMB o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux. Dependendo da escolha entre compartilhamentos SMB ou NFS, as opções apresentadas serão ligeiramente diferentes.

4. Forneça um **Conta de armazenamento** na qual o compartilhamento resida. Um contêiner será criado na conta de armazenamento com o nome do compartilhamento se o contêiner já não existir. Se o contêiner já existir, ele será usado.

5. Na lista suspensa, escolha o **Serviço de armazenamento** entre blob de blocos, blobs de páginas ou arquivos. O tipo do serviço escolhido depende do formato escolhido para os dados que residirão no Azure. Por exemplo, nessa instância, queremos que os dados residam como blobs de blocos no Azure, portanto, selecionamos **Blob de Blocos**. Se você escolher **Blob de Páginas**, precisará garantir que os dados sejam alinhados com 512 bytes. Use **Blob de páginas** para VHDs ou VHDX que são sempre alinhados com 512 bytes.

6. Esta etapa depende do tipo de compartilhamento criado, SMB ou NFS.
    - **Se estiver criando compartilhamento SMB**: no campo **Todos os usuários locais com privilégios**, escolha **Criar novo** ou **Usar existente**. Se a criação for de um novo usuário local, forneça o **nome de usuário**, a **senha** e então confirme a senha. Isso atribui as permissões ao usuário local. Depois de atribuir as permissões aqui, você pode usar o Gerenciador de Arquivos para modificar essas permissões.

        ![Adicionar compartilhamento SMB](media/data-box-edge-manage-shares/add-smb-share.png)

        Se você marcar a opção Permitir apenas operações de leitura para esses compartilhamento de dados, poderá especificar usuários somente leitura.
    - **Se a criação for de um compartilhamento NFS**: você precisará fornecer os **endereços IP dos clientes permitidos** que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Para acessar com facilidade os compartilhamentos por meio dos módulos de computação de borda, use o ponto de montagem local. Selecione **Usar o compartilhamento com a Computação de borda** para que o compartilhamento seja montado automaticamente depois que ele é criado. Quando essa opção é selecionada, o módulo de borda também pode usar a computação com o ponto de montagem local.

8. Clique em **Criar** para criar o compartilhamento. Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, a folha **Compartilhamentos** será atualizada para refletir o novo compartilhamento.

## <a name="add-a-local-share"></a>Adicionar um compartilhamento local

1. No portal do Azure, acesse seu recurso do Data Box Edge e, em seguida, acesse **Gateway > Compartilhamentos**. Selecione **+ Adicionar compartilhamento** na barra de comandos.

    ![Selecionar Adicionar compartilhamento](media/data-box-edge-manage-shares/add-local-share-1.png)

2. Em **Adicionar compartilhamento**, especifique as configurações de compartilhamento. Forneça um nome exclusivo para seu compartilhamento.
    
    Os nomes de compartilhamentos só podem conter números, letras minúsculas e hifens. O nome do compartilhamento deve ter entre 3 e 63 caracteres e começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen.

3. Escolha um **Tipo** para o compartilhamento. O tipo pode ser **SMB** ou **NFS**, sendo SMB o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux. Dependendo da escolha entre compartilhamentos SMB ou NFS, as opções apresentadas serão ligeiramente diferentes.

4. Para acessar com facilidade os compartilhamentos por meio dos módulos de computação de borda, use o ponto de montagem local. Selecione **Usar o compartilhamento com a computação de borda** para que o módulo do Edge possa usar a computação com o ponto de montagem local.

5. Selecione **Configurar como compartilhamentos locais do Edge**. Os dados em compartilhamentos locais permanecerão localmente no dispositivo. Você pode processar esses dados localmente.

6. No campo **Todos os usuários locais com privilégios**, escolha **Criar** ou **Usar existente**.

7. Selecione **Criar**. 

    ![Criar um compartilhamento local](media/data-box-edge-manage-shares/add-local-share-2.png)

    Você verá uma notificação indicando que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, a folha **Compartilhamentos** será atualizada para refletir o novo compartilhamento.

    ![Exibir atualizações da folha Compartilhamentos](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Selecione o compartilhamento para exibir o ponto de montagem local para os módulos de computação de borda para esse compartilhamento.

    ![Exibir detalhes do compartilhamento local](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Montar um compartilhamento

Se você criou um compartilhamento antes de configurar a computação em seu dispositivo Data Box Edge, você precisará montar o compartilhamento. Siga as etapas a seguir para montar um compartilhamento.


1. No portal do Azure, acesse seu recurso do Data Box Edge e, em seguida, acesse **Gateway > Compartilhamentos**. Na lista de compartilhamentos, selecione o compartilhamento que você deseja montar. A coluna **Usados para computação** mostrará o status como **Desabilitado** para o compartilhamento selecionado.

    ![Selecionar compartilhamento](media/data-box-edge-manage-shares/select-share-mount.png)

2. Selecione **Montar**.

    ![Selecione montar](media/data-box-edge-manage-shares/select-mount.png)

3. Quando solicitado a confirmar, selecione **Sim**. Isso montará o compartilhamento.

    ![Confirme a montagem](media/data-box-edge-manage-shares/confirm-mount.png)

4. Depois que o compartilhamento estiver montado, vá para a lista de compartilhamentos. Você verá que a coluna **Usados para computação** mostra o status do compartilhamento como **Habilitado**.

    ![Compartilhamento montado](media/data-box-edge-manage-shares/share-mounted.png)

5. Selecione o compartilhamento novamente para exibir o ponto de montagem local para o compartilhamento. O módulo de computação de Borda usa esse ponto de montagem local para o compartilhamento.

    ![Ponto de montagem local para o compartilhamento](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Desmontar um compartilhamento

Execute as etapas a seguir no portal do Azure para desmontar um compartilhamento.

1. No portal do Azure, acesse seu recurso do Data Box Edge e, em seguida, acesse **Gateway > Compartilhamentos**.

    ![Selecionar compartilhamento](media/data-box-edge-manage-shares/select-share-unmount.png)

2. Na lista de compartilhamentos, selecione o compartilhamento que você deseja desmontar. Você deseja certificar-se de que o compartilhamento que você desmontar não é usado por todos os módulos. Se o compartilhamento for usado por um módulo, então você verá problemas com o módulo correspondente. Selecione **Desmontar**.

    ![Selecione desmontar](media/data-box-edge-manage-shares/select-unmount.png)

3. Quando solicitado a confirmar, selecione **Sim**. Isso desmontará o compartilhamento.

    ![Confirmar desmontagem](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Depois que o compartilhamento estiver desmontado, vá para a lista de compartilhamentos. Você verá que a coluna **Usados para computação** mostra o status do compartilhamento como **Desabilitado**.

    ![Compartilhamento desmontado](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Excluir um compartilhamento

Para excluir um compartilhamento, siga estas etapas no portal do Azure.

1. Na lista de compartilhamentos, selecione e clique no compartilhamento que você deseja excluir.

    ![Selecionar compartilhamento](media/data-box-edge-manage-shares/delete-share-1.png)

2. Clique em **Excluir**.

    ![Clique em Excluir](media/data-box-edge-manage-shares/delete-share-2.png)

3. Quando solicitado a confirmar, clique em **Sim**.

    ![Confirmar exclusão](media/data-box-edge-manage-shares/delete-share-3.png)

A lista de compartilhamentos é atualizada para refletir a exclusão.


## <a name="refresh-shares"></a>Atualizar compartilhamentos

O recurso de atualização permite atualizar o conteúdo de um compartilhamento. Quando você atualiza um compartilhamento, uma pesquisa é iniciada para localizar todos os objetos do Azure, incluindo os blobs e arquivos adicionados à nuvem desde a última atualização. Esses arquivos adicionais são então baixados para atualizar o conteúdo do compartilhamento no dispositivo.

> [!IMPORTANT]
> - Não é possível atualizar compartilhamentos locais.
> - As permissões e ACLs (listas de controle de acesso) não são preservadas em uma operação de atualização. 

Para atualizar um compartilhamento, siga estas etapas no portal do Azure.

1.  No portal do Azure, acesse **Compartilhamentos**. Selecione e clique no compartilhamento que você deseja atualizar.

    ![Selecionar compartilhamento](media/data-box-edge-manage-shares/refresh-share-1.png)

2.  Clique em **Atualizar**. 

    ![Clique em atualizar](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.  Quando solicitado a confirmar, clique em **Sim**. Um trabalho começa a atualizar o conteúdo do compartilhamento local.

    ![Confirmar atualização](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.  Enquanto a atualização está em andamento, a opção de atualização fica esmaecida no menu de contexto. Clique na notificação de trabalho para exibir o status do trabalho de atualização.

5.  O tempo para atualização depende do número de arquivos no contêiner do Azure, bem como dos arquivos no dispositivo. Depois que a atualização tiver sido concluída com êxito, o carimbo de data/hora de compartilhamento será atualizado. Mesmo que a atualização tenha falhas parciais, a operação será considerada bem-sucedida e o carimbo de data/hora será atualizado. Os logs de erros de atualização também são atualizados.

    ![Carimbo de data/hora atualizado](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Se houver uma falha, um alerta será gerado. O alerta detalha a causa e a recomendação para corrigir o problema. O alerta também fornece links para um arquivo que tem o resumo completo das falhas, incluindo os arquivos que falharam em ser atualizados ou excluídos.


## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Se tiver sido feita a rotação das chaves da conta de armazenamento, você precisará sincronizar as chaves de acesso de armazenamento. A sincronização ajuda o dispositivo obter as chaves mais recente para sua conta de armazenamento.

Execute as seguintes etapas no portal do Azure para sincronizar sua chave de acesso de armazenamento.

1. Vá para **Visão Geral** em seu recurso. Na lista de compartilhamentos, escolha e clique em um compartilhamento associado com a conta de armazenamento que você precisa sincronizar.

    ![Selecione o compartilhamento com a conta de armazenamento relevante](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Clique em **Sincronizar chave de armazenamento**. Clique em **Sim** quando solicitada a confirmação.

     ![Selecionar Sincronizar chave de armazenamento](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. Saia da caixa de diálogo depois que a sincronização tiver sido concluída.

>[!NOTE]
> Você precisa fazer isso apenas uma vez para uma conta de armazenamento específica. Você não precisa repetir essa ação para todos os compartilhamentos associados com a mesma conta de armazenamento.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar usuários usando o portal do Azure](data-box-edge-manage-users.md).
