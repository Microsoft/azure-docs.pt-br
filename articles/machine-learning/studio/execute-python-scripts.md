---
title: "Executar scripts de aprendizado de máquina do Python | Microsoft Docs"
description: "Descreve princípios de design subjacentes ao suporte a Python no Azure Machine Learning e seus cenários de uso básico, recursos e limitações."
keywords: "aprendizado de máquina em python, pandas, python pandas, scripts python, executar scripts python"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: bradsev
ms.openlocfilehash: 011b454bc9cf13811c2f6b66cdd02e26e842b4e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts Python de aprendizado de máquina no Azure Machine Learning Studio

Este tópico descreve os princípios de design subjacentes ao suporte atual para scripts Python no Azure Machine Learning. Os principais recursos fornecidos também são descritos, incluindo:

- executar cenários de uso básico
- pontuação de um experimento em um serviço Web
- suporte para importação de código existente
- exportar visualizações
- executar seleção de recursos supervisionada
- entender algumas limitações

[Python](https://www.python.org/) é uma ferramenta indispensável no conjunto de ferramentas de muitos cientistas de dados. Ele tem:

* uma sintaxe elegante e concisa, 
* suporte de plataforma cruzada, 
* uma grande coleção de bibliotecas eficientes, e 
* ferramentas de desenvolvimento sólidas. 

O Python está sendo usado em todas as fases de um fluxo de trabalho normalmente usado na modelagem de aprendizado de máquina:

- ingestão de dados e processamento 
- construção de recurso
- treinamento do modelo 
- validação de modelo
- implantação dos modelos

O Azure Machine Learning Studio dá suporte à inclusão de scripts de Python em várias partes de um experimento de aprendizado de máquina, bem como à sua publicação integrada como serviços Web no Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Princípios de design de scripts Python no Machine Learning

A principal interface para Python no Azure Machine Learning Studio é o módulo [Executar Script Python][execute-python-script] mostrado na Figura 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. O módulo **Executar Script Python** .

O módulo [Executar Script Python][execute-python-script] no Azure ML Studio aceita até três entradas e produz até duas saídas (discutidas na seção a seguir), como seu equivalente em R, o módulo [Executar Script R][execute-r-script]. O código Python a ser executado é inserido na caixa de parâmetro como uma função de ponto de entrada especialmente denominada `azureml_main`. Estes são os principais princípios de design usados para implementar este módulo:

1. *Devem ser expressões idiomáticas para usuários de Python.* A maioria dos usuários de Python fatora o código como funções em módulos. Portanto, colocar um lote de instruções executáveis em um módulo de nível superior é relativamente raro. Como resultado, a caixa de script também recebe uma função de Python especialmente nomeada em vez de apenas uma sequência de instruções. Os objetos expostos na função são tipos de biblioteca do Python padrão, como estruturas de dados [Pandas](http://pandas.pydata.org/) e matrizes [NumPy](http://www.numpy.org/).
2. *Deve ter alta fidelidade entre execuções locais e na nuvem.* O back-end usado para executar o código Python se baseia em [Anaconda](https://store.continuum.io/cshop/anaconda/), uma distribuição científica de plataforma cruzada do Python amplamente utilizada. Ele vem com quase 200 dos pacotes de Python mais comuns. Portanto, os cientistas de dados podem depurar e qualificar seu código no ambiente Anaconda compatível com o Azure Machine Learning. Em seguida, use um ambiente de desenvolvimento existente, como [IPython](http://ipython.org/) notebook ou [Ferramentas Python para Visual Studio](http://aka.ms/ptvs), para executá-lo como parte de um experimento do Azure ML. O ponto de entrada `azureml_main` é uma função comum do Python e, portanto, ****pode ser criado sem código específico do Azure ML ou o SDK instalado.
3. *Deve ser totalmente combinável com outros módulos do Azure Machine Learning.* O módulo [Executar Script Python][execute-python-script] aceita, como entradas e saídas, conjuntos de dados padrão do Azure Machine Learning. A estrutura subjacente faz a conexão entre os tempos de execução do Azure ML e do Python do modo transparente e eficiente. Portanto, o Python pode ser usado em conjunto com fluxos de trabalho do Azure ML existentes, incluindo aqueles que chamam o R e o SQLite. Como resultado, um cientista de dados pôde compor fluxos de trabalho que:
   * usam o Python e o Pandas para pré-processando e limpeza de dados
   * alimentam os dados para uma transformação de SQL, unindo vários conjuntos de dados para formar recursos
   * treinam modelos usando os algoritmos no Azure Machine Learning 
   * avaliar e pós-processar os resultados usando R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Cenários de uso básico no ML para scripts Python

Nesta seção, avaliamos alguns dos usos básicos do módulo [Executar Script Python][execute-python-script]. As entradas para o módulo Python são expostas como quadros de dados Pandas. A função deve retornar uma única estrutura de dados Pandas empacotada dentro de uma [sequência](https://docs.python.org/2/c-api/sequence.html) Python, como uma tupla, lista ou matriz NumPy. O primeiro elemento dessa sequência é retornado na primeira porta de saída do módulo. O esquema é mostrado na Figura 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. Mapeamento de portas de entrada para parâmetros e valor retornado à porta de saída.

Uma semântica mais detalhada de como as portas de entrada são mapeadas para parâmetros da função `azureml_main` é mostrada na Tabela 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabela 1. Mapeamento de portas de entrada para parâmetros de função.

O mapeamento entre as portas de entrada e os parâmetros de função é posicional:

- A primeira porta de entrada conectada é mapeada para o primeiro parâmetro da função. 
- A segunda entrada (se conectada) é mapeada para o segundo parâmetro da função.

Confira *Python for Data Analysis* (O'Reilly, 2012) por W. McKinney para obter mais informações sobre o Pandas do Python e como ele pode ser usado para manipular dados de forma eficaz e eficiente. 


## <a name="translation-of-input-and-output-types"></a>Conversão de tipos de entrada e saída 
Conjuntos de dados de entrada no Azure ML são convertidos em quadros de dados em Pandas. Quadros de dados de saída são convertidos em conjuntos de dados do Azure ML. As seguintes conversões são realizadas:

1. Colunas numéricas e de cadeias de caracteres são convertidas da forma como estão e os valores ausentes em um conjunto de dados são convertidos para valores “NA” no Pandas. A mesma conversão ocorre na direção contrária (valores NA no Pandas são convertidos em valores ausentes no Azure ML).
2. Os vetores de índice no Pandas não têm suporte no Azure ML. Todos os quadros de dados de entrada na função Python sempre têm um índice numérico de 64 bits de 0 até o número de linhas menos 1. 
3. Conjuntos de dados do Azure ML não podem ter nomes de coluna duplicados e nomes de coluna que não são cadeias de caracteres. Se uma estrutura de dados de saída contiver colunas não numéricas, a estrutura chama `str` nos nomes de coluna. Da mesma forma, quaisquer nomes de coluna duplicados são danificados automaticamente para assegurar que os nomes sejam exclusivos. O sufixo (2) é adicionado à primeira duplicata, (3) à segunda duplicata e assim por diante.


## <a name="operationalizing-python-scripts"></a>Operacionalizando scripts Python

Quaisquer módulos [Executar Script Python][execute-python-script] usados em um teste de pontuação são chamados quando publicados como um serviço Web. Por exemplo, a Figura 3 mostra um experimento de pontuação que contém o código para avaliar uma única expressão de Python. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Serviço Web para avaliar uma expressão de Python.

Um serviço Web criado com base neste teste:

- aceita como entrada uma expressão Python (como uma cadeia de caracteres)
- envia-o ao interpretador do Python 
- retorna uma tabela que contém a expressão e o resultado avaliado.


## <a name="importing-existing-python-script-modules"></a>Importando módulos de script Python existentes

Um caso de uso comum para muitos cientistas de dados é incorporar scripts Python existentes a experimentos do Azure ML. Em vez de exigir que todo o código seja concatenado e colado em uma caixa de script simples, o módulo [Executar Script Python][execute-python-script] aceita um arquivo zip que contém módulos Python na terceira porta de entrada. O arquivo é descompactado pela estrutura de execução no tempo de execução e o conteúdo é adicionado ao caminho da biblioteca do interpretador de Python. A função do ponto de entrada `azureml_main` pode, então, importar esses módulos diretamente.

Por exemplo, considere o arquivo Hello.py que contém uma função simples "Hello, World".

![image6](./media/execute-python-scripts/figure4.png)

Figura 4. Função definida pelo usuário no arquivo Hello.py.

Em seguida, criamos um arquivo Hello.zip que contenha o Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Figura 5. Arquivo zip que contém o código Python definido pelo usuário.

Carregue o arquivo zip como um conjunto de dados no Azure Machine Learning Studio. Crie e execute um teste que usa o código Python no arquivo Hello.zip ao anexá-lo à terceira porta de entrada do módulo **Script Executar Python**, conforme mostrado na figura.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Figura 6. Exemplo de experimento com código Python definido pelo usuário carregado como um arquivo zip.

A saída do módulo mostra que o arquivo zip foi descompactado e a função `print_hello` foi executada.
 
![image10](./media/execute-python-scripts/figure7.png)

Figura 7. Função definida pelo usuário em uso dentro do módulo [Executar Script Python][execute-python-script].


## <a name="working-with-visualizations"></a>Trabalhando com visualizações

Gráficos plotados criado usando o MatplotLib que podem ser visualizados no navegador podem ser retornados pelo módulo [Executar Script Python][execute-python-script]. Mas os gráficos não são redirecionados automaticamente para imagens como é feito quando R é usado. Assim, o usuário precisa salvar explicitamente qualquer gráfico em arquivos PNG se eles precisarem ser retornados para o Azure Machine Learning. 

Para gerar imagens do MatplotLib, você precisa realizar o procedimento a seguir:

* alternar o back-end para "AGG" em vez do processador padrão com base em Qt 
* criar um novo objeto de figura 
* obter o eixo e gerar todos os gráficos nele 
* salvar a figura em um arquivo PNG 

Esse processo é ilustrado na Figura 8 a seguir, que cria uma matriz de plotagem de dispersão usando a função scatter_matrix no Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Figura 8. Código para salvar figuras MatplotLib em imagens.

A Figura 9 mostra um experimento que usa o script mostrado anteriormente para retornar gráficos de plotagem por meio da segunda porta de saída.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualizando gráficos de plotagem gerados por meio do código Python.

É possível retornar diferentes figuras salvando-as como diferentes imagens, o tempo de execução do Azure Machine Learning seleciona todas as imagens e as concatena para visualização.


## <a name="advanced-examples"></a>Exemplos avançados

O ambiente Anaconda instalado no Azure Machine Learning contém pacotes comuns, como NumPy, SciPy e Scikits-Learn. Esses pacotes podem ser usados com eficiência para várias tarefas de processamento de dados em um pipeline de aprendizado de máquina. Por exemplo, o experimento e o script a seguir ilustram o uso de mecanismos de ensemble learning em Scikits-Learn para calcular pontuações de importância de recursos para um conjunto de dados. As pontuações podem ser usadas para executar a seleção de recurso supervisionada antes de passar para outro modelo do ML.

Aqui está a função Python usada para calcular as pontuações de importância e ordenar os recursos com base nas pontuações:

![image11](./media/execute-python-scripts/figure8.png)

Figura 10. Função para classificar recursos por pontuação.
  Depois, o experimento seguinte calcula e retorna as pontuações de importância dos recursos do conjunto de dados "Pima Indian Diabetes" no Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figura 11. Experimento para classificar recursos no conjunto de dados Pima Indian Diabetes.

## <a name="limitations"></a>Limitações
O módulo [Executar Script Python][execute-python-script] atualmente tem as seguintes limitações:

1. *Execução em modo seguro.* O tempo de execução do Python atualmente é feito no modo seguro e, consequentemente, não permite o acesso à rede ou ao sistema de arquivos local de maneira persistente. Todos os arquivos salvos localmente são isolados e excluídos após a conclusão do módulo. O código Python não pode acessar a maioria das pastas do computador em que é executado, com exceção do diretório atual e seus subdiretórios.
2. *Falta de suporte para desenvolvimento e depuração sofisticados.* O módulo Python atualmente não dá suporte a recursos de IDE, como IntelliSense e depuração. Além disso, se o módulo falhar em tempo de execução, o rastreamento de pilha do Python completo estará disponível. Porém, ele deve ser exibido no log de saída para o módulo. No momento, recomendamos que você desenvolva e depure scripts Python em um ambiente como IPython e depois importe o código para o módulo.
3. *Saída de estruturas de dados únicas.* O ponto de entrada do Python para retornar somente uma estrutura de dados como saída. Atualmente, não é possível retornar objetos arbitrários do Python como modelos treinados diretamente para o tempo de execução do Azure Machine Learning. Assim como o módulo [Executar Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos serializar objetos em uma matriz de bytes e retorná-la dentro de um quadro de dados.
4. *Incapacidade de personalizar a instalação do Python*. Atualmente, a única maneira de adicionar módulos personalizados do Python é por meio do mecanismo de compactação de arquivo zip descrito anteriormente. Embora isso seja viável para pequenos módulos, é complicado para módulos grandes (especialmente aqueles com DLLs nativos) ou um grande número de módulos. 

## <a name="conclusions"></a>Conclusões
O módulo [Executar Script Python][execute-python-script] permite a um cientista de dados incorporar código Python existente a fluxos de trabalho de aprendizado de máquina hospedados na nuvem no Azure Machine Learning e operacionalizá-los como parte de um serviço Web. O módulo de script Python interopera naturalmente com outros módulos no Azure Machine Learning. O módulo pode ser usado para uma variedade de tarefas, desde exploração de dados até pré-processamento e extração de recursos e, em seguida, para avaliação e pós-processamento dos resultados. O tempo de execução de back-end usado para a execução se baseia no Anaconda, uma distribuição do Python amplamente testada e utilizada. Esse back-end simplifica para você o processo de integrar ativos de código existentes à nuvem.

Esperamos fornecer funcionalidades adicionais para o módulo [Executar Script Python][execute-python-script], como a capacidade de treinar e operacionalizar modelos em Python e acrescentar um suporte aprimorado para o desenvolvimento e depuração de código no Azure Machine Learning Studio.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte o [Centro de Desenvolvedores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
