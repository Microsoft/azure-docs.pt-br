---
title: Criar e explorar experimentos no portal
titleSuffix: Azure Machine Learning service
description: Saiba como criar e gerenciar experiências automatizadas de aprendizado de máquina no portal
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 1bfc415b2e4dbc66e2afeae73b78079fb027a60c
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358837"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Criar e explorar experimentos automatizados de aprendizado de máquina na portal do Azure (versão prévia)

 Neste artigo, você aprenderá a criar, executar e explorar experiências automatizadas de aprendizado de máquina no portal do Azure sem uma única linha de código. O aprendizado de máquina automatizado automatiza o processo de seleção do melhor algoritmo a ser usado para seus dados específicos, para que você possa gerar um modelo de aprendizado de máquina rapidamente. [Saiba mais sobre o aprendizado de máquina automatizado](concept-automated-ml.md).

 Se você preferir uma experiência mais baseada em código, também poderá [configurar seus experimentos de aprendizado de máquina automatizados no Python](how-to-configure-auto-train.md) com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um workspace de serviço do Azure Machine Learning. Consulte [criar um Azure Machine Learning espaço de trabalho de serviço](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Introdução

Navegue até o painel esquerdo do seu espaço de trabalho. Selecione Machine Learning automatizado na seção criação (visualização).

![Painel de navegação portal do Azure](media/how-to-create-portal-experiments/nav-pane.png)

 Se esta for a primeira vez que você faz qualquer experimento com Machine Learning automatizado, você verá o seguinte:

![Página de aterrissagem do portal do Azure experimento](media/how-to-create-portal-experiments/landing-page.png)

Caso contrário, você verá o painel automatizado do Machine Learning com uma visão geral de todos os seus experimentos de aprendizado de máquina automatizados, incluindo aqueles criados com o SDK. Aqui você pode filtrar e explorar suas execuções por data, nome do experimento e status de execução.

![Painel do experimento do portal do Azure](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Criar uma experiência

Selecione o botão criar experimento para preencher o formulário a seguir.

![Criar formulário de experimento](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Insira seu nome de experimento.

1. Selecione uma computação para o trabalho de criação de perfil de dados e treinamento. Uma lista de suas computações existentes está disponível na lista suspensa. Para criar uma nova computação, siga as instruções na etapa 3.

1. Selecione o botão criar uma nova computação para abrir o painel abaixo e configure seu contexto de computação para este experimento.

    ![Criar nova computação para experimento](media/how-to-create-portal-experiments/create-new-compute.png)

    Campo|Descrição
    ---|---
    Nome de computação| Insira um nome exclusivo que identifique o contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho da máquina virtual para sua computação.
    Configurações adicionais| *Nó mínimo*: Insira o número mínimo de nós para a computação. O número mínimo de nós para a computação AML é 0. Para habilitar a criação de perfil de dados, você deve ter um ou mais nós. <br> *Nó máximo*: Insira o número máximo de nós para sua computação. O padrão é 6 nós para uma computação AML.

      Para iniciar a criação de sua nova computação, selecione **criar**. Isso pode levar alguns minutos.

      >[!NOTE]
      > Seu nome de computação indicará se a computação que você selecionou/criar está com a *criação de perfil habilitada*. (Consulte 7B para obter mais detalhes sobre a criação de perfil de dados).

1. Selecione uma conta de armazenamento para seus dados. A visualização pública dá suporte apenas a carregamentos de arquivos locais e contas de armazenamento de BLOBs do Azure.

1. Selecione um contêiner de armazenamento.

1. Selecione um arquivo de dados do seu contêiner de armazenamento ou carregue um arquivo do seu computador local para o contêiner.

    ![Selecionar arquivo de dados para experimento](media/how-to-create-portal-experiments/select-file.png)

1. Use as guias Visualizar e perfil para configurar ainda mais seus dados para este experimento.

    1. Na guia Visualização, indique se os dados incluem cabeçalhos e selecione os recursos (colunas) para treinamento usando os botões de opção **incluídos** em cada coluna de recurso.

        ![Visualização de dados](media/how-to-create-portal-experiments/data-preview.png)

    1. Na guia perfil, você pode exibir o [perfil de dados](#profile) por recurso, bem como a distribuição, o tipo e as estatísticas de resumo (Mean, Median, Max/min e assim por diante) de cada um.

        ![Guia perfil de dados](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > A seguinte mensagem de erro será exibida se o contexto de computação **não** estiver com a criação de perfil habilitada: A *criação de perfil de dados só está disponível para destinos de computação que já estão em execução*.

1. Selecione o tipo de trabalho de treinamento: classificação, regressão ou previsão.

1. Selecione a coluna de destino. A coluna na qual você gostaria de fazer as previsões.

1. Para previsão:
    1. Selecione a coluna de tempo: Esta coluna contém os dados de tempo a serem usados.
    1. Selecione o horizonte de previsão: Indique quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever para o futuro. Quanto mais o modelo for necessário para prever no futuro, menor será a sua precisão. [Saiba mais sobre previsão e previsão horizonte](how-to-auto-train-forecast.md).

1. Adicional Configurações avançadas: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento.

    Configurações avançadas|Descrição
    ------|------
    Métrica primária| Métrica principal usada para pontuar seu modelo. [Saiba mais sobre métricas de modelo](how-to-configure-auto-train.md#explore-model-metrics).
    Critérios de saída| Quando qualquer um desses critérios é atendido, o trabalho de treinamento termina antes da conclusão completa. <br> *Tempo de trabalho de treinamento (minutos)* : Por quanto tempo permitir que o trabalho de treinamento seja executado.  <br> *Número máximo de iterações*: Número máximo de pipelines (iterações) a serem testados no trabalho de treinamento. O trabalho não será executado mais do que o número especificado de iterações. <br> *Limite de pontuação da métrica*:  Pontuação de métrica mínima para todos os pipelines. Isso garante que, se você tiver uma métrica de destino definida que deseja alcançar, não gaste mais tempo no trabalho de treinamento do que o necessário.
    Pré-processamento| Selecione para habilitar ou desabilitar o pré-processamento feito pelo Machine Learning automatizado. O pré-processamento inclui a limpeza, preparação e transformação automáticas de dados para gerar recursos sintéticos. [Saiba mais sobre o pré-processamento](#preprocess).
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre a validação cruzada](how-to-configure-auto-train.md).
    Simultaneidade| Selecione os limites de vários núcleos que você gostaria de usar ao usar a computação de vários núcleos.
    Algoritmo bloqueado| Selecione os algoritmos que você deseja excluir do trabalho de treinamento.

   ![Formulário de configurações avançadas](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Para obter mais informações sobre os campos, clique na dica da ferramenta informações.

<a name="profile"></a>

### <a name="data-profiling"></a>Criação de perfil de dados

Você pode obter uma grande variedade de estatísticas de resumo em seu conjunto de dados para verificar se o conjunto de dados está pronto para ML. Para colunas não numéricas, elas incluem apenas estatísticas básicas, como mín., máx. e contagem de erros. Para colunas numéricas, você também pode revisar seus momentos estatísticos e quantis estimados. Especificamente, nosso perfil de dados inclui:

* **Recurso**: o nome da coluna que está sendo resumida.

* **Perfil**: uma visualização em linha com base no tipo inferido. Por exemplo, cadeias de caracteres, Boolianos e datas terão contagens de valor, enquanto decimais (numéricos) têm histogramas aproximados. Isso permite que você tenha uma compreensão rápida da distribuição dos dados.

* **Distribuição de tipos**: uma contagem de valores em linha de tipos dentro de uma coluna. Os nulos são de seu próprio tipo, portanto, essa visualização é útil para detectar valores ímpares ou ausentes.

* **Tipo**: o tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, Boolianos, datas e decimais.

* **Min**: o valor mínimo da coluna. Entradas em branco aparecem para recursos cujo tipo não tem uma ordenação inerente (por exemplo, Boolianos).

* **Max**: o valor máximo da coluna. Como as entradas em branco "min", aparecem para recursos com tipos irrelevantes.

* **Contagem**: o número total de entradas ausentes e não ausentes na coluna.

* **Contagem ausente**: o número de entradas na coluna que não estão ausentes. Observe que as cadeias de caracteres vazias e os erros são tratados como valores, de modo que não irão contribuir para a "contagem não encontrada".

* **Quantis** (às 0,1, 1, 5, 25, 50, 75, 95, 99 e 99,9% intervalos): os valores aproximados em cada Quantil para fornecer uma noção da distribuição dos dados. Entradas em branco aparecem para recursos com tipos irrelevantes.

* **Mean**: a média aritmética da coluna. Entradas em branco aparecem para recursos com tipos irrelevantes.

* **Desvio padrão**: o desvio padrão da coluna. Entradas em branco aparecem para recursos com tipos irrelevantes.

* **Variância**: a variação da coluna. Entradas em branco aparecem para recursos com tipos irrelevantes.

* **Distorção**: a distorção da coluna. Entradas em branco aparecem para recursos com tipos irrelevantes.

* **Curtose**: a curtose da coluna. Entradas em branco aparecem para recursos com tipos irrelevantes.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Pré-processamento avançado

Ao configurar seus experimentos, você pode habilitar a configuração `Preprocess`avançada. Isso significa que as etapas de pré-processamento e personalização de dados a seguir são executadas automaticamente.

|&nbsp;Etapas de pré-processamento| Descrição |
| ------------- | ------------- |
|Remover alta cardinalidade ou nenhum recurso de variação|Descarte-os dos conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou com cardinalidade extremamente alta (por exemplo, hashes, IDs ou GUIDs).|
|Acrescentar valores ausentes|Para recursos numéricos, imputar com a média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|Gerar recursos adicionais|Para recursos DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos Text: A frequência do termo com base em unigrams, bi-grams e Tri-Character-grams.|
|Transformar e codificar |Recursos numéricos com poucos valores exclusivos são transformados em recursos categóricos.<br/><br/>A codificação One-Hot é executada para uma baixa cardinalidade categórica; para alta cardinalidade, codificação One-Hot-hash.|
|Incorporações de palavras|Texto featurizer que converte vetores de tokens de texto em vetores de sentença usando um modelo pré-treinado. O vetor de incorporação de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso de documento.|
|Codificações de destino|Para recursos categóricos, o mapeia cada categoria com o valor de destino médio para problemas de regressão e a probabilidade de classe de cada classe para problemas de classificação. O peso baseado em frequência e a validação cruzada de k-fold são aplicados para reduzir o ajuste do mapeamento e do ruído causados por categorias de dados esparsas.|
|Codificação de destino de texto|Para entrada de texto, um modelo linear empilhado com conjunto de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (WoE)|Calcula WoE como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de fora de classe vs in-Class. Esta etapa gera uma coluna de recurso numérico por classe e remove a necessidade de imputar explicitamente os valores ausentes e o tratamento de exceção.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Gera novos recursos e um novo recurso numérico por cluster, contendo a distância de cada amostra para o centróide de cada cluster.|

## <a name="run-experiment-and-view-results"></a>Executar experimento e exibir resultados

Para executar o experimento, clique em Iniciar. O processo de preparação do experimento leva alguns minutos.

### <a name="view-experiment-details"></a>Exibir detalhes do experimento

Depois que a fase de preparação do experimento for concluída, você verá a tela de detalhes da execução. Isso fornece uma lista completa dos modelos criados. Por padrão, o modelo que pontua o mais alto com base em seus parâmetros está na parte superior da lista. Como o trabalho de treinamento tenta mais modelos, eles são adicionados à lista de iterações e ao gráfico. Use o gráfico de iteração para obter uma comparação rápida das métricas para os modelos produzidos até agora.

Os trabalhos de treinamento podem levar algum tempo para que cada pipeline termine a execução.

![Painel de detalhes de execução](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>Exibir detalhes da execução de treinamento

Faça uma busca detalhada em qualquer um dos modelos de saída para ver detalhes de execução de treinamento, como métricas de desempenho e gráficos de distribuição. [Saiba mais sobre gráficos](how-to-understand-automated-ml.md).

![Detalhes da iteração](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>Implantar modelo

Quando você tem o melhor modelo em mãos, é hora de implantá-lo como um serviço Web para prever novos dados.

O ML automatizado ajuda você a implantar o modelo sem escrever código:

1. Você tem algumas opções de implantação. 
    1. Se você quiser implantar o melhor modelo com base nos critérios de métrica definidos para o experimento, selecione **implantar melhor modelo** na página **executar detalhes** .

        ![Botão implantar modelo](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. Se você quiser implantar uma iteração de modelo específica, faça uma busca detalhada no modelo para abrir sua página de detalhes de execução específica e selecione **implantar modelo**.

        ![Botão implantar modelo](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. A primeira etapa é registrar o modelo no serviço. Selecione "registrar modelo" e aguarde a conclusão do processo de registro.

    ![Folha implantar modelo](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. Depois que o modelo for registrado, você poderá baixar o script de Pontuação (scoring.py) e o script de ambiente (condaEnv. yml) a ser usado durante a implantação.

1. Quando o script de Pontuação e o script do ambiente forem baixados, vá para a folha **ativos** do painel de navegação esquerdo e selecione **modelos**.

    ![Modelos de painel de navegação](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Selecione o modelo que você registrou e selecione "criar imagem".

    Você pode identificar o modelo por sua descrição, que incluirá a ID de execução, o número de iteração, no seguinte formato: *< Run_ID > _ < Iteration_number > _Model*

    ![Modelos: Criar imagem](media/how-to-create-portal-experiments/model-create-image.png)

1. Insira um nome para a imagem. 
1. Selecione o botão **procurar** ao lado da caixa "arquivo de Pontuação" para carregar o arquivo de pontuação (Scoring.py) que você baixou anteriormente.

1. Selecione o botão **procurar** ao lado da caixa "arquivo Conda" para carregar o arquivo de ambiente (condaEnv. yml) que você baixou anteriormente.

    Você pode usar seu próprio script de Pontuação e arquivo Conda, bem como carregar arquivos adicionais. [Saiba mais sobre o script de Pontuação](how-to-deploy-and-where.md#script).

      >[!Important]
      > Os nomes de arquivo devem ter menos de 32 caracteres e devem começar e terminar com alfanuméricos. Pode incluir traços, sublinhados, pontos e alfanuméricos entre. Não são permitidos espaços.

    ![Criar imagem](media/how-to-create-portal-experiments/create-image.png)

1. Selecione o botão "criar" para iniciar a criação da imagem. Isso levará alguns minutos para ser concluído, uma vez que você verá uma mensagem na barra superior.
1. Vá para a guia "imagens", marque a caixa de seleção ao lado da imagem que você deseja implantar e selecione "criar implantação". [Saiba mais sobre](how-to-deploy-and-where.md)implantações.

    Há duas opções de implantação.
     + ACI (instância de contêiner do Azure)-isso é usado mais para testar a finalidade em vez da implantação operacional em escala. Certifique-se de preencher os valores de pelo menos um núcleo para a _capacidade de reserva de CPU_e pelo menos um gigabyte (GB) para a capacidade de reserva de _memória_
     + AKS (serviço kubernetes do Azure)) – esta opção é para implantação em escala. Você precisará ter uma computação com base em AKS pronta.

     ![Imagens Criar implantação](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Ao terminar, selecione **Criar**. A implantação do modelo pode levar vários minutos para que cada pipeline termine a execução.

1. É só isso! Você tem um serviço Web operacional para gerar previsões.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o aprendizado de máquina](concept-automated-ml.md) e a Azure Machine Learning automatizados.
* [Entenda os resultados automatizados do Machine Learning](how-to-understand-automated-ml.md).
* [Saiba como consumir um serviço Web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
