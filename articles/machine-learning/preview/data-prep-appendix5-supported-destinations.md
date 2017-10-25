---
title: "Saídas/destinos de dados com suporte disponíveis com a preparação dos dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa de saídas/destinos com suporte disponíveis para a preparação de dados do Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-exports-for-this-preview"></a>Exportações de dados com suporte para essa versão prévia 
É possível exportar para diversos formatos diferentes. Esses formatos podem ser usados para manter os resultados intermediários de preparação de dados antes da integração com o restante do fluxo de trabalho de Machine Learning.

## <a name="types"></a>Tipos 
### <a name="csv-file"></a>Arquivo CSV 
Gravar um arquivo de valores separados por vírgula no armazenamento

#### <a name="options"></a>Opções
- Terminações de Linha
- Substituir Valores Nulos Por
- Substituir Erros Por 
- Separador


### <a name="parquet"></a>Parquet ###
Grave um conjunto de dados no armazenamento como Parquet.

O Parquet como um formato pode assumir vários formatos no armazenamento. Para conjuntos de dados menores, às vezes é usado um único arquivo ‘.parquet’, várias bibliotecas do Python dão suporte à leitura/gravação em arquivos ‘.parquet’ únicos. Por enquanto, a AMLWB se baseia na biblioteca do Python PyArrow para gravar o Parquet durante o uso ‘interativo’ local. Isso significa que único arquivo Parquet é atualmente o único formato de saída com suporte do Parquet durante o uso interativo local.

Durante as execuções de escala horizontal (no Spark), a AMLWB depende dos recursos de leitura/gravação do Parquet do Spark. O formato de saída padrão do Spark para Parquet (atualmente o único com suporte) é semelhante em termos de estrutura a um conjunto de dados HIVE. Isso significa uma pasta que contém muitos arquivos ‘.parquet’ que são uma partição menor de um conjunto de dados maior. 

#### <a name="caveats"></a>Limitações ####
O Parquet como um formato é relativamente novo e tem algumas inconsistências de implementação entre as diferentes bibliotecas. Por exemplo, Spark impõe restrições em quais caracteres são válidos em Nomes de Colunas ao gravar no Parquet o que a PyArrow não faz. Qualquer caractere no conjunto, " ,;{}()\\n\\t=", não pode estar presente em um Nome de Coluna.

>[!NOTE]
>Para garantir a compatibilidade com o Spark, sempre que dados são gravados no Parquet, as ocorrências desses caracteres Nomes de Coluna são substituídos por ‘_’ (sublinhado).**

>[!NOTE]
>Para garantir a consistência entre os dados locais e com escala horizontal, todos os dados gravados no Parquet, por meio do aplicativo, Python ou Spark, têm os Nomes de Coluna corrigidos para garantir a compatibilidade com o Spark. Para garantir os Nomes de Coluna esperados ao gravar caracteres do Parquet no Sparks, o conjunto inválido deve ser removido das colunas antes da gravação.



## <a name="locations"></a>Locais 
### <a name="local"></a>Local 
Disco rígido local ou local de armazenamento de rede mapeado

### <a name="azure-blob"></a>BLOB do Azure 
O Armazenamento do Azure (BLOB) requer uma assinatura do Azure

