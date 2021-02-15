---
title: Copiar arquivos de vários contêineres
description: Saiba como usar um modelo de solução para copiar arquivos de vários contêineres usando Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376081"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Copiar várias pastas com Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo descreve um modelo de solução que permite usar várias atividades de cópia para copiar contêineres ou pastas entre repositórios baseados em arquivo, em que cada atividade de cópia deve copiar um único contêiner ou pasta. 

> [!NOTE]
> Se você quiser copiar arquivos de um único contêiner, é mais eficiente usar a [ferramenta copiar dados](copy-data-tool.md) para criar um pipeline com uma única atividade de cópia. O modelo neste artigo é mais do que você precisa para esse cenário simples.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo enumera as pastas de uma determinada pasta pai no armazenamento de origem. Em seguida, ele copia cada uma das pastas para o repositório de destino.

O modelo contém três atividades:
- O **GetMetadata** verifica seu armazenamento de origem e obtém a lista de subpastas de uma determinada pasta pai.
- **Foreach** Obtém a lista de subpastas da atividade **GetMetadata** e, em seguida, itera na lista e passa cada pasta para a atividade de cópia.
- **Copiar** copia cada pasta do repositório de armazenamento de origem para o repositório de destino.

O modelo define os seguintes parâmetros:
- *SourceFileFolder* faz parte do caminho da pasta pai do seu repositório de fonte de dados: *SourceFileFolder/SourceFileDirectory*, onde você pode obter uma lista das subpastas. 
- *SourceFileDirectory* faz parte do caminho da pasta pai do seu repositório de fonte de dados: *SourceFileFolder/SourceFileDirectory*, onde você pode obter uma lista das subpastas. 
- *DestinationFileFolder* faz parte do caminho da pasta pai: *DestinationFileFolder/DestinationFileDirectory* em que os arquivos serão copiados para o armazenamento de destino. 
- *DestinationFileDirectory* faz parte do caminho da pasta pai: *DestinationFileFolder/DestinationFileDirectory* em que os arquivos serão copiados para o armazenamento de destino. 

Se você quiser copiar vários contêineres em pastas raiz entre armazenamentos de repositório, poderá inserir todos os quatro parâmetros como */* . Ao fazer isso, você replicará tudo entre repositórios de armazenamento.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **copiar vários arquivos entre os repositórios** de arquivos. Crie uma **nova** conexão com o repositório de armazenamento de origem. O repositório de armazenamento de origem é onde você deseja copiar arquivos de vários contêineres.

    ![Criar uma nova conexão para a origem](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Crie uma **nova** conexão com o repositório de armazenamento de destino.

    ![Criar uma nova conexão para o destino](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Selecione **Usar este modelo**.

    ![Usar este modelo](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Você verá o pipeline, como no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Selecione **Depurar**, insira os **Parâmetros** e, em seguida, selecione **Concluir**.

    ![Executar o pipeline](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Examine o resultado.

    ![Revisar o resultado](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Próximas etapas

- [Cópia em massa de um banco de dados usando uma tabela de controle com Azure Data Factory](solution-template-bulk-copy-with-control-table.md)

- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
