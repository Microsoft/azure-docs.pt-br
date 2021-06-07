---
title: Visão geral de modelos
description: Saiba como usar um modelo predefinido para iniciar rapidamente com Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 8c0e4db2bc686fff2bd718f45c63a0fc26f6cd55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375384"
---
# <a name="templates"></a>Modelos

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Modelos são pipelines predefinidos do Azure Data Factory que permitem iniciar rapidamente com o Data Factory. Os modelos são úteis quando você está iniciando no Data Factory e deseja começar de forma rápida. Esses modelos reduzem o tempo de desenvolvimento na criação de projetos de integração de dados, melhorando a produtividade do desenvolvedor.

## <a name="create-data-factory-pipelines-from-templates"></a>Criar pipelines do Data Factory a partir de modelos

Há duas maneiras para você começar a criar um pipeline do Data Factory a partir de um modelo:

1.  Selecione **Criar pipeline a partir do modelo** na página Visão Geral para abrir a galeria de modelos.

    ![Abra a galeria de modelos na página Visão Geral](media/solution-templates-introduction/templates-intro-image1.png)

1.  Na guia autor do Gerenciador de recursos, selecione **+** e, em seguida, **pipeline de modelo** para abrir a Galeria de modelos.

    ![Abra a galeria de modelos na guia Autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galeria de modelos

![A galeria de modelos](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Modelos do Data Factory prontos para uso

O Data Factory usa modelos do Azure Resource Manager para salvar modelos de pipeline do Data Factory. Você pode ver todos os modelos do Resource Manager, juntamente com o arquivo de manifesto usado para modelos de Data Factory prontos, no [repositório do GitHub oficial Azure data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Os modelos predefinidos fornecidos pela Microsoft incluem os, mas não estão limitados aos, seguintes itens:

-   Copiar modelos:

    -   [Cópia em massa do banco de dados](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiar novos arquivos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar vários contêineres de arquivos entre armazenamentos baseados em arquivo](solution-template-copy-files-multiple-containers.md)

    -   [Mover os arquivos](solution-template-move-files.md)

    -   [Cópia Delta do banco de dados](solution-template-delta-copy-with-control-table.md)

    -   Copiar de \<source\> para \<destination\>

        -   [Do Amazon S3 para o Azure Data Lake Store Gen2](solution-template-migration-s3-azure.md)

        -   Do Google BigQuery para o Azure Data Lake Store Gen2

        -   Do HDF para o Azure Data Lake Store Gen2

        -   Do Netezza para o Azure Data Lake Store Gen1

        -   Do SQL Server local para o Banco de Dados SQL do Azure

        -   Do SQL Server local para o Azure Synapse Analytics

        -   Do Oracle local para o Azure Synapse Analytics

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
