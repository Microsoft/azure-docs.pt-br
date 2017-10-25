---
title: "Na guia profunda sobre como usar a Preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento apresenta os detalhes e uma visão geral sobre a resolução de problemas de dados com a preparação de dados do Azure ML"
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
ms.openlocfilehash: 22389ba85edb119acdd21b63f2deae2d71f31373
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-preparation-user-guide"></a>Guia do usuário de preparação de dados 
A experiência de Preparação de Dados oferece muitas funções avançadas. A seguir estão documentadas as partes mais profundas da experiência.

### <a name="step-execution-history-and-caching"></a>Execução de etapa, histórico e caching 
O histórico de etapas de Preparação de Dados mantém uma série de caches por motivos de desempenho. Se você clicar em uma etapa e ela atinja um cache, ela não será executada novamente. Se você tiver um bloco de gravação no final do histórico de etapas e alternar entre as etapas, mas não fizer alterações, a gravação não será disparada depois da primeira vez. Se você 
- Fizer alterações ao bloco de gravação ou
- Adicionar um novo bloco de transformação e movê-lo para uma posição acima do bloco de gravação, gerando uma invalidação de cache ou
- Se você alterar as propriedades de um bloco acima do bloco de gravação, gerando uma invalidação de cache ou
- Selecione atualizar em uma amostra (portanto, invalidando todo o cache)

Então uma nova gravação ocorrerá e substituirá a anterior.

### <a name="error-values"></a>Valores de erro

Transformações de dados podem falhar para um valor de entrada porque não é possível tratar esse valor apropriadamente. Por exemplo, na causa das operações de coerção de tipo, a coerção falhará se o valor da cadeia de caracteres de entrada não puder ser convertido para o tipo de destino especificado. Uma operação de coerção de tipo pode converter uma coluna de tipo de cadeia de caracteres em um tipo numérico ou booliano. Da mesma forma, a tentativa de duplicar uma coluna que não existe (o resultado de mover a operação Excluir Coluna X antes da operação de Duplicar Coluna X).

Nesses casos, a Preparação de Dados produzirá um **Valor de Erro** como a saída. Valores de erro indicam que uma operação anterior falhou para o valor especificado. Internamente, são tratados como um tipo de valor de primeira classe, mas sua presença não altera o tipo subjacente de uma coluna, mesmo que uma coluna consista inteiramente em Valores de Erro.

Valores de Erro são fáceis de identificar. Eles são realçados em vermelho e indicam "Erro". Para determinar o motivo para o erro, focalize um Valor de Erro para obter uma descrição para a falha.

Valores de Erro propagam-se. Depois que um valor de erro ocorre, ele se propaga na maioria dos casos como um erro pela maioria das operações. No entanto, atualmente há três maneiras de removê-lo ou substituí-lo:

1) Substitua
    -  Clique com o botão direito do mouse em uma coluna e selecione *Substituir Valores de Erro*. Então você pode escolher um valor de substituição para cada valor de erro encontrado na coluna.

2) Remover
    - A Preparação de Dados inclui filtros interativos para preservar ou remover Valores de Erro.
    - Clique com o botão direito do mouse em uma coluna e selecione *Filtrar Coluna*. Para preservar ou remover valores de erro, crie uma condicional com a condição *"é erro"* ou *"não é erro"*.

3) Use uma expressão do Python para operar condicionalmente em Valores de Erro. Para obter mais informações, acesse a [seção sobre extensões do Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>amostragem
Um arquivo de Fonte de Dados pega dados brutos de uma ou mais fontes, seja do sistema de arquivos local ou de um local remoto. O bloco de Exemplo permite que você especifique se deseja trabalhar com um subconjunto dos dados gerando exemplos. Ao operar em uma amostra de dados, em vez de um grande conjunto de dados, geralmente leva a um melhor desempenho ao realizar operações em etapas posteriores.

Para cada arquivo de Fonte de Dados, vários exemplos podem ser gerados e armazenados. No entanto, apenas um exemplo pode ser definido como o exemplo ativo. Você pode criar, editar ou excluir amostras no assistente de Fonte de Dados ou ao editar o Bloco de Exemplo. Quaisquer arquivos de Preparação de Dados que faça referência a uma Fonte de Dados usa inerentemente o exemplo especificado no arquivo da Fonte de Dados.

Há várias estratégias de amostragem disponíveis, cada uma com diferentes parâmetros configuráveis.

#### <a name="top"></a>Top
Essa estratégia pode ser aplicada a arquivos locais ou remotos. Ela usa as N primeiras linhas (especificadas pela Contagem) na Fonte de Dados.

#### <a name="random-n"></a>N aleatória 
Essa estratégia só pode ser aplicada a arquivos locais. Ela usa N linhas aleatórias (especificadas pela Contagem) na Fonte de Dados. Você pode fornecer uma semente específica para garantir que o mesmo exemplo seja gerado, desde que a Contagem também seja a mesma.

#### <a name="random-"></a>Aleatório % 
Essa estratégia pode ser aplicada a arquivos locais ou remotos. Em ambos os casos, uma probabilidade e semente devem ser fornecidas, de modo similar à estratégia de N aleatório.

Para obter exemplos de arquivos remotos, é necessário fornecer parâmetros adicionais.

- Gerador de exemplo 
  - Selecione um cluster do Spark ou destino de computação remota do Docker a ser usado para a geração de exemplo. O destino de computação deve ser criado para o projeto com antecedência para que apareça na lista. Siga as etapas em [Como usar a GPU no Azure Machine Learning](how-to-use-gpu.md) "Criar um novo destino de computação" para criar destinos de computação.
- Armazenamento de exemplo 
  - Informe um local de armazenamento intermediário para armazenar o exemplo remoto. Esse caminho deve ser um diretório diferente do local do arquivo de entrada.

#### <a name="full-file"></a>Arquivo completo 
Essa estratégia só pode ser aplicada a arquivos locais, levando o arquivo completo para a Fonte de Dados. Se o arquivo for grande demais, essa opção poderá desacelerar operações futuras no aplicativo e você poderá considerar mais apropriado usar uma estratégia de amostragem diferente.


### <a name="forking-merging-and-appending"></a>Bifurcar, mesclar e acrescentar

Ao aplicar um filtro em um conjunto de dados, a operação divide os dados em dois conjuntos de resultados, em que um conjunto representa registros bem-sucedidos n filtro e outro conjunto para os registros malsucedidos. Em ambos os casos, o usuário pode escolher qual conjunto de resultados exibir. O usuário pode descartar o outro conjunto de dados ou colocá-lo em um novo fluxo de dados. A última opção é conhecida como *bifurcação*.

Para bifurcar, selecione uma coluna, clique com o botão direito do mouse e selecione a Coluna de Filtro.
- Em "Eu quero", selecione *Manter Linhas* para exibir o conjunto de resultados que é aprovado no filtro ou *Remover Linhas* para exibir o conjunto que falhou.
- Após "Condições", selecione *Criar Fluxo de Dados Contendo as Linhas Filtradas* para bifurcar o conjunto de resultados não em exibição em um novo fluxo de dados.


Essa prática geralmente serve para separar um conjunto de dados que exige preparação adicional. Depois de arrebanhar o conjunto de dados bifurcado, é comum mesclar os dados com o conjunto de resultados no fluxo de dados original. Para executar uma “mesclagem” (o inverso de uma operação de "bifurcação"), use uma das seguintes ações:
- *Acrescentar Linhas*. Mesclar dois ou mais fluxos de dados verticalmente (por linha). 
- *Acrescentar Colunas*. Mesclar dois ou mais fluxos de dados horizontalmente (por coluna).


>[!NOTE]
>AppendColumns falhará se ocorrer uma colisão de coluna.


Após uma operação de mesclagem, um ou mais fluxos de dados serão referenciados por um fluxo de dados de origem. DataPrep notifica você com uma notificação no canto inferior direito do aplicativo, abaixo da lista de etapas.


Qualquer operação no fluxo de dados referenciado exige que o fluxo de dados pai atualize o exemplo usado no fluxo de dados referenciado. Nesse caso, uma caixa de diálogo de confirmação substitui a notificação de referência de fluxo de dados no canto inferior direito. A caixa de diálogo confirma que você precisa atualizar o fluxo de dados para sincronizar com alterações em quaisquer fluxos de dados de dependência.

### <a name="list-of-appendices"></a>Lista de apêndices 
[Apêndice 2 – Fontes de dados com suporte](data-prep-appendix2-supported-data-sources.md)  
[Apêndice 3 – Transformações com suporte](data-prep-appendix3-supported-transforms.md)  
[Apêndice 4 – Inspetores com suporte](data-prep-appendix4-supported-inspectors.md)  
[Apêndice 5 – Destinos com suporte](data-prep-appendix5-supported-destinations.md)  
[Apêndice 6 – Exemplo de expressões de filtro em Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Apêndice 7 – Exemplo de expressões de fluxo de dados de transformação em Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Apêndice 8 – Exemplo de fontes de dados em Python](data-prep-appendix8-sample-source-connections-python.md)  
[Apêndice 9 – Exemplo de conexões de destino em Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Apêndice 10 – Exemplo de transformações de coluna em Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
