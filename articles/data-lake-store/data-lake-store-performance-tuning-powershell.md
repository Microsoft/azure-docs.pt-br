---
title: Diretrizes de ajuste do desempenho para uso do PowerShell com o Data Lake Store | Microsoft Docs
description: Dicas sobre como melhorar o desempenho ao usar o Azure PowerShell com o Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/30/2017
ms.author: nitinme
ms.openlocfilehash: 49404c7df6423a20c71347e4a764d5626110310e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Diretrizes de ajuste do desempenho para uso do PowerShell com o Azure Data Lake Store

Este artigo lista as propriedades que podem ser ajustadas para obter um melhor desempenho ao usar o PowerShell para trabalhar com o Data Lake Store:

## <a name="performance-related-properties"></a>Propriedades relacionadas com desempenho

| Propriedade            | Padrão | Descrição |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Esse parâmetro permite que você escolha o número de threads paralelos para carregar ou baixar cada arquivo. Esse número representa o máximo de threads que pode ser alocado por arquivo, mas você pode obter menos threads dependendo do cenário (por exemplo, se você estiver carregando um arquivo de 1 KB, você obterá um thread mesmo que você peça 20 threads).  |
| ConcurrentFileCount | 10      | Esse parâmetro é especificamente para carregar ou baixar pastas. Esse parâmetro determina o número de arquivos simultâneos que podem ser carregados ou baixados. Esse número representa o número máximo de arquivos simultâneos que podem ser carregados ou baixados ao mesmo tempo, mas você pode obter menor simultaneidade, dependendo do cenário (por exemplo, se você estiver carregando dois arquivos, você obterá dois uploads de arquivo simultâneos mesmo se você pedir 15). |

**Exemplo**

Esse comando baixa os arquivos do Azure Data Lake Store para a unidade local do usuário usando 20 threads por arquivo e 100 arquivos simultâneos.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Como faço para determinar o valor dessas propriedades?

A próxima questão que você pode ter é como determinar qual valor fornecer para as propriedades relacionadas com desempenho. Aqui estão algumas diretrizes que podem ser usadas.

* **Etapa 1: Determinar a contagem total de threads** – você deve começar calculando a contagem total de threads a usar. Como diretriz geral, você deve usar seis threads para cada núcleo físico.

        Total thread count = total physical cores * 6

    **Exemplo**

    Supondo que você está executando os comandos do PowerShell de uma VM D14 que tem 16 núcleos

        Total thread count = 16 cores * 6 = 96 threads


* **Etapa 2: Calcular PerFileThreadCount** – calculamos nosso PerFileThreadCount com base no tamanho dos arquivos. Para arquivos menores do que 2,5 GB, não é necessário alterar esse parâmetro, pois o padrão de 10 é suficiente. Para arquivos com mais de 2,5 GB, você deve usar 10 threads como a base para os primeiros 2,5 GB e adicionar 1 thread para cada aumento de 256 MB no tamanho do arquivo. Se você estiver copiando uma pasta com uma grande variedade de tamanhos de arquivo, considere agrupá-los em tamanhos de arquivo semelhantes. Ter tamanhos de arquivo diferentes pode fazer com que o desempenho não seja o ideal. Se não for possível agrupar os tamanhos de arquivo semelhantes, você deverá definir PerFileThreadCount com base no maior tamanho do arquivo.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Exemplo**

    Supondo que você tem 100 arquivos variando de 1 GB a 10 GB, usamos 10 GB como o maior tamanho do arquivo para a equação, cuja leitura seria semelhante à seguinte.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Etapa 3: calcular ConcurrentFilecount** – use a contagem total de threads e PerFileThreadCount para calcular ConcurrentFileCount com base na equação a seguir:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exemplo**

    Com base nos valores de exemplo que utilizamos

        96 = 40 * ConcurrentFileCount

    Portanto, **ConcurrentFileCount** é **2,4**, que podemos pode arredondar para **2**.

## <a name="further-tuning"></a>Ajuste adicional

Você pode exigir ajuste adicional porque há um intervalo de tamanhos de arquivo com os quais trabalhar. O cálculo anterior funciona bem se a maioria dos arquivos ou todos eles forem maiores e mais próximos do intervalo de 10 GB. Se em vez disso houver vários tamanhos de arquivos diferentes com muitos arquivos sendo menores, você poderá reduzir PerFileThreadCount. Reduzindo PerFileThreadCount, podemos aumentar ConcurrentFileCount. Então, se supormos que a maioria dos nossos arquivos são menores no intervalo de 5 GB, poderemos refazer nosso cálculo:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Portanto, **ConcurrentFileCount** se torna 96/20, que é igual a 4,8, arredondado para **4**.

Você pode continuar a ajustar essas configurações, alterando **PerFileThreadCount** para cima e para baixo dependendo da distribuição de seus tamanhos de arquivo.

### <a name="limitation"></a>Limitações

* **O número de arquivos é menor do que ConcurrentFileCount**: se o número de arquivos que você está carregando for menor do que o **ConcurrentFileCount** calculado, você deverá reduzir **ConcurrentFileCount** para ser igual ao número de arquivos. Você pode usar quaisquer segmentos restantes para aumentar **PerFileThreadCount**.

* **Muitos threads**: se você aumentar muito a contagem de threads sem aumentar o tamanho do cluster, correrá o risco de degradação do desempenho. Pode haver problemas de contenção ao realizar alternância de contexto na CPU.

* **Simultaneidade insuficiente**: se a simultaneidade não for suficiente, o cluster poderá ser muito pequeno. Você pode aumentar o número de nós no cluster, o que lhe proporciona mais simultaneidade.

* **Erros de limitação**: você pode ver erros de limitação se a simultaneidade for muito alta. Se você estiver vendo erros de limitação, deverá reduzir a simultaneidade ou entrar em contato conosco.

## <a name="next-steps"></a>Próximas etapas
* [Usar o Azure Data Lake Store para requisitos de Big Data](data-lake-store-data-scenarios.md) 
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

