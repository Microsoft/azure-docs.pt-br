---
title: Visão geral de modelos para Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo predefinido para iniciar rapidamente com Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60786898"
---
# <a name="templates"></a>Modelos

Modelos são pipelines predefinidos do Azure Data Factory que permitem iniciar rapidamente com o Data Factory. Os modelos são úteis quando você está iniciando no Data Factory e deseja começar de forma rápida. Esses modelos reduzem o tempo de desenvolvimento na criação de projetos de integração de dados, melhorando a produtividade do desenvolvedor.

## <a name="create-data-factory-pipelines-from-templates"></a>Criar pipelines do Data Factory a partir de modelos

Há duas maneiras para você começar a criar um pipeline do Data Factory a partir de um modelo:

1.  Selecione **Criar pipeline a partir do modelo** na página Visão Geral para abrir a galeria de modelos.

    ![Abra a galeria de modelos na página Visão Geral](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na guia Autor no Gerenciador de Recursos, selecione **+** e, em seguida, **Pipeline do modelo** para abrir a galeria de modelos.

    ![Abra a galeria de modelos na guia Autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria de modelos

![A galeria de modelos](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Modelos do Data Factory prontos para uso

O Data Factory usa modelos do Azure Resource Manager para salvar modelos de pipeline do Data Factory. Você pode ver todos os modelos do Gerenciador de recursos, juntamente com o arquivo de manifesto usados para fora os modelos de fábrica de dados de caixa, nos [repositório do GitHub do Azure Data Factory oficial](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Os modelos predefinidos fornecidos pela Microsoft incluem os, mas não estão limitados aos, seguintes itens:

-   Copiar modelos:

    -   [Cópia em massa do Banco de Dados](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiar novos arquivos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar vários contêineres de arquivos entre armazenamentos baseados em arquivo](solution-template-copy-files-multiple-containers.md)

    -   [Cópia delta do Banco de Dados](solution-template-delta-copy-with-control-table.md)

    -   Copiar da \<origem\> para o \<destino\>

        -   Do Amazon S3 para o Azure Data Lake Store Gen2

        -   Do Google BigQuery para o Azure Data Lake Store Gen2

        -   Do HDF para o Azure Data Lake Store Gen2

        -   Do Netezza para o Azure Data Lake Store Gen1

        -   Do SQL Server local para o Banco de Dados SQL do Azure

        -   Do SQL Server local para o SQL Data Warehouse do Azure

        -   Do Oracle local para o SQL Data Warehouse do Azure

-   Modelos do SSIS

    -   Agendar o Azure-SSIS Integration Runtime para executar pacotes SSIS

-   Transformar modelos

    -   [ETL com Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meus Modelos

Também é possível salvar um pipeline como um modelo, selecionando **Salvar como modelo** na guia Pipeline.

![Salvar um pipeline como modelo](media/solution-templates-introduction/templates-intro-image4.png)

Você pode exibir os pipelines salvos como modelos na seção **Meus Modelos** da Galeria de Modelos. Também é possível vê-los na seção **Modelos** no Gerenciador de Recursos.

![Meus modelos](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Para usar o recurso Meus Modelos, é necessário habilitar a integração GIT. Há suporte para GIT do Azure DevOps e GitHub.
