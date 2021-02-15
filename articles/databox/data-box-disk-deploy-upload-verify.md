---
title: Tutorial para verificar o upload de dados do Azure Data Box Disk para a conta de armazenamento | Microsoft Docs
description: Use este tutorial para saber como verificar os dados carregados de seu Azure Data Box Disk para a conta de armazenamento do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: abe09cf10e241ec95ceed767e7038cde07667fc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322726"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Tutorial: Verificar o upload de dados do Azure Data Box Disk

Este é o último tutorial da série: Implantar o Azure Data Box Disk. Neste tutorial, você aprenderá sobre:

> [!div class="checklist"]
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [Tutorial: Devolução do Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Depois que os discos forem recolhidos pelo serviço de correio, o status do pedido no portal de atualizações será exibido como **Recolhidos**. Uma ID de rastreamento também é exibida.

![Discos escolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando a Microsoft receber e verificar o disco, o status do trabalho será atualizado para **Recebido**. 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são copiados automaticamente quando os discos são conectados a um servidor no datacenter do Azure. Dependendo do tamanho dos dados, a operação de cópia poderá demorar de algumas horas a dias para ser concluída. Você pode monitorar o andamento do trabalho de cópia no portal.

Depois que a cópia for concluída, o status do pedido será atualizado para **Concluído**.

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se a cópia for concluída com erros, confira [Solucionar problemas de upload](data-box-disk-troubleshoot-upload.md).

Verifique se seus dados estão nas contas de armazenamento antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Microsoft Azure. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

  - Para blobs de página e blobs de bloco: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Arquivos do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Como alternativa, você pode acessar a sua conta de armazenamento do Azure no portal do Azure e navegar por meio dela.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blob de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

  - Se a cópia para discos gerenciados no Azure tiver êxito, você poderá acessar os **Detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

      ![Exibir detalhes do pedido](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos anotado e localize os discos gerenciados.

      ![Grupo de recursos para discos gerenciados](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > Caso um blob de páginas não seja convertido com êxito em um disco gerenciado durante uma cópia de dados, ele permanecerá na conta de armazenamento e você será cobrado pelo armazenamento.

  -  Se você copiou um VHDX, ou um VHD diferencial/dinâmico, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de blocos. Acesse sua **conta de armazenamento de preparo > Blobs** e selecione o contêiner apropriado - StandardSSD, StandardHDD ou PremiumSSD. Os VHDX/VHDs devem aparecer como blobs de blocos na conta de armazenamento de preparo.
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Depois que os dados forem carregados no Azure, verifique se os seus dados estão nas contas de armazenamento antes de excluí-los da fonte. Seus dados podem estar em:

- Suas contas de Armazenamento do Microsoft Azure. Quando você copia os dados para o Data Box, dependendo do tipo, eles são carregados em um dos caminhos a seguir em sua conta do Armazenamento do Azure.

    - **Para blobs de blocos e blobs de páginas**: https://<nome_da_conta_de_armazenamento>.blob.core.windows.net/<containername>/files/a.txt

    - **Para Arquivos do Azure**: https://<nome_da_conta_de_armazenamento>.file.core.windows.net/<sharename>/files/a.txt

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como blob de páginas e, em seguida, convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

::: zone-end

Para verificar se os dados foram carregados no Azure, execute as seguintes etapas:

1. Vá para a conta de armazenamento associada ao pedido do disco.
2. Vá para **serviço Blob > Procurar blobs**. A lista de contêineres é apresentada. Correspondente à subpasta que você criou nas pastas *BlockBlob* e *PageBlob*, contêineres com o mesmo nome são criados em sua conta de armazenamento.
    Se os nomes das pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure falhará.

3. Para verificar se todo o conjunto de dados foi carregado, use o Gerenciador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente ao pedido do Data Box Disk e, em seguida, observe a lista de contêineres de blob. Escolha um contêiner, clique em **...Mais** e, em seguida, clique em **Estatísticas da pasta**. No painel **Atividades**, as estatísticas dessa pasta, incluindo o número de blobs e o tamanho total do blob são exibidos. O tamanho total do blob em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas da pasta no Gerenciador de Armazenamento](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminar dados do Data Box Disk

Após a conclusão do upload no Azure, o Data Box Disk apaga os dados nos discos de acordo com o padrão [NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Verificar o carregamento de dados para o Azure
> * Eliminar dados do Data Box Disk


Avance para a próxima instrução para saber como gerenciar o Data Box Disk por meio do portal do Azure.

> [!div class="nextstepaction"]
> [Usar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end




