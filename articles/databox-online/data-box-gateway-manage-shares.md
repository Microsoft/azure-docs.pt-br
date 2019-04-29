---
title: Azure Data Box Gateway, gerenciar compartilhamentos | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar compartilhamentos em seu Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9284400254860b47f3aea6de5c79ab4c2a77f199
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755671"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-gateway"></a>Use o portal do Azure para gerenciar compartilhamentos em seu Azure Data Box Gateway 

Este artigo descreve como gerenciar compartilhamentos em seu Azure Data Box Gateway. Você pode gerenciar o Azure Data Box Gateway usando o portal do Azure ou a interface do usuário da Web local. Use o portal do Azure para adicionar, excluir, atualizar compartilhamentos ou sincronizar a chave de armazenamento para conta de armazenamento associada com os compartilhamentos.

## <a name="about-shares"></a>Sobre compartilhamentos

Para transferir dados para o Azure, você precisa criar compartilhamentos em seu Azure Data Box Gateway. Os compartilhamentos que você adiciona ao dispositivo do Data Box Gateway compartilhamentos em nuvem. Os dados nesses compartilhamentos são carregados automaticamente para a nuvem. Todas as funções de nuvem, como Atualizar e Sincronizar as chaves de armazenamento se aplicam a esses compartilhamentos. Use os compartilhamentos de nuvem quando você desejar que os dados de dispositivo sejam enviados automaticamente para sua conta de armazenamento na nuvem.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Adicionar um compartilhamento
> * Excluir um compartilhamento
> * Atualizar compartilhamentos
> * Sincronizar chave de armazenamento


## <a name="add-a-share"></a>Adicionar um compartilhamento

Execute as etapas a seguir no portal do Azure para criar um compartilhamento.

1. No portal do Azure, vá até seu recurso do Data Box Gateway e navegue até **Visão geral**. Clique em **+ Adicionar compartilhamento** na barra de comandos.
2. Em **Adicionar compartilhamento**, especifique as configurações de compartilhamento. Forneça um nome exclusivo para seu compartilhamento.

    ![Clique em adicionar compartilhamento](media/data-box-gateway-manage-shares/add-share-1.png)

    Os nomes de compartilhamentos só podem conter números, letras minúsculas e hifens. O nome do compartilhamento deve ter entre 3 e 63 caracteres e começar com uma letra ou um número. Cada hífen deve ser precedido e seguido por um caractere que não seja um hífen.

3. Escolha um **Tipo** para o compartilhamento. O tipo pode ser **SMB** ou **NFS**, sendo SMB o padrão. SMB é o padrão para clientes do Windows e NFS é usado para clientes Linux. Dependendo da escolha entre compartilhamentos SMB ou NFS, as opções apresentadas serão ligeiramente diferentes.

4. Forneça um **Conta de armazenamento** na qual o compartilhamento resida. Um contêiner será criado na conta de armazenamento com o nome do compartilhamento se o contêiner já não existir. Se o contêiner já existir, ele será usado.

5. Escolha o **Serviço de armazenamento** entre blob de blocos, blobs de página ou arquivos. O tipo do serviço escolhido depende do formato escolhido para os dados que residirão no Azure. Por exemplo, nesta instância, queremos que os dados residam como blocos de blob no Azure e, portanto, vamos escolher o **Blob de Blocos**. Se estiver escolhendo **Blob de Páginas**, você precisará fazer com que seus dados sejam alinhados com 512 bytes. Por exemplo, um VHDX sempre é alinhado com 512 bytes.

6. Esta etapa depende do tipo de compartilhamento criado, SMB ou NFS.
    - **Se estiver criando compartilhamento SMB**: no campo **Todos os usuários locais com privilégios**, escolha **Criar novo** ou **Usar existente**. Se a criação for de um novo usuário local, forneça o **nome de usuário**, a **senha** e então confirme a senha. Isso atribui as permissões ao usuário local. Depois de atribuir as permissões aqui, você pode usar o Gerenciador de Arquivos para modificar essas permissões.

        ![Adicionar compartilhamento SMB](media/data-box-gateway-manage-shares/add-share-2.png)

        Se você marcar a opção Permitir apenas operações de leitura para esses compartilhamento de dados, poderá especificar usuários somente leitura.
    - **Se a criação for de um compartilhamento NFS**: você precisará fornecer os **endereços IP dos clientes permitidos** que podem acessar o compartilhamento.

        ![Adicionar compartilhamento NFS](media/data-box-gateway-manage-shares/add-share-3.png)

7. Clique em **Criar** para criar o compartilhamento. Você será notificado de que a criação do compartilhamento está em andamento. Depois que o compartilhamento for criado com as configurações especificadas, a folha **Compartilhamentos** será atualizada para refletir o novo compartilhamento.
 
## <a name="delete-a-share"></a>Excluir um compartilhamento

Para excluir um compartilhamento, siga estas etapas no portal do Azure.

1. Na lista de compartilhamentos, selecione e clique no compartilhamento que você deseja excluir.

    ![Selecionar compartilhamento](media/data-box-gateway-manage-shares/delete-1.png)

2. Clique em **Excluir**. 

    ![Clique em Excluir](media/data-box-gateway-manage-shares/delete-2.png)

3. Quando solicitado a confirmar, clique em **Sim**.

    ![Confirmar exclusão](media/data-box-gateway-manage-shares/delete-3.png)

A lista de compartilhamentos é atualizada para refletir a exclusão.


## <a name="refresh-shares"></a>Atualizar compartilhamentos

O recurso de atualização permite que você atualize o conteúdo de um compartilhamento local. Quando você atualiza um compartilhamento, uma pesquisa é iniciada para localizar todos os objetos do Azure, incluindo os blobs e arquivos adicionados à nuvem desde a última atualização. Esses arquivos adicionais então são usados para atualizar o conteúdo do compartilhamento local no dispositivo. 

> [!NOTE]
> As permissões e ACLs (listas de controle de acesso) não são preservadas em uma operação de atualização. 

Para atualizar um compartilhamento, siga estas etapas no portal do Azure.

1.  No portal do Azure, acesse **Compartilhamentos**. Selecione e clique no compartilhamento que você deseja atualizar.

    ![Selecionar compartilhamento](media/data-box-gateway-manage-shares/refresh-1.png)

2.  Clique em **Atualizar**. 

    ![Clique em atualizar](media/data-box-gateway-manage-shares/refresh-2.png)
 
3.  Quando solicitado a confirmar, clique em **Sim**. Um trabalho começa a atualizar o conteúdo do compartilhamento local. 

    ![Confirmar atualização](media/data-box-gateway-manage-shares/refresh-3.png)
 
4.  Enquanto a atualização está em andamento, a opção de atualização fica esmaecida no menu de contexto. Clique na notificação de trabalho para exibir o status do trabalho de atualização.

5.  O tempo para atualização depende do número de arquivos no contêiner do Azure, bem como dos arquivos no dispositivo. Depois que a atualização tiver sido concluída com êxito, o carimbo de data/hora de compartilhamento será atualizado. Mesmo que a atualização tenha falhas parciais, a operação será considerada bem-sucedida e o carimbo de data/hora será atualizado. 

    ![Carimbo de data/hora atualizado](media/data-box-gateway-manage-shares/refresh-4.png)
 
Se houver uma falha, um alerta será gerado. O alerta detalha a causa e a recomendação para corrigir o problema. O alerta também fornece links para um arquivo que tem o resumo completo das falhas, incluindo os arquivos que falharam em ser atualizados ou excluídos.

>[!IMPORTANT]
> Nesta liberação de versão prévia, não atualize mais de um único compartilhamento por vez.

## <a name="sync-storage-keys"></a>Sincronizar chaves de armazenamento

Se tiver sido feita a rotação das chaves da conta de armazenamento, você precisará sincronizar as chaves de acesso de armazenamento. A sincronização ajuda o dispositivo obter as chaves mais recente para sua conta de armazenamento.

Execute as seguintes etapas no portal do Azure para sincronizar sua chave de acesso de armazenamento.

1. Vá para **Visão Geral** em seu recurso. 
2. Na lista de compartilhamentos, escolha e clique em um compartilhamento associado com a conta de armazenamento que você precisa sincronizar. Clique em **Sincronizar chave de armazenamento**. 

     ![Sincronizar chave de armazenamento 1](media/data-box-gateway-manage-shares/sync-storage-key-1.png)

3. Clique em **Sim** quando solicitada a confirmação. Saia da caixa de diálogo depois que a sincronização tiver sido concluída.

     ![Sincronizar chave de armazenamento 1](media/data-box-gateway-manage-shares/sync-storage-key-2.png)

>[!NOTE]
> Você precisa fazer isso apenas uma vez para uma conta de armazenamento específica. Você não precisa repetir essa ação para todos os compartilhamentos associados com a mesma conta de armazenamento.


## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar usuários usando o portal do Azure](data-box-gateway-manage-users.md).
