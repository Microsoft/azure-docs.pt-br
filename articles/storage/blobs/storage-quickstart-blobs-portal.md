---
title: Início rápido – Criar um blob com o portal do Azure
titleSuffix: Azure Storage
description: Neste início rápido, você usa o portal do Azure no armazenamento de objeto (Blob). Em seguida, use o portal do Azure para carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95523324"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Início Rápido: Carregar, baixar e listar blobs com o portal do Azure

Neste guia de início rápido, você aprenderá a usar o [portal do Azure](https://portal.azure.com/) para criar um contêiner no Armazenamento do Azure e a carregar e baixar blobs de blocos nesse contêiner.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner no portal do Azure, siga estas etapas:

1. Navegue até sua nova conta de armazenamento no portal do Azure.
2. No menu à esquerda da conta de armazenamento, role a página até a seção **Serviço Blob** e, em seguida, selecione **Contêineres**.
3. Selecione o botão **+ Contêiner**.
4. Digite um nome para o novo contêiner. O nome do contêiner deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-). Para obter mais informações sobre nomes de contêiner e de blobs, confira [Nomeando e referenciando contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Defina o nível de acesso público ao contêiner. O nível padrão é **Privado (sem acesso anônimo)** .
6. Selecione **OK** para criar o contêiner.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Captura de tela mostrando como criar um contêiner no portal do Azure":::

## <a name="upload-a-block-blob"></a>Carregar um blob de blocos

Blobs de bloco consistem em blocos de dados montados para criar um blob. A maioria dos cenários que usa o Armazenamento de blobs emprega blobs de blocos. Os blobs de blocos são ideais para armazenar texto e dados binários na nuvem, como arquivos, imagens e vídeos. Este guia de início rápido mostra como trabalhar com blobs de blocos.

Para carregar um blob de blocos para seu novo contêiner no portal do Azure, siga estas etapas:

1. No portal do Azure, navegue até o contêiner que você criou na seção anterior.
1. Selecione o contêiner para mostrar uma lista de blobs que ele contém. Esse contêiner é novo, então ele ainda não contém blobs.
1. Selecione o botão **Carregar** para abrir a folha de mesmo nome e procure seu sistema de arquivos local para encontrar um arquivo a ser carregado como um blob de blocos. Opcionalmente, você pode expandir a seção **Avançado** para definir outras configurações para a operação de upload.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Captura de tela mostrando como carregar um blob da unidade local por meio do portal do Azure":::

1. Selecione o botão **Carregar** para carregar o blob.
1. Carregue quantos blobs desejar usando essa maneira. Você verá que os novos blobs agora estão listados dentro do contêiner.

## <a name="download-a-block-blob"></a>Baixar um blob de blocos

Você pode baixar um blob de blocos para exibir no navegador ou salvar em seu sistema de arquivos local. Para baixar um blob de blocos, siga estas etapas:

1. Navegue até a lista de blobs que você carregou na seção anterior.
1. Clique com botão direito do mouse no blob que você deseja baixar e selecione **Baixar**.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Captura de tela mostrando como baixar um blob no portal do Azure":::

## <a name="delete-a-block-blob"></a>Excluir um blob de blocos

Para excluir um ou mais blobs no portal do Azure, siga estas etapas:

1. No portal do Azure, navegue até o contêiner.
1. Exiba a lista de blobs no contêiner.
1. Use a caixa de seleção para selecionar um ou mais blobs da lista.
1. Selecione o botão **Excluir** para excluir os blobs selecionados.
1. Na caixa de diálogo, confirme a exclusão e indique se você também deseja excluir os instantâneos do blob.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Captura de tela que mostra como excluir blobs no portal do Azure":::

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos criados neste guia de início rápido, basta excluir o contêiner. Todos os blobs no contêiner também serão excluídos.

Para excluir o contêiner:

1. No portal do Azure, navegue até a lista de contêineres na sua conta de armazenamento.
1. Selecione o contêiner a ser excluído.
1. Selecione o botão **Mais** ( **...** ) e selecione **Excluir**.
1. Confirme que você realmente deseja excluir o contêiner.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprenderá como criar um contêiner e carregar um blob com o portal do Azure. Para saber mais sobre como trabalhar com o Armazenamento de blobs de um aplicativo Web, continue em um tutorial que mostra como carregar imagens para uma conta de armazenamento.

> [!div class="nextstepaction"]
> [Tutorial: Carregar dados de imagem na nuvem com o Armazenamento do Azure](storage-upload-process-images.md)