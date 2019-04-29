---
title: Carregar arquivos em uma conta dos Serviços de Mídia do Azure usando o REST | Microsoft Docs
description: Saiba como obter o conteúdo de mídia nos serviços de mídia ao criar e carregar ativos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: a241f66adecbab1d0b1462f379d3765d6c1de252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466760"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar arquivos em uma conta dos Serviços de Mídia usando o REST

Nos Serviços de Mídia, você pode carregar seus arquivos digitais em contêiner de blob associado a um ativo. A entidade [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos). Depois que os arquivos são carregados no contêiner do ativo, o conteúdo é armazenado com segurança na nuvem para processamento e transmissão adicionais.

Este artigo mostra como carregar um arquivo local usando REST.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas descritas neste tópico, você precisa:

- Revisão [conceito ativo](assets-concept.md).
- [Configurar o Postman para chamadas à API REST de Serviços de Mídia do Azure](media-rest-apis-with-postman.md).
    
    Certifique-se de seguir a última etapa no tópico [Obter token do Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Criar um ativo

Esta seção mostra como criar um novo ativo.

1. Selecione **Ativos** -> **Criar ou atualizar um ativo**.
2. Pressione **Enviar**.

    ![Criar um ativo](./media/upload-files/postman-create-asset.png)

Você vê a **resposta** com as informações sobre o ativo criado recentemente.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obter uma URL de SAS com permissões de leitura-gravação 

Esta seção mostra como obter uma URL de SAS que foi gerado para o ativo criado. A URL de SAS foi criada com permissões de leitura-gravação e pode ser usada para carregar arquivos digitais em contêiner do ativo.

1. Selecione **Ativos** -> **Listar as URLs de ativo**.
2. Pressione **Enviar**.

    ![Carregar um arquivo](./media/upload-files/postman-create-sas-locator.png)

Você vê a **resposta** com as informações sobre a URL criada recentemente. Copie a primeira URL e use-a para carregar seu arquivo.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carregar um arquivo para o armazenamento de blob usando a URL de carregamento

Use os SDKs ou as APIs de Armazenamento do Azure (por exemplo, a [API REST de Armazenamento](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [SDK do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Codificar um arquivo remoto baseado em URL e transmitir o vídeo – REST](stream-files-tutorial-with-rest.md)
