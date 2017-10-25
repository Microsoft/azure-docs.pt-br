---
title: Assistente de Fonte de Dados do azure para o Azure Machine Learning | Microsoft Docs
description: Explica o assistente de fonte de dados do workbench AML
author: cforbe
ms.author: cforbe
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: bed026e8f75618403efa3eed475371d1d2746bc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-source-wizard"></a>Assistente de Fonte de Dados #

O Assistente de Fonte de Dados é uma maneira rápida e fácil de levar um conjunto de dados para o Azure ML Workbench sem código. É nele que você também pode selecionar uma estratégia de exemplo para o conjunto de dados. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Etapa 1: disparar o assistente de fonte de dados ## 

Levar dados para um projeto usando o assistente de fonte de dados. Selecione o botão **+** ao lado da caixa de pesquisa na exibição de dados e escolha Adicionar Fonte de Dados. 

![adicionar fonte de dados](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Etapa 2: Selecionar o local para armazenar os dados ##
Primeiro, especifique como os dados estão no momento. Eles podem estar armazenados em um arquivo simples ou em um diretório, um arquivo parquet, um arquivo do Excel ou um banco de dados. Consulte [Fontes de dados com suporte](data-prep-appendix2-supported-data-sources.md) para obter mais informações.

![etapa 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Etapa 3: Selecionar o arquivo de dados ##
Para um arquivo/diretório, especifique o caminho do arquivo. Escolha na lista suspensa o local dos dados – pode ser um caminho de arquivo local, um Armazenamento de Blobs do Azure ou o Azure Data Lake. 

Especifique o caminho digitando-o ou clicando no botão **Procurar…** para procurá-lo. Você pode procurar um diretório ou um ou mais arquivos.

Clique em **Concluir** para aceitar os padrões para o restante das etapas ou em Avançar para prosseguir para a próxima etapa.


![etapa 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Etapa 4: Escolher parâmetros de arquivo ##

O Assistente de Fonte de Dados pode detectar automaticamente o tipo de arquivo, os separadores e a codificação. Também mostraria um exemplo da aparência dos dados. Você também pode alterar qualquer um desses parâmetros manualmente. 

![etapa 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Etapa 5: Definir tipos de dados para colunas ##

O Assistente de Fonte de Dados detecta automaticamente os tipos de dados das colunas do conjunto de dados. Se perder um ou você desejar impor um tipo de dados, você poderá alterar manualmente o tipo de dados. A coluna **EXEMPLO DE DADOS DE SAÍDA** mostra exemplos da aparência dos dados.

![etapa 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Etapa 6: Escolher a estratégia de amostragem de dados ##

Você pode especificar uma ou mais estratégias de amostragem para o conjunto de dados e escolher uma como a estratégia ativa. O padrão é carregar as 10.000 linhas principais. Você pode editar este exemplo clicando no botão **Editar** na barra de ferramentas ou adicionar uma nova estratégia clicando em +Novo. As estratégias que têm suporte no momento são

-     Número máximo de linhas
-     Número aleatório de linhas
-     Percentual aleatório de linhas
-     Arquivo completo

Você pode especificar quantas estratégias de amostragem desejar, mas apenas uma pode ser definida como ativa durante a preparação de dados. Você pode definir qualquer estratégia como ativa selecionando-a e clicando em Definir como Ativa na barra de ferramentas.

Dependendo da origem dos dados, algumas estratégias de exemplo podem não ter suporte. Para obter mais informações sobre amostragem, consulte a seção de amostragem [neste documento](data-prep-user-guide.md) 

![etapa 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Etapa 7: Tratamento de coluna de caminho ##

Se o caminho do arquivo incluir dados importantes, você poderá escolher incluí-los como a primeira coluna no conjunto de dados. Isso seria útil se você estivesse trazendo vários arquivos. Caso contrário, você pode optar por não incluí-lo.

![etapa 7](media/data-source-wizard/step6.png)

Depois de clicar em Concluir, uma nova fonte de dados será adicionada ao projeto. Você pode encontrá-la no grupo Fontes de Dados na Exibição de Dados ou como um arquivo .dsource na **Exibição de Arquivo**.
