---
title: Executar tarefas de ciência de dados – Processo de Ciência de Dados da Equipe
description: Como um cientista de dados pode executar um projeto de ciência de dados de modo rastreável, com controle de versão e colaborativo.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100371950"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Executar tarefas de ciência de dados: exploração, modelagem e implantação

Em geral, entre as tarefas de ciência de dados, temos: exploração, modelagem e implantação. Este artigo descreve as tarefas que devem realizadas para concluir várias tarefas de ciência de dados comuns como exploração interativa de dados, análise de dados, relatórios e criação de modelo. As opções para implantar um modelo em um ambiente de produção podem incluir:

- [Azure Machine Learning](../index.yml)
- [SQL-Server com serviços de ML](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Exploração 

Um cientista de dados pode realizar exploração e geração de relatórios de várias maneiras: usando bibliotecas e pacotes disponíveis para Python (matplotlib, por exemplo) ou com R (ggplot ou malha, por exemplo). Os cientistas de dados podem personalizar esse código para atender às necessidades de exploração de dados para cenários específicos. As necessidades de lidar com dados estruturados são diferentes daquelas de dados não estruturados, como texto ou imagens. 

Produtos como o Azure Machine Learning também fornecem uma [preparação avançada de dados](../how-to-create-register-datasets.md) para estruturação e exploração de dados, incluindo a criação de recursos. O usuário deve decidir quais ferramentas, bibliotecas e pacotes se ajustam melhor às suas necessidades. 

A entrega ao final dessa fase é um relatório de exploração de dados. O relatório deve fornecer uma exibição bastante abrangente dos dados a serem usados para modelagem e uma avaliação de se os dados são adequados para prosseguir para a etapa de modelagem. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelagem

Há vários kits de ferramentas e pacotes para treinar modelos em diversas linguagens. Os cientistas de dados devem ficar à vontade para usar aqueles com os quais estão acostumados, desde que as considerações de desempenho em relação à precisão e latência sejam atendidas para os casos de uso nos negócios e cenários de produção relevantes.

### <a name="model-management"></a>Gerenciamento de modelos
Depois de vários modelos terem sido compilados, geralmente é necessário ter um sistema para registrar e gerenciar os modelos. Geralmente, é necessária uma combinação de scripts ou APIs e um sistema de controle de versão ou de banco de dados de back-end. Algumas opções que podem ser levadas em consideração para essas tarefas de gerenciamento são:

1. [Azure Machine Learning – serviço de gerenciamento de modelos](../index.yml)
2. [ModelDB do MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL-Server como um sistema de gerenciamento de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Implantação

A implantação de produção permite que um modelo execute uma função ativa em uma empresa. Previsões de um modelo implantado podem ser usadas para decisões de negócios.

### <a name="production-platforms"></a>Plataformas de produção
Há várias abordagens e plataformas para colocar modelos em produção. Veja algumas opções:


- [Implantação de modelo no Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implantação de um modelo no SQL-server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Antes da implantação, é preciso garantir que a latência da pontuação de modelo é baixa o suficiente para usar em produção.
>
>

Outros exemplos estão disponíveis em instruções passo a passo completas que demonstram todas as etapas do processo para **cenários específicos**. Eles estão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.

> [!NOTE]
> Para implantações que usam o Azure Machine Learning Studio, consulte [Implantar um serviço Web do Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Testes de A/B
Quando vários modelos estão em produção, pode ser útil executar [Testes de A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Próximas etapas

[Acompanhar o progresso de projetos de ciência de dados](track-progress.md) mostra como um cientista de dados pode acompanhar o progresso de um projeto de ciência de dados.

A [Operação de modelo e CI/CD](ci-cd-flask.md) mostra como a CI/CD pode ser executada com modelos desenvolvidos.
