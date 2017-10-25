---
title: "Previsão da série temporal de demanda de energia | Microsoft Docs"
description: "Como aplicar o aprendizado de máquina para prever as séries temporais de demanda de energia no Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: bd0ddfcffdb6f946f9a3786f3d0add1740be861b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="energy-demand-time-series-forecasting"></a>Previsão da série temporal de demanda de energia


A previsão da série temporal é a tarefa de prever valores futuros em uma sequência temporal de observações. É um problema comum e tem aplicações em vários setores. Por exemplo, as empresas de varejo precisam prever as vendas futuras de produtos para que possam de fato organizar sua rede de fornecedores para atenderem à demanda. Da mesma forma, as empresas de entrega de pacotes precisam estimar a demanda de seus serviços para que possam planejar os requisitos de força de trabalho e as rotas de entrega antecipadamente. Em muitos casos, os riscos financeiros de previsões imprecisas podem ser graves. Portanto, a previsão geralmente é uma atividade de negócios crítica.

Este exemplo mostra como a previsão de série temporal pode ser realizada por meio da aplicação de técnicas de aprendizado de máquina. Você será guiado através de cada etapa do processo de modelagem, incluindo:
- Preparação de dados para limpar e formatar os dados;
- Recursos de criação para os modelos de aprendizado de máquina dos dados brutos de série temporal;
- Treinamento sobre vários modelos de aprendizado de máquina;
- Avaliação dos modelos ao comparar seu desempenho em um conjunto de dados de teste mantido externamente; e
- Operacionalização do melhor modelo, tornando-o disponível por meio de um serviço Web para gerar previsões sob demanda.

O Azure Machine Learning Workbench auxilia o processo de modelagem em cada etapa: 
- O exemplo mostra como o ambiente de notebook Jupyter, disponível diretamente por meio do Workbench, pode tornar o desenvolvimento do código Python mais fácil. O processo de desenvolvimento do modelo pode ser explicado para outras pessoas de forma mais clara usando gráficos e anotações de markdown. Estes notebooks podem ser exibidos, editados e executados diretamente do Workbench.
- Os modelos preparados podem ser preservados e carregados no Armazenamento de Blobs. Isso ajuda o cientista de dados a controlar os objetos de modelo preparados e garantir que eles sejam retidos e recuperáveis quando necessário.
- As métricas podem ser registradas durante a execução de um script do Python, permitindo que o cientista de dados mantenha um registro de pontuações de desempenho do modelo.
- O Workbench produz tabelas personalizáveis das métricas registradas, permitindo que o cientista de dados compare facilmente as métricas de desempenho do modelo. Os gráficos são exibidos automaticamente para que o melhor modelo de desempenho possa ser prontamente identificado.
- Por fim, o exemplo mostra como um modelo preparado pode ser operacionalizado ao implantá-lo em um serviço Web em tempo real.

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria
O repositório público do GitHub para este cenário do mundo real contém todos os materiais, incluindo exemplos de código, necessários para este exemplo:

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Visão geral de casos de uso

Este cenário se concentra na previsão da demanda de energia, em que a meta é prever o carregamento futuro em uma grade de energia. É uma operação comercial crítica para as empresas do setor de energia, uma vez que as operadoras precisam manter o equilíbrio entre a energia consumida em uma grade e a energia fornecida a ela. O fornecimento de muita energia à grade pode resultar em perda de energia ou em falhas técnicas. No entanto, se pouca energia for fornecida, poderá haver apagões, deixando os clientes sem energia. Normalmente, os operadores de grade podem tomar decisões de curto prazo para gerenciar o fornecimento de energia para a grade e manter o carregamento em equilíbrio. Portanto, uma previsão de curto prazo exata da demanda de energia é essencial para que o operador tome essas decisões com confiança.

Este cenário fornece detalhes sobre a construção de uma solução de previsão de demanda de energia de aprendizado de máquina. A solução é preparada em um conjunto de dados público do [NYISO (New York Independent System Operator – Operador de Sistemas Independente de Nova York)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), que opera a grade de energia para o Estado de Nova York. O conjunto de dados inclui os dados de demanda de energia por hora para a cidade de Nova York durante um período de cinco anos. Um conjunto de dados adicional contendo as condições climáticas por hora em Nova York durante o mesmo período de tempo foi coletado do [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Pré-requisitos

- Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).
- Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.
- Este exemplo pressupõe que você esteja executando o Azure ML Workbench no Windows 10 com o [mecanismo do Docker](https://www.docker.com/) instalado localmente. Se você estiver usando o macOS, as instruções serão basicamente as mesmas.
- Operacionalização do Azure Machine Learning instalada com uma configuração de ambiente de implantação local e uma conta de gerenciamento de modelos criada, conforme descrito neste [guia](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md).
- Este exemplo requer que você atualize a instalação Pandas para a versão 0.20.3 ou superior e instale o matplotlib. Clique em *Abrir Prompt de Comando* do menu *Arquivo* no Workbench e execute os seguintes comandos para instalar essas dependências:

    ```
    conda install "pandas>=0.20.3"

    conda install matplotlib
    ```
    
## <a name="create-a-new-workbench-project"></a>Criar um novo projeto de Workbench

Crie um novo projeto usando este exemplo como modelo:
1.  Abra o Azure Machine Learning Workbench
2.  Na página **Projetos**, clique no sinal **+** e selecione **Novo Projeto**
3.  No painel **Criar Novo Projeto**, preencha as informações do seu novo projeto
4.  Na caixa de pesquisa **Pesquisar Modelos de Projeto**, digite "Previsão da série temporal de demanda de energia" e selecione o modelo
5.  Clique em **Criar**


## <a name="data-description"></a>Descrição dos dados

Há dois conjuntos de dados: `nyc_demand.csv` e `nyc_weather.csv`:

**nyc_demand.csv** contém os valores de demanda de energia por hora para a cidade de Nova York, nos anos de 2012-2017. Os dados têm a seguinte estrutura simples:

| timeStamp | demanda |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Os valores de demanda estão em megawatt-hora (MWh). Abaixo há um gráfico da demanda de energia durante um período de sete dias em julho de 2017:

![Demanda de energia](./media/scenario-time-series-forecasting/energy_demand.png  "Demanda de energia")

**nyc_weather.csv** contém os valores climáticos por hora da cidade de Nova York, ao longo dos anos 2012-2017:

| timeStamp | precip | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0,0 | 46,13 |
| 2012-01-01 01:00:00 | 0,01 | 45,89 |
| 2012-01-01 02:00:00 | 0,05 | 45,04 |
| 2012-01-01 03:00:00 | 0,02 | 45,03 |

*precip* é uma medida do percentual do nível de precipitação. Os valores *temp* (temperatura) foram redimensionados de modo que todos os valores caíram no intervalo [0, 100].

## <a name="scenario-structure"></a>Estrutura do cenário

Ao abrir este projeto pela primeira vez no Azure Machine Learning Workbench, navegue até o painel *Arquivos* no lado esquerdo. Os arquivos de código a seguir são fornecidos com este exemplo:
- `1-data-preparation.ipynb` – este notebook Jupyter baixa e processa os dados para prepará-los para modelagem. Este é o primeiro notebook que será executado.
- `2-linear-regression.ipynb` – este notebook prepara um modelo de regressão linear nos dados de treinamento.
- `3-ridge.ipynb` – prepara um modelo de regressão ondulado.
- `4-ridge-poly2.ipynb` – prepara um modelo de regressão ondulado nos recursos polinomiais de nível 2.
- `5-mlp.ipynb` – prepara uma rede neural perceptron de várias camadas.
- `6-dtree.ipynb` – prepara um modelo de árvore de decisão.
- `7-gbm.ipynb` – prepara um modelo de computador gradiente aumentado.
- `8-evaluate-model.py` – este script carrega um modelo preparado e o utiliza para fazer previsões sobre um conjunto de dados mantido em teste. Ele gera métricas de avaliação do modelo para que o desempenho de modelos diferentes possa ser comparado.
- `9-forecast-output-exploration.ipynb` – este notebook produz visualizações das previsões geradas pelos modelos de aprendizado de máquina.
- `10-deploy-model.ipynb` – este notebook demonstra como um modelo de previsão preparado pode ser operacionalizado em um serviço Web em tempo real.
- `evaluate-all-models.py` – este script avalia todos os modelos preparados. Ele fornece uma alternativa à execução do `8-evaluate-model.py` para cada modelo preparado individualmente.

### <a name="1-data-preparation-and-cleaning"></a>1. Preparação e limpeza de dados

Para iniciar a execução do exemplo, primeiro clique em `1-data-preparation.ipynb` para abrir o notebook no modo de visualização. Clique em ***Iniciar Servidor do Notebook*** para iniciar um servidor do notebook Jupyter e do kernel Python em seu computador. Você pode executar os notebooks do Workbench ou usar seu navegador ao acessar [http://localhost:8888](http://localhost:8888). Observe que você deve alterar o kernel para *PROJECT_NAME local*. Você pode fazer isso do menu *Kernel* no notebook, em *Alterar kernel*. Pressione ***shift+Enter*** para executar o código em células individuais de notebooks ou clique em *Executar Tudo* no menu *Célula* para executar todo o notebook.

O notebook primeiro baixa os dados de um contêiner de Armazenamento de Blobs hospedado no Azure. Os dados, em seguida, são armazenados no seu computador no `AZUREML_NATIVE_SHARE_DIRECTORY`. Esse local é acessível de qualquer notebook ou script que você executar do Workbench, portanto, ele é um bom lugar para armazenar dados e modelos preparados.

Depois que os dados forem baixados, o notebook limpará os dados preenchendo as lacunas na série temporal e os valores ausentes por interpolação. Limpar os dados de série temporal dessa maneira maximiza a quantidade de dados disponíveis para preparar os modelos.

Vários recursos de modelo são criados usando os dados brutos limpos. Estes recursos podem ser categorizados em dois grupos:

- Os **recursos controlados por tempo** são derivados da variável *carimbo de data/hora*, por exemplo, *mês*, *dia da semana* e *hora*.
- Os **recursos de retardamento** são valores de tempo deslocados da demanda real ou dos valores de temperatura. Estes recursos visam capturar as dependências condicionais entre os períodos de tempo consecutivos no modelo.

O conjunto de dados resultante do recurso pode, então, ser usado durante o desenvolvimento de modelos de previsão.

### <a name="2-model-development"></a>2. Desenvolvimento do modelo

Uma primeira abordagem de modelagem pode ser um modelo de regressão linear simples. O notebook `2-linear-regression.ipynb` mostra como preparar um modelo de previsão de regressão linear para demandas de energia futuras. Em particular, o modelo será preparado para prever a demanda de energia de uma hora (*t+1*) antes do período de tempo atual (*t*). No entanto, é possível aplicar esse modelo de forma recursiva 'em uma etapa' no tempo de teste para gerar previsões para períodos de tempo futuros, *t+n*.

Para preparar um modelo, um pipeline preditivo é criado, o que envolve três etapas distintas:

- **Uma transformação de dados**: os formatos necessários para dados de entrada podem variar dependendo do algoritmo de aprendizado de máquina. Neste caso, o modelo de regressão linear exige que variáveis categóricas passem pela codificação one-hot.
- **Uma rotina de validação cruzada**: em geral, um modelo de aprendizado de máquina terá um ou mais hiperparâmetros que precisam ser ajustados por meio de experimentação. A validação cruzada pode ser usada para localizar o conjunto ideal de valores de parâmetro. O modelo é preparado repetidamente e avaliado em partições diferentes do conjunto de dados. Os melhores parâmetros são aqueles que obtém o melhor desempenho de modelo quando calculado entre as dobras de validação cruzada. Este processo é explicado com mais detalhes em `2-linear-regression.ipynb`.
- **Treinamento do modelo final**: o modelo é preparado no conjunto de dados inteiro, usando o melhor conjunto de hiperparâmetros.

Incluímos uma série de seis modelos diferentes em notebooks numerados de 2 a 7. Cada notebook prepara um modelo diferente e o armazena no local `AZUREML_NATIVE_SHARE_DIRECTORY`. Uma vez que você tenha preparado todos os modelos desenvolvidos para este cenário, poderemos avaliá-los e compará-los, como descrito na próxima seção.

### <a name="3-model-evaluation-and-comparison"></a>3. Avaliação e comparação de modelos

Podemos usar um modelo preparado para fazer previsões para períodos futuros. É melhor avaliar esses modelos em um conjunto de dados mantidos em teste. Ao avaliarmos os modelos diferentes no mesmo conjunto de dados não vistos, podemos comparar seu desempenho com precisão e identificar o melhor modelo. Neste cenário, aplicamos o modelo preparado de forma recursiva para prevermos várias etapas de tempo no futuro. Em particular, geramos previsões por até seis horas, *t+6* à frente da hora atual, *t*. Essas previsões são avaliadas em relação à demanda real observada para cada período.

Para avaliar um modelo, abra o script `8-evaluate-model.py` do painel *Arquivos* no Workbench. Verifique se a *Configuração de Execução* está definida como **Local** e, em seguida, digite o nome do modelo no campo *Argumentos*. O nome do modelo deve corresponder exatamente à variável *model_name* definida no início do notebook no qual o modelo é preparado. Por exemplo, digite "linear_regression" para avaliar o modelo de regressão linear preparado. Como alternativa, depois que todos os modelos foram preparados, você poderá avaliá-los com um comando, executando `evaluate-all-models.py`. Para executar este script, abra um prompt de comando do Workbench e execute o seguinte comando:

```
python evaluate-all-models.py
```
O script `8-evaluate-model.py` executa as operações a seguir:

- Carrega um pipeline preparado do disco
- Faz previsões no conjunto de dados de teste
- Calcula as métricas de desempenho do modelo e as registra em log
- Salva as previsões do conjunto de dados de teste em `AZUREML_NATIVE_SHARE_DIRECTORY` para inspeção e análise posteriores
- Salva o modelo preparado do pipeline na pasta *saídas*.

> [!Note]
> Salvar o modelo na pasta *saídas* copia automaticamente o objeto de modelo na Conta de Armazenamento de Blobs do Azure associada à sua conta de experimentação. Isso significa que você sempre poderá recuperar o objeto de modelo salvo do blob, mesmo se alterar o computador ou o contexto de computação.

Navegue até o painel *Histórico de execuções* e clique em `8-evaluate-model.py`. Você verá gráficos que exibem várias métricas de desempenho do modelo:

- **ME**: erro médio da previsão. Ele pode ser interpretado como o desvio médio da previsão.
- **MPE**: [erro de percentual médio](https://en.wikipedia.org/wiki/Mean_percentage_error) (ME expresso como um percentual da demanda real)
- **MSE**: [erro quadrático médio](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**: raiz do erro quadrático médio (a raiz quadrada do MSE)
- **MAPE**: [erro percentual médio absoluto](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**: [erro percentual médio absoluto simétrico](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**: o MAPE de uma previsão de linha de base na qual a previsão é igual ao último valor conhecido de demanda.
- **MdRAE**: erro absoluto mediano relativo. O taxa mediana do erro de previsão para o erro de previsão da linha de base. Um valor inferior a 1 significa que a previsão está com um desempenho melhor do que a linha de base.

Todas as métricas acima se referem à previsão *t+1*. Além das métricas acima, você também verá um conjunto de métricas correspondentes com o sufixo *_horizon*. Esta é a média calculada em todos os períodos no intervalo de previsão do período *t+1* para o período *t+6*.

Se as métricas não forem exibidas na área do gráfico, clique no símbolo de engrenagem no canto superior direito. Certifique-se de que as métricas de desempenho de modelo nas quais você está interessado foram verificadas. As métricas também aparecerão em uma tabela abaixo dos gráficos. Novamente, esta tabela pode ser personalizada ao clicar no símbolo de engrenagem. Classifique a tabela por uma métrica de desempenho para identificar o melhor modelo. Se você estiver interessado em ver como o desempenho da previsão varia do período *t+1* ao *t+6*, clique na entrada do modelo na tabela. Os gráficos que exibem as métricas MAPE, MPE e MdRAE ao longo do período de previsão são mostrados em *Visualizações*.

Ao avaliar os modelos de previsão, pode ser muito útil visualizar as previsões de saída. Isso ajuda o cientista de dados a determinar se a previsão gerada parece realista. Isso também poderá ajudar a identificar problemas na previsão se ela for mal executada em determinados períodos, por exemplo. O notebook `9-forecast-output-exploration.ipynb` produzirá visualizações das previsões geradas para o conjunto de dados de teste.

### <a name="4-deployment"></a>4. Implantação

O melhor modelo pode ser operacionalizado ao implantá-lo como um serviço Web em tempo real. Este serviço Web poderá, então, ser invocado para gerar previsões sob demanda à medida que novos dados se tornarem disponíveis. Neste cenário, uma nova previsão precisa ser gerada a cada hora para prever a demanda de energia na hora subsequente. Para executar esta tarefa, um serviço Web poderá ser implantado, o que considera uma série de recursos de um determinado período em horas como entrada e retorna a demanda prevista como saída.

Neste exemplo, um serviço Web é implantado em um computador com o Windows 10. Verifique se você concluiu as etapas de pré-requisito para a implantação local estabelecidas neste [Guia de Introdução](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) para a CLI de Operacionalização. Depois de configurar seu ambiente local e a conta de gerenciamento de modelos, execute o notebook `10-deploy-model.ipynb` para implantar o serviço Web.

## <a name="conclusion"></a>Conclusão

Este exemplo demonstra como criar uma solução de previsão de séries temporais de ponta a ponta, com o objetivo de prever a demanda de energia. Muitos dos princípios explorados neste exemplo podem ser ampliados para outros setores e cenários de previsão.

Este cenário mostra como o Azure Machine Learning Workbench pode ajudar um cientista de dados no desenvolvimento de soluções do mundo real com recursos úteis, como o ambiente de notebook Jupyter e os recursos de registro de métricas. O exemplo também orienta o leitor sobre como um modelo pode ser operacionalizado e implantado usando a CLI de Operacionalização do Azure Machine Learning. Uma vez implantada, a API do serviço Web permite aos desenvolvedores ou engenheiros de dados integrarem o modelo de previsão em um pipeline de dados mais amplo.
