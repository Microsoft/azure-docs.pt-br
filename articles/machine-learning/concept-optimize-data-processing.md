---
title: Otimizar processamento de dados
titleSuffix: Azure Machine Learning
description: Conheça as práticas recomendadas para otimizar as velocidades de processamento de dados e quais integrações Azure Machine Learning dão suporte ao processamento de dados em escala.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 77edac14ef13901725eed656835e1a937d4f4ddf
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360812"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Otimizar o processamento de dados com Azure Machine Learning

Neste artigo, você aprenderá sobre as práticas recomendadas para ajudá-lo a otimizar as velocidades de processamento de dados localmente e em escala.

O Azure Machine Learning é integrado a pacotes e estruturas de software livre para processamento de dados. Ao usar essas integrações e aplicar as recomendações de práticas recomendadas neste artigo, você pode melhorar suas velocidades de processamento de dados localmente e em escala.

## <a name="parquet-and-csv-file-formats"></a>Formatos de arquivo parquet e CSV

Arquivos CSV (valores separados por vírgula) são formatos de arquivo comuns para processamento de dados. No entanto, formatos de arquivo parquet são recomendados para tarefas de aprendizado de máquina.

[Os arquivos parquet](https://parquet.apache.org/) armazenam dados em um formato binário colunar. Esse formato será útil se a divisão dos dados em vários arquivos for necessária. Além disso, esse formato permite que você direcione os campos relevantes para seus experimentos de aprendizado de máquina. Em vez de ter que ler em um arquivo de dados de 20 GB, você pode diminuir esse carregamento de dados selecionando as colunas necessárias para treinar seu modelo de ML. Os arquivos parquet também podem ser compactados para minimizar a potência de processamento e ocupar menos espaço.

Os arquivos CSV são comumente usados para importar e exportar dados, já que são fáceis de editar e ler no Excel. Os dados em CSVs são armazenados como cadeias de caracteres em um formato baseado em linha, e os arquivos podem ser compactados para diminuir as cargas de transferência de dados. O CSVs não compactado pode ser expandido por um fator de cerca de 2-10 e o CSVs compactado pode aumentar ainda mais. Para que o CSV de 5 GB na memória se expanda para um desempenho superior aos 8 GB de RAM que você tem em seu computador. Esse comportamento de compactação pode aumentar a latência de transferência de dados, o que não é ideal se você tiver grandes quantidades de dados para processar. 

## <a name="pandas-dataframe"></a>Dataframe do Pandas

Os [dataframes do pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) são comumente usados para análise e manipulação de dados. `Pandas` funciona bem para tamanhos de dados menores que 1 GB, mas os tempos de processamento para `pandas` dataframes ficam lentos quando os tamanhos de arquivo atingem cerca de 1 GB. Essa lentidão ocorre porque o tamanho dos dados no armazenamento não é igual ao tamanho dos dados em um dataframe. Por exemplo, os dados em arquivos CSV podem ser expandidos até 10 vezes em um dataframe, de modo que um arquivo CSV de 1 GB pode se tornar 10 GB em um dataframe.

`Pandas` é de thread único, o que significa que as operações são feitas uma de cada vez em uma única CPU. Você pode facilmente paralelizar cargas de trabalho para várias CPUs virtuais em uma única instância de computação Azure Machine Learning com pacotes como [Modin](https://modin.readthedocs.io/en/latest/) que encapsulam `Pandas` usando um back-end distribuído.

Para paralelizar suas tarefas com `Modin` e [Dask](https://dask.org), basta alterar essa linha de código `import pandas as pd` para `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Dataframe: erro de memória insuficiente 

Normalmente, um erro *de memória insuficiente* ocorre quando o dataframe se expande sobre a RAM disponível em seu computador. Esse conceito também se aplica a uma estrutura distribuída como o `Modin` ou o `Dask` .  Ou seja, a operação tenta carregar o dataframe na memória em cada nó no cluster, mas não há RAM suficiente disponível para fazer isso.

Uma solução é aumentar a RAM para ajustar o dataframe na memória. Recomendamos que o tamanho de computação e a potência de processamento contenham duas vezes o tamanho da RAM. Portanto, se o dataframe for de 10 GB, use um destino de computação com, pelo menos, 20 GB de RAM para garantir que o dataframe possa se ajustar confortavelmente à memória e ser processado. 

Para várias CPUs virtuais, vCPU, tenha em mente que você deseja que uma partição se ajuste confortavelmente à RAM que cada vCPU pode ter no computador. Ou seja, se você tiver 16 GB de RAM 4 vCPUs, você desejará cerca de 2 GB de quadros de segundo por cada vCPU.

### <a name="local-vs-remote"></a>Local vs remoto

Você pode observar que alguns comandos do dataframe do pandas são executados mais rapidamente ao trabalhar em seu PC local em vez de uma VM remota provisionada com Azure Machine Learning. Seu PC local normalmente tem um arquivo de paginação habilitado, que permite que você carregue mais do que cabe na memória física, que é o seu disco rígido está sendo usado como uma extensão de sua RAM. Atualmente, Azure Machine Learning VMs são executadas sem um arquivo de paginação, portanto, só pode carregar o máximo de dados que a RAM física disponível. 

Para trabalhos de computação intensa, recomendamos que você escolha uma VM maior para melhorar as velocidades de processamento.

Saiba mais sobre a [série de VMs e os tamanhos disponíveis](concept-compute-target.md#supported-vm-series-and-sizes) para Azure Machine Learning. 

Para obter especificações de RAM, consulte as páginas da série de VMs correspondentes, como [Dv2-Dsv2 Series](../virtual-machines/dv2-dsv2-series-memory.md) ou [NC Series](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>Minimizar cargas de trabalho de CPU

Se não for possível adicionar mais RAM ao seu computador, você poderá aplicar as seguintes técnicas para ajudar a minimizar as cargas de trabalho de CPU e otimizar os tempos de processamento. Essas recomendações pertencem a sistemas únicos e distribuídos.

Técnica | Descrição
----|----
Compactação | Use uma representação diferente para seus dados, de forma que use menos memória e não afete significativamente os resultados do seu cálculo.<br><br>*Exemplo:* Em vez de armazenar entradas como uma cadeia de caracteres com cerca de 10 bytes ou mais por entrada, armazene-as como um booliano, true ou false, que você pode armazenar em 1 byte.
Agrupamento | Carregar dados na memória em subconjuntos (partes), processar os dados de um subconjunto no tempo ou vários subconjuntos em paralelo. Esse método funciona melhor se você precisa processar todos os dados, mas não precisa carregar todos os dados na memória de uma só vez. <br><br>*Exemplo:* Em vez de processar os dados de um ano inteiro de uma vez, carregue e processe os dados de um mês por vez.
Indexação | Aplicar e usar um índice, um resumo que informa onde encontrar os dados importantes para você. A indexação é útil quando você só precisa usar um subconjunto dos dados, em vez do conjunto completo<br><br>*Exemplo:* Se você tiver um ano inteiro de dados de vendas classificados por mês, um índice ajudará a pesquisar rapidamente o mês desejado que você deseja processar.

## <a name="scale-data-processing"></a>Processamento de dados de escala

Se as recomendações anteriores não forem suficientes e você não puder obter uma máquina virtual que atenda aos seus dados, você poderá 

* Use uma estrutura como `Spark` ou `Dask` para processar os dados de "memória insuficiente". Nessa opção, o dataframe é carregado na partição de RAM por partição e processado, com o resultado final sendo coletado no final.  

* Escale horizontalmente para um cluster usando uma estrutura distribuída. Nessa opção, as cargas de processamento de dados são divididas e processadas em várias CPUs que funcionam em paralelo, com o resultado final coletado no final.

### <a name="recommended-distributed-frameworks"></a>Estruturas distribuídas recomendadas

A tabela a seguir recomenda estruturas distribuídas que são integradas com Azure Machine Learning com base em sua preferência de código ou tamanho de dados.

Experiência ou tamanho dos dados | Recomendação
------|------
Se você estiver familiarizado com `Pandas`| `Modin` ou `Dask` dataframe
Se você preferir `Spark` | `PySpark`
Para dados com menos de 1 GB | `Pandas` instância de computação de Azure Machine Learning local **ou** remota
Para dados maiores que 10 GB| Mover para um cluster usando `Ray` , `Dask` ou `Spark`

Você pode criar `Dask` clusters no cluster de computação do Azure ml com o pacote [Dask-cloudprovider](https://cloudprovider.dask.org/en/latest/#azure) . Ou você pode executar `Dask` localmente em uma instância de computação.

## <a name="next-steps"></a>Próximas etapas

* [Opções de ingestão de dados com Azure Machine Learning](concept-data-ingestion.md).
* [Criar e registrar conjuntos de](how-to-create-register-datasets.md)os.
