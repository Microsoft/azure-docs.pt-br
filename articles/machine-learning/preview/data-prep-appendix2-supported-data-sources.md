---
title: "Fontes de dados com suporte disponíveis com a preparação dos dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa de fontes de dados com suporte disponíveis para a preparação de dados do Azure ML"
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
ms.date: 09/12/2017
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-for-this-release"></a>Fontes de dados com suporte para esta versão 
O documento a seguir descreve a lista de fontes de dados com suporte no momento na preparação de dados.

As fontes de dados com suporte para essa versão estão listadas abaixo.

## <a name="types"></a>Tipos 
### <a name="directory-vs-file"></a>Diretório vs arquivo
Arquivos/diretórios – escolha um único arquivo e leia-o na preparação de dados, o tipo de arquivo é analisado para determinar os parâmetros padrão para a conexão de arquivo que está na próxima tela. Escolha um diretório ou conjunto de arquivos em um diretório (o seletor de arquivos tem seleção múltipla), usando qualquer um dos resultados da abordagem nos arquivos sendo lidos como um único fluxo de dados com os arquivos anexados entre si (com cabeçalhos eliminados, se necessário)

Os tipos de arquivo com suporte são:
- Delimitados (csv, tsv, txt etc.), 
- Largura fixa
- Texto sem formatação
- Arquivo JSON

### <a name="csv-file"></a>Arquivo CSV
Ler um arquivo de valores separados por vírgula do armazenamento

#### <a name="options"></a>Opções
- Separador
- Comentário
- Cabeçalhos
- Símbolo decimal
- Codificação de arquivo
- Linhas a serem ignoradas

### <a name="tsv-file"></a>Arquivos TSV
Ler um arquivo de Valores Separados por Tabulação do armazenamento

#### <a name="options"></a>Opções
- Comentário
- Cabeçalhos
- Codificação de arquivo
- Linhas a serem ignoradas

### <a name="excel-xlsxlsx"></a>Excel (.xls/xlsx)
Ler um arquivo do Excel, uma planilha por vez, especificando o nome ou o número da planilha

#### <a name="options"></a>Opções
- Nome/número da planilha
- Cabeçalhos
- Linhas a serem ignoradas

### <a name="json-file"></a>Arquivo JSON
Ler um arquivo JSON do armazenamento, observe que o arquivo é "deixado bidimensional" na leitura

#### <a name="options"></a>Opções
- Nenhum

### <a name="parquet"></a>Parquet
Ler um conjunto de dados Parquet, um único arquivo ou pasta.

O Parquet como um formato pode assumir vários formatos no armazenamento. Para conjuntos de dados menores, às vezes é usado um único arquivo ‘.parquet’, várias bibliotecas do Python dão suporte à leitura/gravação em arquivos ‘.parquet’ únicos. Por enquanto, a AMLWB se baseia na biblioteca do Python PyArrow para ler o Parquet durante o uso ‘interativo’ local. Ele dá suporte a arquivos ‘.parquet’ únicos (contanto que fossem escritos de tal forma, não como uma parte de um conjunto de dados maior), bem como conjuntos de dados Parquet. Um conjunto de dados Parquet é uma coleção de mais de um arquivo '.parquet', em que cada um representa uma partição menor de um conjunto de dados maior. Os conjuntos de dados geralmente estão contidos em uma pasta e são o formato de saída padrão parquet para plataforma comum como Spark e Hive.

>[!NOTE]
>Ao ler dados Parquet que estão em uma pasta com vários arquivos ‘.parquet’ é mais seguro selecionar o diretório para leitura e marcar a opção ‘Conjunto de dados Parquet’. Isso fará com que PyArrow leia toda a pasta em vez de os arquivos individuais, garantindo o suporte para a leitura de maneiras mais complicadas de armazenar o Parquet no disco (como o particionamento de pasta.)**

A execução de escalar horizontalmente depende dos recursos de leitura do Parquet do Spark e dá suporte a arquivos únicos, bem como a pastas, da mesma forma para o local interativo.

#### <a name="options"></a>Opções
- Conjunto de dados Parquet
  - Essa opção determina se AMLWB expandirá um determinado diretório e tentará ler cada arquivo individualmente (o modo não marcado) ou tratará o diretório como o conjunto de dados inteiro e deixará o PyArrow encontrar a melhor maneira de interpretar os arquivos (o modo marcado).


## <a name="locations"></a>Locais
### <a name="local"></a>Local
Disco rígido local ou local de armazenamento de rede mapeado

### <a name="azure-blob"></a>BLOB do Azure
O Armazenamento do Azure (BLOB) requer uma assinatura do Azure

