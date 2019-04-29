---
title: Copiar arquivos de vários contêineres usando o Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar arquivos de vários contêineres usando o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/1/2018
ms.openlocfilehash: a52729adf8d6df3f4e44e561b45b854db433628c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635112"
---
# <a name="copy-files-from-multiple-containers-with-azure-data-factory"></a>Copiar arquivos de vários contêineres com o Azure Data Factory

Este artigo descreve um modelo de solução que você pode usar para copiar arquivos de vários contêineres entre armazenamentos de arquivo. Por exemplo, você pode usá-lo para migrar seu lago de dados do AWS S3 para o Azure Data Lake Store. Ou, você pode usar o modelo para replicar tudo de uma conta de armazenamento de BLOBs do Azure para outro.

> [!NOTE]
> Se você quiser copiar arquivos de um único contêiner, é mais eficiente usar a [a ferramenta Copiar dados](copy-data-tool.md) para criar um pipeline com atividade de cópia única. O modelo neste artigo é mais do que você precisa para esse cenário simple.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo enumera os contêineres de seu repositório de armazenamento de origem. Ele, em seguida, copia esses contêineres para o armazenamento de destino.

O modelo contém três atividades:
- **GetMetadata** examina o armazenamento do armazenamento de origem e obtém a lista de contêineres.
- **ForEach** obtém a lista de contêineres do **GetMetadata** atividade e, em seguida, itera através da lista e passa cada contêiner para a atividade de cópia.
- **Cópia** copia cada contêiner do armazenamento do armazenamento de origem para o armazenamento de destino.

O modelo define dois parâmetros:
- *SourceFilePath* é o caminho do seu armazenamento de fonte de dados, onde você pode obter uma lista de contêineres. Na maioria dos casos, o caminho é o diretório raiz, que contém várias pastas de contêiner. O valor padrão desse parâmetro é `/`.
- *DestinationFilePath* é o caminho onde os arquivos serão copiados no seu repositório de destino. O valor padrão desse parâmetro é `/`.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o **copiar vários contêineres de arquivos entre os armazenamentos de arquivo** modelo. Criar uma **New** conexão para o armazenamento do armazenamento de origem. O repositório de armazenamento de origem é onde você deseja copiar arquivos de vários contêineres de.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Criar uma **New** conexão para seu repositório de armazenamento de destino.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Use este modelo**.

    ![Usar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Você verá o pipeline, como no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **Debug**, insira o **parâmetros**e, em seguida, selecione **concluir**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Revise o resultado.

    ![Revisar o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Próximas etapas

- [Cópia em massa de um banco de dados usando uma tabela de controle com o Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)