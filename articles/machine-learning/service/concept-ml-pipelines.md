---
title: 'Pipelines: fluxos de trabalho de aprendizado de máquina de otimizar'
titleSuffix: Azure Machine Learning service
description: Neste artigo, aprenda sobre os pipelines de aprendizado de máquina que você pode criar com o SDK do Azure Machine Learning para Python e as vantagens de usar pipelines. Pipelines de ML (aprendizado de máquina) são usados por cientistas de dados para criar, otimizar e gerenciar seus fluxos de trabalho de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 12/4/2018
ms.custom: seodec18
ms.openlocfilehash: 3f1dd0921153d6b65bdc257f91019483adbb18fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821065"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Criar pipelines de aprendizado de máquina com o Serviço do Azure Machine Learning

Neste artigo, saiba mais sobre os pipelines de aprendizado de máquina que você pode criar com o SDK do Azure Machine Learning para Python, bem como as vantagens de usá-los.

## <a name="what-are-machine-learning-pipelines"></a>O que são pipelines de aprendizado de máquina?

Usando pipelines de aprendizado de máquina (ML), os cientistas de dados, os engenheiros de dados e os profissionais de TI podem colaborar com as etapas envolvidas em:
+ Preparação de dados, como normalizações e transformações
+ Treinamento do modelo
+ Avaliação do modelo
+ Implantação 

O diagrama a seguir mostra um exemplo de pipeline:

![Pipelines de aprendizado de máquina no Serviço do Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>Qual tecnologia de pipeline do Azure deve usar?

A nuvem do Azure fornece vários outros pipelines, cada um com uma finalidade diferente. A tabela a seguir lista os diferentes pipelines e que elas são usadas para:

| Pipeline | O que faz | Pipe canônico |
| ---- | ---- | ---- |
| Pipelines de Machine Learning do Azure | Define fluxos de trabalho que podem ser usados para cenários de aprendizado de máquina como um modelo de aprendizado de máquina reutilizável. | Dados de modelo -> |
| [Pipelines do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Atividades de controle, transformação e movimentação de dados de grupos necessários para executar uma tarefa.  | Data -> data |
| [Pipelines do Azure](https://azure.microsoft.com/services/devops/pipelines/) | Integração contínua e entrega do seu aplicativo para qualquer plataforma/qualquer nuvem  | Código de aplicativo/serviço-> |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Por que criar pipelines com o Azure Machine Learning?

Use o [SDK do Azure Machine Learning para Python](#the-python-sdk-for-pipelines) para criar pipelines de ML, bem como enviar e acompanhar execuções de pipeline individuais.

Com pipelines, você pode otimizar o fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao criar pipelines com o Azure Machine Learning, você pode se concentrar em suas competências, o aprendizado de máquina, em vez de na infraestrutura.

O uso de etapas distintas possibilita executar novamente apenas as etapas necessárias, conforme você ajusta e testa seu fluxo de trabalho. Uma etapa é uma unidade computacional no pipeline. Conforme mostrado no diagrama anterior, a tarefa de preparação de dados pode envolver muitas etapas. Essas etapas incluem, mas não estão limitadas a normalização, transformação, validação e personalização. As fontes de dados e os dados intermediários são reutilizados no pipeline, o que economiza tempo e recursos de computação. 

Depois que o pipeline é criado, muitas vezes, há mais ajustes finos no loop de treinamento do pipeline. Quando você executa novamente um pipeline, a execução vai pula para as etapas que precisa executar novamente, como um script de treinamento atualizado, e ignora o que não mudou. O mesmo paradigma se aplica a scripts inalterados usados para a execução da etapa. 

Com o Azure Machine Learning, você pode usar vários kits de ferramentas e estruturas, como o PyTorch ou o TensorFlow, para cada etapa em seu pipeline. O Azure é coordenado entre os vários [destinos de computação](concept-azure-machine-learning-architecture.md) usados, de modo que seus dados intermediários possam ser compartilhados com facilidade com os destinos de computação downstream. 

Você pode [acompanhar as métricas para seus testes de pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) diretamente no portal do Azure. 

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens da criação de pipelines para seus fluxos de trabalho de aprendizado de máquina são:

|Principal vantagem|DESCRIÇÃO|
|:-------:|-----------|
|**Execuções&nbsp;autônomas**|Agende algumas etapas para executar em paralelo ou em sequência de maneira confiável e autônoma. Como a preparação e a modelagem de dados podem durar dias ou semanas, agora você pode se concentrar em outras tarefas enquanto seu pipeline está em execução. |
|**Computação diversificada e mista**|Use vários pipelines coordenados de modo confiável entre armazenamentos e computações heterogêneos e escalonáveis. Você pode executar etapas individuais do pipeline em diferentes destinos de computação, como HDInsight, VMs de Ciência de Dados de GPU e Databricks. Isso proporciona um uso eficiente das opções de computação disponíveis.|
|**Capacidade de reutilização**|Você pode modelar os pipelines para cenários específicos, como novo treinamento e pontuação em lote. Dispare-os em sistemas externos por meio de chamadas REST simples.|
|**Acompanhamento e controle de versão**|Em vez de acompanhar manualmente os caminhos de dados e resultados durante a iteração, use o SDK de pipelines para fornecer explicitamente o nome e a versão de fontes de dados, entradas e saídas. Você também pode gerenciar os scripts e os dados separadamente para aumentar a produtividade.|

## <a name="the-python-sdk-for-pipelines"></a>O SDK do Python para pipelines

Use o Python para criar os pipelines de ML. O SDK do Azure Machine Learning oferece constructos obrigatórios para o sequenciamento e a paralelização de etapas em seus pipelines quando nenhuma dependência de dados está presente. Interaja com ele em Jupyter notebooks ou em outro ambiente de desenvolvimento integrado preferencial. 

Usando dependências de dados declarativas, você pode otimizar suas tarefas. O SDK inclui uma estrutura de módulos predefinidos para tarefas comuns, como transferência de dados e publicação do modelo. Estenda a estrutura para modelar suas próprias convenções, implementando etapas personalizadas reutilizáveis nos pipelines. Gerencie também os destinos de computação e os recursos de armazenamento diretamente no SDK.

Salve os pipelines como modelos e implante-os em um ponto de extremidade REST, de modo que você possa agendar trabalhos de novo treinamento ou pontuação em lote.

Para ver como criar seus próprios pipelines, confira a [documentação de referência de pipelines do SDK do Python](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) e o notebook na próxima seção.

## <a name="example-notebooks"></a>Blocos de anotações de exemplo
 
Os seguintes notebooks demonstram pipelines com o Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md).
