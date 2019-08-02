---
title: Mover arquivos entre o armazenamento baseado em arquivo usando Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para mover arquivos entre o armazenamento baseado em arquivo usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/12/2019
ms.openlocfilehash: 9eb82a23aac5a98a521976118c1e859d0be253d0
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881237"
---
# <a name="move-files-with-azure-data-factory"></a>Mover arquivos com Azure Data Factory

Este artigo descreve um modelo de solução que você pode usar para mover arquivos de uma pasta para outra entre repositórios baseados em arquivo. Um dos cenários comuns de uso deste modelo: Os arquivos são continuamente descartados para uma pasta de aterrissagem do seu armazenamento de origem. Ao criar um gatilho de agendamento, o pipeline do ADF pode mover periodicamente esses arquivos da origem para o repositório de destino.  A maneira como o pipeline do ADF atinge "movendo arquivos" está obtendo os arquivos da pasta de aterrissagem, copiando cada um deles para outra pasta no repositório de destino e, em seguida, excluindo os mesmos arquivos da pasta de aterrissagem no repositório de origem.

> [!NOTE]
> Lembre-se de que esse modelo foi projetado para mover arquivos em vez de mover pastas.  Se você quiser mover a pasta alterando o conjunto de um para que ela contenha apenas um caminho de pasta e, em seguida, usando a atividade de cópia e excluir a referência ao mesmo conjunto de um que representa uma pasta, você precisa ter muito cuidado. Isso porque você tem que ter certeza de que NÃO haverá novos arquivos entrando na pasta entre as operações de cópia e exclusão. Se houver novos arquivos chegando à pasta no momento em que a atividade de cópia tiver acabado o trabalho de cópia, mas a atividade de exclusão ainda não tiver sido iniciada, é possível que a atividade Excluir exclua esse novo arquivo que esteja entrando e que ainda NÃO foi copiado para o destino ao excluir a pasta inteira.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo obtém os arquivos do armazenamento baseado em arquivo de origem. Em seguida, ele move cada um deles para o repositório de destino.

O modelo contém cinco atividades:
- **GetMetadata** Obtém a lista de objetos, incluindo os arquivos e subpastas de sua pasta no repositório de origem. Ele não recuperará os objetos recursivamente. 
- Filtro filtrar a lista de objetos da atividade **GetMetadata** para selecionar apenas os arquivos. 
- **Foreach** Obtém a lista de arquivos da atividade de **filtro** e, em seguida, itera na lista e passa cada arquivo para a atividade de cópia e a atividade de exclusão.
- **Copiar** copia um arquivo da origem para o repositório de destino.
- **Excluir** exclui o mesmo arquivo do repositório de origem.

O modelo define dois parâmetros:
- *FolderPath_SourceStore* é o caminho da pasta do seu repositório de origem para o qual você deseja mover arquivos. 
- *FolderPath_DestinationStore* é o caminho da pasta do armazenamento de destino para o qual você deseja mover os arquivos. 

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Vá para o modelo **mover arquivos** . Selecione conexão existente ou crie uma **nova** conexão com o repositório de arquivos de origem para o qual você deseja mover arquivos. Lembre-se de que **DataSource_Folder** e **DataSource_File** são referências à mesma conexão do seu repositório de arquivos de origem.

    ![Criar uma nova conexão para a origem](media/solution-template-move-files/move-files1.png)

2. Selecione conexão existente ou crie uma **nova** conexão com o repositório de arquivos de destino para o qual você deseja mover os arquivos.

    ![Criar uma nova conexão para o destino](media/solution-template-move-files/move-files2.png)

3. Selecione **usar este modelo**.

    ![Use este modelo](media/solution-template-move-files/move-files3.png)
    
4. Você verá o pipeline, como no exemplo a seguir:

    ![Mostrar o pipeline](media/solution-template-move-files/move-files4.png)

5. Selecione **depurar**, insira os **parâmetros**e, em seguida, selecione **concluir**.   Os parâmetros são o caminho da pasta para onde você deseja mover os arquivos e o caminho da pasta para onde você deseja mover os arquivos. 

    ![Executar o pipeline](media/solution-template-move-files/move-files5.png)

6. Revise o resultado.

    ![Revisar o resultado](media/solution-template-move-files/move-files6.png)

## <a name="next-steps"></a>Próximas etapas

- [Copiar arquivos novos e alterados por LastModifiedDate com Azure Data Factory](solution-template-copy-new-files-lastmodifieddate.md)

- [Copiar arquivos de vários contêineres com Azure Data Factory](solution-template-copy-files-multiple-containers.md)