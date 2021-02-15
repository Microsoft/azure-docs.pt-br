---
title: Ajuste de desempenho Azure Data Lake Storage Gen1-PowerShell
description: Dicas sobre como melhorar o desempenho ao usar Azure PowerShell com Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/09/2018
ms.author: twooley
ms.openlocfilehash: 4ac2bbb21fd1a987d544a536d0f52628824e0bf4
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723772"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>Orientação de ajuste de desempenho para o uso do PowerShell com o Armazenamento de Data Lake do Azure Gen1

Este artigo descreve as propriedades que você pode ajustar para obter melhor desempenho ao usar o PowerShell para trabalhar com Data Lake Storage Gen1.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Propriedades relacionadas com desempenho

| Propriedade            | Padrão | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Esse parâmetro permite que você escolha o número de threads paralelos para carregar ou baixar cada arquivo. Esse número representa o máximo de threads que pode ser alocado por arquivo, mas você pode obter menos threads dependendo do cenário (por exemplo, se você estiver carregando um arquivo de 1 KB, você obterá um thread mesmo que você peça 20 threads).  |
| ConcurrentFileCount | 10      | Esse parâmetro é especificamente para carregar ou baixar pastas. Esse parâmetro determina o número de arquivos simultâneos que podem ser carregados ou baixados. Esse número representa o número máximo de arquivos simultâneos que podem ser carregados ou baixados ao mesmo tempo, mas você pode obter menor simultaneidade, dependendo do cenário (por exemplo, se você estiver carregando dois arquivos, você obterá dois uploads de arquivo simultâneos mesmo se você pedir 15). |

**Exemplo:**

Esse comando baixa arquivos do Data Lake Storage Gen1 para a unidade local do usuário usando 20 threads por arquivo e 100 arquivos simultâneos.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Como determinar valores de propriedade

A próxima questão que você pode ter é como determinar qual valor fornecer para as propriedades relacionadas com desempenho. Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: determinar a contagem total de threads** -início calculando a contagem total de threads a ser usada. Como diretriz geral, você deve usar seis threads para cada núcleo físico.

    `Total thread count = total physical cores * 6`

    **Exemplo:**

    Supondo que você está executando os comandos do PowerShell de uma VM D14 que tem 16 núcleos

    `Total thread count = 16 cores * 6 = 96 threads`

* **Etapa 2: calcular PerFileThreadCount** – calculamos nosso PerFileThreadCount com base no tamanho dos arquivos. Para arquivos menores do que 2,5 GB, não é necessário alterar esse parâmetro, pois o padrão de 10 é suficiente. Para arquivos com mais de 2,5 GB, você deve usar 10 threads como a base para os primeiros 2,5 GB e adicionar 1 thread para cada aumento de 256 MB no tamanho do arquivo. Se você estiver copiando uma pasta com uma grande variedade de tamanhos de arquivo, considere agrupá-los em tamanhos de arquivo semelhantes. Ter tamanhos de arquivo diferentes pode fazer com que o desempenho não seja o ideal. Se não for possível agrupar os tamanhos de arquivo semelhantes, você deverá definir PerFileThreadCount com base no maior tamanho do arquivo.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Exemplo:**

    Supondo que você tem 100 arquivos variando de 1 GB a 10 GB, usamos 10 GB como o maior tamanho do arquivo para a equação, cuja leitura seria semelhante à seguinte.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **Etapa 3: calcular ConcurrentFilecount** – use a contagem total de threads e PerFileThreadCount para calcular ConcurrentFileCount com base na equação a seguir:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Exemplo:**

    Com base nos valores de exemplo que utilizamos

    `96 = 40 * ConcurrentFileCount`

    Portanto, **ConcurrentFileCount** é **2,4**, que podemos pode arredondar para **2**.

## <a name="further-tuning"></a>Ajuste adicional

Você pode exigir ajuste adicional porque há um intervalo de tamanhos de arquivo com os quais trabalhar. O cálculo anterior funciona bem se a maioria dos arquivos ou todos eles forem maiores e mais próximos do intervalo de 10 GB. Se em vez disso houver vários tamanhos de arquivos diferentes com muitos arquivos sendo menores, você poderá reduzir PerFileThreadCount. Reduzindo PerFileThreadCount, podemos aumentar ConcurrentFileCount. Então, se supormos que a maioria dos nossos arquivos são menores no intervalo de 5 GB, poderemos refazer nosso cálculo:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Portanto, **ConcurrentFileCount** se torna 96/20, que é igual a 4,8, arredondado para **4**.

Você pode continuar a ajustar essas configurações, alterando **PerFileThreadCount** para cima e para baixo dependendo da distribuição de seus tamanhos de arquivo.

### <a name="limitation"></a>Limitação

* **O número de arquivos é menor do que ConcurrentFileCount**: se o número de arquivos que você está carregando for menor do que o **ConcurrentFileCount** calculado, você deverá reduzir **ConcurrentFileCount** para ser igual ao número de arquivos. Você pode usar quaisquer segmentos restantes para aumentar **PerFileThreadCount**.

* **Muitos threads**: se você aumentar muito a contagem de threads sem aumentar o tamanho do cluster, correrá o risco de degradação do desempenho. Pode haver problemas de contenção ao realizar alternância de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, o cluster poderá ser muito pequeno. Você pode aumentar o número de nós no cluster, o que lhe proporciona mais simultaneidade.

* **Erros de limitação**: você pode ver erros de limitação se a simultaneidade for muito alta. Se você estiver vendo erros de limitação, deverá reduzir a simultaneidade ou entrar em contato conosco.

## <a name="next-steps"></a>Próximas etapas

* [Use o Armazenamento de Data Lake do Azure Gen1 para requisitos de big data](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

