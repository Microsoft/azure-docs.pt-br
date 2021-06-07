---
title: 'ML Studio (clássico): migrar para Azure Machine Learning'
description: Migre do Studio (clássico) para Azure Machine Learning para uma plataforma de ciência de dados modernizada.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 7cdf016d7de66c33973ec7818fa92df28c4639dd
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566941"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrar para o Azure Machine Learning

Neste artigo, você aprenderá a migrar ativos do estúdio (clássico) para Azure Machine Learning. Neste momento, para migrar recursos, você deve recompilar seus experimentos manualmente.

O Azure Machine Learning fornece uma plataforma de ciência de dados modernizada que combina abordagens sem código e por código. Para saber mais sobre as diferenças entre Studio (clássico) e Azure Machine Learning, consulte a seção [avaliar Azure Machine Learning](#step-1-assess-azure-machine-learning) .


## <a name="recommended-approach"></a>Abordagem recomendada

Para migrar para o Azure Machine Learning, recomendamos a seguinte abordagem:

> [!div class="checklist"]
> * Etapa 1: avaliar Azure Machine Learning
> * Etapa 2: criar um plano de migração
> * Etapa 3: recompilar experimentos e serviços Web
> * Etapa 4: integrar aplicativos cliente
> * Etapa 5: limpar ativos do estúdio (clássico)


## <a name="step-1-assess-azure-machine-learning"></a>Etapa 1: avaliar Azure Machine Learning
1. Saiba mais sobre o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/); são os benefícios, os custos e a arquitetura.

1. Compare os recursos do Azure Machine Learning e do Studio (clássico).

    >[!NOTE]
    > O recurso de **Designer** no Azure Machine Learning fornece uma experiência de arrastar e soltar semelhante ao Studio (clássico). No entanto, Azure Machine Learning também fornece [fluxos de trabalho robustos de Code-First](../concept-model-management-and-deployment.md) como uma alternativa. Esta série de migração se concentra no designer, pois é mais semelhante à experiência do estúdio (clássico).

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Verifique se há suporte para seus módulos do estúdio (clássico) no Azure Machine Learning designer. Para obter mais informações, consulte a tabela de [mapeamento de módulo do Studio (clássico) e do designer](#studio-classic-and-designer-module-mapping) abaixo.

4. [Criar um workspace do Azure Machine Learning](../how-to-manage-workspace.md?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>Etapa 2: criar um plano de migração

1. Identifique os **conjuntos de dados**, os **modelos** e os **Serviços Web** do estúdio (clássico) que você deseja migrar.

1. Determine o impacto que uma migração terá em sua empresa.

1. Crie um plano de migração.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Etapa 3: recompilar experimentos e serviços Web

1. [Migre conjuntos de os para Azure Machine Learning](migrate-register-dataset.md).
1. Use o designer para [Recompilar experimentos](migrate-rebuild-experiment.md).
1. Use o designer para [reimplantar os serviços Web](migrate-rebuild-web-service.md).

    >[!NOTE]
    > O Azure Machine Learning também dá suporte a fluxos de trabalho de primeiro código para [conjuntos](../how-to-create-register-datasets.md)de, [treinamento](../how-to-set-up-training-targets.md)e [implantação](../how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Etapa 4: integrar aplicativos cliente

1. Modifique os aplicativos cliente que chamam os serviços Web do estúdio (clássico) para usar seus novos [pontos de extremidade de Azure Machine Learning](migrate-rebuild-integrate-with-client-app.md).

## <a name="step-5-cleanup-studio-classic-assets"></a>Etapa 5: ativos do Cleanup Studio (clássico)

1. [Limpe os ativos do estúdio (clássico)](export-delete-personal-data-dsr.md) para evitar encargos adicionais. Talvez você queira manter ativos para fallback até que tenha validado Azure Machine Learning cargas de trabalho.

## <a name="studio-classic-and-designer-module-mapping"></a>Módulo do Studio (clássico) e do designer-mapeamento

Consulte a tabela a seguir para ver quais módulos usar ao recompilar experimentos de estúdio (clássico) no designer.


> [!IMPORTANT]
> O designer implementa módulos por meio de pacotes python de software livre em vez de pacotes C#, como o Studio (clássico). Devido a essa diferença, a saída de módulos de designer pode variar um pouco de suas contrapartes de estúdio (clássicas).


|Categoria|Módulo Studio (clássico)|Módulo do designer de substituição|
|--------------|----------------|--------------------------------------|
|Entrada e saída de dados|-Inserir dados manualmente </br> -Exportar dados </br> -Importar dados </br> -Modelo treinado para carregamento </br> -Desempacotar conjuntos de valores compactados|-Inserir dados manualmente </br> -Exportar dados </br> -Importar dados|
|Conversões de formato de dados|-Converter em CSV </br> -Converter em conjunto de um </br> -Converter em ARFF </br> -Converter em SVMLight </br> -Converter em TSV|-Converter em CSV </br> -Converter em conjunto de um|
|Transformação de dados-manipulação|-Adicionar colunas</br> -Adicionar linhas </br> – Aplicar transformação SQL </br> -Limpando dados ausentes </br> -Converter para valores de indicador </br> -Editar metadados </br> -Dados de junção </br> -Remover linhas duplicadas </br> -Selecionar colunas no conjunto de linhas </br> -Selecionar transformação de colunas </br> -SMOTE </br> -Agrupar valores categóricos|-Adicionar colunas</br> -Adicionar linhas </br> – Aplicar transformação SQL </br> -Limpando dados ausentes </br> -Converter para valores de indicador </br> -Editar metadados </br> -Dados de junção </br> -Remover linhas duplicadas </br> -Selecionar colunas no conjunto de linhas </br> -Selecionar transformação de colunas </br> -SMOTE|
|Transformação de dados – escala e redução |-Recortar valores </br> -Agrupar dados em compartimentos </br> -Normalizar dados </br>-Análise de componente principal |-Recortar valores </br> -Agrupar dados em compartimentos </br> -Normalizar dados|
|Transformação de dados – amostra e divisão|-Partição e exemplo </br> -Dividir dados|-Partição e exemplo </br> -Dividir dados|
|Transformação de dados – filtro |-Aplicar filtro </br> -Filtro FIR </br> -Filtro de IIR </br> -Filtro mediano </br> -Filtro de média de movimentação </br> -Filtro de limite </br> -Filtro definido pelo usuário||
|Transformação de dados – aprendendo com contagens |-Transformação contagem de compilação </br> -Exportar tabela de contagem </br> -Tabela de contagem de importação </br> -Transformação de contagem de mesclagem</br>  -Modificar parâmetros da tabela de contagem||
|Seleção de recursos |-Seleção de recursos baseada em filtro </br> -Análise de discriminante linear de Fisher  </br> -Importância do recurso de permutação |-Seleção de recursos baseada em filtro </br>  -Importância do recurso de permutação|
| Modelo-classificação| -Floresta de decisão multiclasse </br> -Selva de decisão multiclasse  </br> -Regressão logística multiclasse  </br>-Rede neural multiclasse  </br>-Multiclasse Um contra Todos </br>-Two-Class perceptron média </br>-Two-Class máquina do ponto de Bayes </br>-Árvore de decisão aumentada Two-Class  </br> -Two-Class floresta de decisão  </br> -Two-Class selva de decisão  </br> -Two-Class Locally-Deep SVM </br> -Regressão logística Two-Class  </br> -Two-Class rede neural </br> -Two-Class computador de vetor de suporte  | -Floresta de decisão multiclasse </br>  -Árvore de decisão de aumento multiclasse  </br> -Regressão logística multiclasse </br> -Rede neural multiclasse </br> -Multiclasse Um contra Todos  </br> -Two-Class perceptron média  </br> -Árvore de decisão aumentada Two-Class  </br> -Two-Class floresta de decisão </br>-Regressão logística Two-Class </br> -Two-Class rede neural </br>-Two-Class computador de vetor de suporte  |
| Clustering de modelos| -K-significa clustering| -K-significa clustering|
| Modelo-regressão| -Regressão linear Bayesiana  </br> -Regressão da árvore de decisão aumentada  </br>-Regressão de floresta de decisão  </br> -Regressão de Quantil de floresta rápida  </br> -Regressão linear </br> -Regressão de rede neural </br> -Regressão Poisson de regressão do ordinal| -Regressão da árvore de decisão aumentada  </br>-Regressão de floresta de decisão  </br> -Regressão de Quantil de floresta rápida </br> -Regressão linear  </br> -Regressão de rede neural </br> -Regressão Poisson|
| Modelo – detecção de anomalias| -One-Class SVM  </br> -Detecção de anomalias de PCA-Based | -Detecção de anomalias de PCA-Based|
| Machine Learning – avaliar  | -Modelo de validação cruzada  </br>-Avaliar modelo  </br>-Avaliar Recomendador | -Modelo de validação cruzada  </br>-Avaliar modelo </br> -Avaliar Recomendador|
| Machine Learning – treinamento| -Varredura de clustering  </br> -Modelo de detecção de anomalias de treinamento </br>-Treinar modelo de clustering  </br> -Treinar Recomendador Matchbox-</br> Treinar Modelo  </br>-Ajustar hiperparâmetros de modelo| -Modelo de detecção de anomalias de treinamento  </br> -Treinar modelo de clustering </br> -Modelo de treinamento-</br> -Treinar modelo PyTorch  </br>-Treinar Recomendador SVD  </br>– Recomendação de treinamento amplo e profundo </br>-Ajustar hiperparâmetros de modelo|
| Machine Learning – Pontuação| -Aplicar transformação  </br>-Atribuir dados a clusters  </br>-Matchbox Recomendador de Pontuação </br> -Modelo de Pontuação|-Aplicar transformação  </br> -Atribuir dados a clusters </br> -Modelo de imagem de Pontuação  </br> -Modelo de Pontuação </br>-SVD Recomendador de Pontuação </br> – Recomendado pela pontuação de largura e profundo|
| Módulos de biblioteca OpenCV| -Importar imagens </br>-Classificação de imagem em cascata pré-treinada | |
| Módulos de linguagem Python| -Executar script Python| -Executar script Python  </br> -Criar modelo Python |
| Módulos de linguagem R  | -Executar script R  </br> -Criar modelo de R| -Executar script R|
| Funções estatísticas | -Aplicar operação matemática </br>– Estatísticas elementares de computação  </br>-Correlação linear de computação  </br>-Função de probabilidade de avaliação  </br>-Substituir valores discretos  </br>-Resumir dados  </br>-Hipótese de teste usando o t-test| -Aplicar operação matemática  </br>-Resumir dados|
| Análise de Texto| -Detectar idiomas  </br>-Extrair frases-chave do texto  </br>-Extrair recursos de N-Gram de texto  </br>-Hash de recurso </br>-Alocação de Dirichlet latente  </br>-Reconhecimento de entidade nomeada </br>-Pré-processar texto  </br>-Score Vowpal Wabbit versão 7-10 modelo  </br>-Score Vowpal Wabbit versão 8 modelo </br>-Treinar modelo do Vowpal Wabbit versão 7-10  </br>-Treinar modelo do Vowpal Wabbit versão 8 |-Converter palavra em vetor </br> -Extrair recursos de N-Gram de texto </br>-Hash de recurso  </br>-Alocação de Dirichlet latente </br>-Pré-processar texto  </br>-Pontuação Vowpal Wabbit Model </br> -Treinar modelo de Wabbit Vowpal|
| Série temporal| -Detecção de anomalias de série temporal | |
| Serviço Web | -Entrada </br> -Saída | -Entrada </br>  - Saída|
| Pesquisa Visual Computacional| | -Aplicar transformação de imagem </br> -Converter em diretório de imagens </br> -Transformação imagem de inicialização </br> -Dividir diretório de imagens  </br> -Classificação de imagem DenseNet   </br>-Classificação de imagem ResNet |

Para obter mais informações sobre como usar os módulos de designer individuais, consulte a [referência de módulo do designer](../algorithm-module-reference/module-reference.md).

### <a name="what-if-a-designer-module-is-missing"></a>E se um módulo de designer estiver faltando?

O designer de Azure Machine Learning contém os módulos mais populares do estúdio (clássico). Ele também inclui novos módulos que aproveitam as técnicas de aprendizado de máquina mais recentes. 

Se a migração for bloqueada devido a módulos ausentes no designer, entre em contato conosco [criando um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Migração de exemplo

A migração de teste a seguir realça algumas das diferenças entre o Studio (clássico) e Azure Machine Learning.

### <a name="datasets"></a>Conjunto de dados

No Studio (clássico), os **conjuntos de valores** foram salvos em seu espaço de trabalho e podem ser usados apenas pelo estúdio (clássico).

![automóvel-Price-clássico-DataSet](./media/migrate-overview/studio-classic-dataset.png)

No Azure Machine Learning, os **conjuntos de valores** são registrados no espaço de trabalho e podem ser usados em todas as Azure Machine Learning. Para obter mais informações sobre os benefícios dos conjuntos de dados do Azure Machine Learning, consulte [proteger o acesso a dados](../concept-data.md#reference-data-in-storage-with-datasets).

![automóvel-Price-AML-DataSet](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Pipeline

No estúdio (clássico), os **experimentos** continham a lógica de processamento para seu trabalho. Você criou experimentos com os módulos arrastar e soltar.


![automóvel-preço-clássico-experimento](./media/migrate-overview/studio-classic-experiment.png)

No Azure Machine Learning, **pipelines** contêm a lógica de processamento para seu trabalho. Você pode criar pipelines com módulos de arrastar e soltar ou escrevendo código.

![automóvel-Price-AML-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Ponto de extremidade de serviço Web

No Studio (clássico), a **API de solicitação/resposta** foi usada para previsão em tempo real. A **API de execução em lote** foi usada para previsão ou readaptação de lote.

![automóvel-preço-clássico-WebService](./media/migrate-overview/studio-classic-web-service.png)

Em Azure Machine Learning, os **pontos de extremidade em tempo real** são usados para previsão em tempo real. **Pontos de extremidade de pipeline** são usados para previsão de lote ou readaptação.

![automóvel-Price-AML-Endpoint](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu os requisitos de alto nível para migrar para o Azure Machine Learning. Para obter etapas detalhadas, consulte os outros artigos na série de migração do Studio (clássico):

1. **Visão geral da migração**.
1. [Migrar conjunto](migrate-register-dataset.md)de um.
1. [Recompile um pipeline de treinamento do Studio (clássico)](migrate-rebuild-experiment.md).
1. [Recompile um serviço Web Studio (clássico)](migrate-rebuild-web-service.md).
1. [Integre um serviço web Azure Machine Learning com aplicativos cliente](migrate-rebuild-integrate-with-client-app.md).
1. [Migrar executar script R](migrate-execute-r-script.md).