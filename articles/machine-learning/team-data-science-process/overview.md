---
title: "Visão geral do Processo de Ciência de Dados de Equipe do Azure | Microsoft Docs"
description: "Fornece uma metodologia de ciência de dados com o objetivo de fornecer soluções de análise preditiva e aplicativos inteligentes."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: d92c8df7ad8cf522079bdea86e7b33d8441452ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-overview"></a>Visão geral do Processo de Ciência de dados de Equipe

O TDSP (Processo de Ciência de Dados de Equipe) é uma metodologia de ciência de dados ágil e iterativa que fornece com eficiência soluções de análise preditiva e de aplicativos inteligentes. O TDSP ajuda a melhorar a colaboração e o aprendizado em equipe. Ele contém as principais práticas recomendadas e estruturas da Microsoft, e de outras empresas do setor, que facilitam a implementação bem-sucedida de iniciativas de ciência de dados. O objetivo é ajudar as empresas a obter todos os benefícios do programa de análise escolhido.

Este artigo fornece uma visão geral do TDSP e de seus principais componentes. Fornecemos uma descrição genérica do processo que pode ser implementada com várias ferramentas. Confira os links para outros tópicos com descrições mais detalhadas das tarefas e funções do projeto envolvidas no ciclo de vida do processo. Também fornecemos uma orientação sobre como implementar o TDSP usando um conjunto específico de ferramentas da Microsoft, e a infraestrutura que usamos para implementar o TDSP em nossas equipes.

## <a name="key-components-of-the-tdsp"></a>Principais componentes do TDSP

O TDSP inclui os seguintes componentes principais:

- Uma definição de **ciclo de vida de ciência de dados**
- Uma **estrutura de projeto padronizada**
- **Infraestrutura e recursos** para projetos de ciência de dados
- **Ferramentas e utilitários** para execução do projeto


## <a name="data-science-lifecycle"></a>Ciclo de vida de ciência de dados

O TDSP (Processo de Ciência de Dados de Equipe) fornece um ciclo de vida para estruturar o desenvolvimento de seus projetos de ciência de dados. O ciclo de vida descreve as etapas, do início ao fim, que os projetos normalmente seguem quando são executados.

Se você estiver usando outro ciclo de vida de ciência de dados, como [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou o próprio processo personalizado de sua organização, você ainda poderá usar o TDSP baseado em tarefas no contexto desses ciclos de vida de desenvolvimento. Em um alto nível, essas metodologias diferentes têm muito em comum. 

Esse ciclo de vida foi projetado para projetos de ciência de dados que devem ser fornecidos como parte de aplicativos inteligentes. Esses aplicativos implantam modelos de machine learning ou de inteligência artificial para análise preditiva. Os projetos de ciência de dados exploratórios ou os projetos de análise ad hoc também podem se beneficiar do uso desse processo. Mas, nesses casos, talvez algumas etapas descritas não sejam necessárias.    

O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

* **Noções básicas sobre negócios**
* **Aquisição de dados e reconhecimento**
* **Modelagem**
* **Implantação**
* **Aceitação do cliente**

Esta é uma representação visual do **ciclo de vida do Processo de Ciência de Dados de Equipe**. 

![TDSP-Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

As metas, tarefas e artefatos de documentação de cada estágio do ciclo de vida no TDSP estão descritos no tópico [Ciclo de vida do Processo de Ciência de Dados de Equipe](lifecycle.md). Essas tarefas e artefatos estão associados a funções do projeto:

- Arquitetura da solução
- Gerenciamento de projetos
- Cientista de dados
- Líder de projeto 

O diagrama a seguir fornece um modo de exibição de grade das tarefas (em azul), e os artefatos (em verde) associados a cada estágio do ciclo de vida (no eixo horizontal) para essas funções (no eixo vertical). 

![Funções e tarefas de TDSP](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Estrutura de projeto padronizada

Ter todos os projetos compartilhando uma estrutura de diretórios, e usar modelos para documentos do projeto, facilita muito para os membros da equipe a localização de informações sobre seus projetos. Todos os documentos e código são armazenados em um VCS (Sistema de controle de versão) como Git, TFS ou Subversion para habilitar a colaboração em equipe. O acompanhamento de tarefas e recursos em um sistema de acompanhamento de projetos ágil, como Jira, Rally ou Visual Studio Team Services, permite que um controle mais preciso do código com relação a recursos individuais. Esse controle também permite que as equipes obtenham as melhores estimativas de custo. O TDSP recomenda a criação de um repositório separado para cada projeto no VCS para controle de versão, segurança das informações e colaboração. A estrutura padronizada para todos os projetos ajuda a criar conhecimento institucional por toda a organização.

Fornecemos modelos para a estrutura de pastas e documentos obrigatórios em locais padrão. Essa estrutura de pastas organiza os arquivos que contêm código para exploração de dados e extração de recurso, e que registram iterações do modelo. Esses modelos facilitam para os membros da equipe a compreensão do trabalho feito por outras pessoas e a adição de novos membros às equipes. É fácil exibir e atualizar modelos de documento no formato markdown. Use modelos para fornecer listas de verificação com perguntas fundamentais para cada projeto a fim de garantir que o problema esteja bem definido e que os resultados atendam à qualidade esperada. Os exemplos incluem:

- um estatuto do projeto para documentar o problema comercial e o escopo do projeto
- relatórios de dados para documentar a estrutura e as estatísticas dos dados brutos
- modelar os relatórios documentar os recursos derivados
- modelar métricas de desempenho como curvas de ROC ou MSE


![Diretórios de TDSP](./media/overview/tdsp-dir-structure.png)

A estrutura de diretórios pode ser clonada no [Github](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infraestrutura e recursos para projetos de ciência de dados  

O TDSP fornece recomendações para o gerenciamento de infraestrutura compartilhada de análise e armazenamento, como:

- sistemas de arquivos de nuvem para armazenamento de conjuntos de dados, 
- databases
- clusters de big data (Hadoop ou Spark) 
- serviços de machine learning. 

A infraestrutura de análise e armazenamento pode ser local ou na nuvem. É nela que os conjuntos de dados brutos e processados são armazenados. Essa infraestrutura permite a reprodução da análise. Também evita a duplicação, o que pode resultar em inconsistências e custos de infraestrutura desnecessários. As ferramentas são fornecidas para provisionar os recursos compartilhados, acompanhá-los e permitir que cada membro da equipe se conecte a esses recursos com segurança. Também é uma prática recomendada que os membros do projeto criem um ambiente de computação consistente. Assim, membros diferentes da equipe podem replicar e validar experiências.

Confira um exemplo de uma equipe que trabalha em vários projetos e compartilha vários componentes da infraestrutura de análise em nuvem.

![Infraestrutura do TDSP](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Ferramentas e utilitários para execução do projeto

A introdução de processos na maioria das organizações é um desafio. As ferramentas fornecidas para implementar o processo e o ciclo de vida de ciência de dados ajudam a diminuir as barreiras à adoção, e a aumentar sua consistência. O TDSP fornece um conjunto inicial de ferramentas e scripts para impulsionar a adoção do TDSP dentro de uma equipe. Ele também ajuda a automatizar algumas tarefas comuns do ciclo de vida de ciência de dados, como a exploração de dados e a modelagem de linha de base. Há uma estrutura bem definida para as pessoas contribuírem com ferramentas e utilitários compartilhados no repositório de código compartilhado da própria equipe. Esses recursos podem ser aproveitados por outros projetos dentro da equipe ou da organização. O TDSP também planeja habilitar as colaborações de ferramentas e utilitários para toda a comunidade. Os utilitários de TDSP podem ser clonados no [Github](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Próximas etapas

[Processo de Ciência de Dados de Equipe: funções e tarefas](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) descreve as principais funções de equipe e suas tarefas associadas para uma equipe de ciência de dados que padroniza com base nesse processo. 
