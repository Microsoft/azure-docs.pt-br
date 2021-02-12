---
title: O que é o Processo de Ciência de Dados de Equipe?
description: Fornece uma metodologia de ciência de dados com o objetivo de fornecer soluções de análise preditiva e aplicativos inteligentes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a341d33c957af8fe7423bda80f1d34a7f59cdd6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737992"
---
# <a name="what-is-the-team-data-science-process"></a>O que é o Processo de Ciência de Dados de Equipe?

O TDSP (Processo de Ciência de Dados de Equipe) é uma metodologia de ciência de dados ágil e iterativa que fornece com eficiência soluções de análise preditiva e de aplicativos inteligentes. O TDSP ajuda a aprimorar a colaboração e o aprendizado da equipe sugerindo como as funções de equipe funcionam melhor em conjunto. O TDSP inclui melhores práticas e estruturas da Microsoft e outros líderes do setor para ajudar na implementação bem-sucedida de iniciativas de ciência de dados. O objetivo é ajudar as empresas a obter todos os benefícios do programa de análise escolhido.

Este artigo fornece uma visão geral do TDSP e de seus principais componentes. Fornecemos uma descrição genérica do processo que pode ser implementada com diferentes tipos de ferramentas. Confira os links para outros tópicos com descrições mais detalhadas das tarefas e funções do projeto envolvidas no ciclo de vida do processo. Também fornecemos uma orientação sobre como implementar o TDSP usando um conjunto específico de ferramentas da Microsoft, e a infraestrutura que usamos para implementar o TDSP em nossas equipes.

## <a name="key-components-of-the-tdsp"></a>Principais componentes do TDSP

O TDSP tem os seguintes componentes principais:

- Uma definição de **ciclo de vida de ciência de dados**
- Uma **estrutura de projeto padronizada**
- **Infraestrutura e recursos** recomendados para projetos de ciência de dados
- **Ferramentas e utilitários** recomendados para a execução do projeto


## <a name="data-science-lifecycle"></a>Ciclo de vida de ciência de dados

O TDSP (Processo de Ciência de Dados de Equipe) fornece um ciclo de vida para estruturar o desenvolvimento de seus projetos de ciência de dados. O ciclo de vida descreve as etapas completas que projetos bem-sucedidos seguem.

Se você estiver usando outro ciclo de vida de ciência de dados, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou o próprio processo personalizado de sua organização, você ainda poderá usar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. Em um alto nível, essas metodologias diferentes têm muito em comum. 

Esse ciclo de vida foi projetado para projetos de ciência de dados que devem ser fornecidos como parte de aplicativos inteligentes. Esses aplicativos implantam modelos de machine learning ou de inteligência artificial para análise preditiva. Os projetos de ciência de dados exploratórios ou os projetos de análise improvisados também podem se beneficiar do uso desse processo. Mas, nesses casos, talvez algumas etapas descritas não sejam necessárias.    

O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![O diagrama mostra o ciclo de vida da ciência de dados, incluindo noções básicas sobre negócios, aquisição/compreensão dos dados, modelagem e implantação.](./media/overview/tdsp-lifecycle2.png) 

As metas, tarefas e artefatos de documentação de cada estágio do ciclo de vida no TDSP estão descritos no tópico [Ciclo de vida do Processo de Ciência de Dados de Equipe](lifecycle.md). Essas tarefas e artefatos estão associados a funções do projeto:

- Arquitetura da solução
- Gerenciamento de projetos
- Engenheiro de dados
- Cientista de dados
- Desenvolvedor de aplicativos
- Líder de projeto 

O diagrama a seguir fornece um modo de exibição de grade das tarefas (em azul), e os artefatos (em verde) associados a cada estágio do ciclo de vida (no eixo horizontal) para essas funções (no eixo vertical). 

[![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Estrutura de projeto padronizada

Ter todos os projetos compartilhando uma estrutura de diretórios, e usar modelos para documentos do projeto, facilita muito para os membros da equipe a localização de informações sobre seus projetos. Todos os documentos e código são armazenados em um VCS (Sistema de controle de versão) como Git, TFS ou Subversion para habilitar a colaboração em equipe. O acompanhamento de tarefas e recursos em um sistema de acompanhamento de projetos ágil, como Jira, Rally e Azure DevOps, permite um acompanhamento mais preciso do código com relação a recursos individuais. Esse controle também permite que as equipes obtenham as melhores estimativas de custo. O TDSP recomenda a criação de um repositório separado para cada projeto no VCS para controle de versão, segurança das informações e colaboração. A estrutura padronizada para todos os projetos ajuda a criar conhecimento institucional por toda a organização.

Fornecemos modelos para a estrutura de pastas e documentos obrigatórios em locais padrão. Essa estrutura de pastas organiza os arquivos que contêm código para exploração de dados e extração de recurso, e que registram iterações do modelo. Esses modelos facilitam para os membros da equipe a compreensão do trabalho feito por outras pessoas e a adição de novos membros às equipes. É fácil exibir e atualizar modelos de documento no formato markdown. Use modelos para fornecer listas de verificação com perguntas fundamentais para cada projeto a fim de verificar se o problema está bem definido e se os resultados atendem à qualidade esperada. Os exemplos incluem:

- um estatuto do projeto para documentar o problema comercial e o escopo do projeto
- relatórios de dados para documentar a estrutura e as estatísticas dos dados brutos
- modelar os relatórios documentar os recursos derivados
- modelar métricas de desempenho como curvas de ROC ou MSE


[![TDSP-directories](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

A estrutura de diretórios pode ser clonada no [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestrutura e recursos para projetos de ciência de dados  

O TDSP fornece recomendações para o gerenciamento de infraestrutura compartilhada de análise e armazenamento, como:

- sistemas de arquivos de nuvem para armazenar conjuntos de dados 
- bancos de dados
- clusters Big Data (SQL ou Spark) 
- serviço de aprendizado de máquina 

A infraestrutura de análise e armazenamento, em que os conjuntos de dados brutos e processados são armazenados, podem estar na nuvem ou ser locais. Essa infraestrutura permite a reprodução da análise. Também evita a duplicação, o que pode resultar em inconsistências e custos de infraestrutura desnecessários. As ferramentas são fornecidas para provisionar os recursos compartilhados, acompanhá-los e permitir que cada membro da equipe se conecte a esses recursos com segurança. Também é uma boa prática que os membros do projeto criem um ambiente de computação consistente. Assim, membros diferentes da equipe podem replicar e validar experiências.

Confira um exemplo de uma equipe que trabalha em vários projetos e compartilha vários componentes da infraestrutura de análise em nuvem.

[![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Ferramentas e utilitários para execução do projeto

A introdução de processos na maioria das organizações é um desafio. As ferramentas fornecidas para implementar o processo e o ciclo de vida de ciência de dados ajudam a diminuir as barreiras à adoção, e a aumentar sua consistência. O TDSP fornece um conjunto inicial de ferramentas e scripts para impulsionar a adoção do TDSP dentro de uma equipe. Ele também ajuda a automatizar algumas tarefas comuns do ciclo de vida de ciência de dados, como a exploração de dados e a modelagem de linha de base. Há uma estrutura bem definida para as pessoas contribuírem com ferramentas e utilitários compartilhados no repositório de código compartilhado da própria equipe. Esses recursos podem ser aproveitados por outros projetos dentro da equipe ou da organização.  A Microsoft fornece ferramentas abrangentes dentro do [Azure Machine Learning](../index.yml) que dão suporte a software livre (Python, R, ONNX e estruturas comuns de aprendizado profundo) e também às ferramentas próprias da Microsoft (AutoML).


## <a name="next-steps"></a>Próximas etapas

[Processo de Ciência de Dados de Equipe: Funções e tarefas](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Descreve as principais funções de equipe e suas tarefas associadas para uma equipe de ciência de dados que faz a padronização nesse processo. 
