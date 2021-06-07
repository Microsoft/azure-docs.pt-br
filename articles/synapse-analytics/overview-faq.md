---
title: Perguntas frequentes – Azure Synapse Analytics
description: Perguntas frequentes sobre o Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 9a0fb8ed8ac54fa866b6db7d8f808c011c0c6758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695814"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Synapse Analytics

Neste guia, você verá as perguntas mais frequentes sobre o Azure Synapse Analytics.

## <a name="general"></a>Geral

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>P: Como posso usar funções RBAC para proteger meu workspace?

A: O Azure Synapse introduz várias funções e escopos que podem ser atribuídos que simplificarão a proteção do seu workspace.

Funções RBAC do Azure Synapse:
* Administrador do Azure Synapse
* Administrador do SQL do Synapse
* Administrador do Spark para Azure Synapse
* Colaborador do Azure Synapse (versão prévia)
* Editor de Artefatos do Azure Synapse (versão prévia)
* Usuário de Artefato do Azure Synapse (versão prévia)
* Operador de Computação do Azure Synapse (versão prévia)
* Usuário de Credencial do Azure Synapse (versão prévia)

Para proteger o workspace do Azure Synapse, atribua as Funções RBAC a estes escopos RBAC:
* Workspaces
* Pools do Spark
* Runtimes de integração
* Serviços vinculados
* Credenciais

Além disso, com os pools de SQL dedicados, você tem todos os recursos de segurança que você já conhece e adora.

### <a name="q-how-do-i-control-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>P: como controlar os pools de SQL dedicados, pools de SQL sem servidor e pools do Spark sem servidor?

A: Como ponto de partida, o Azure Synapse trabalha com análise de custo e alertas de custo internos disponíveis no nível de assinatura do Azure.

- Pools de SQL dedicados – Você tem visibilidade direta do custo e controle sobre o custo, pois cria e especifica os tamanhos de pools de SQL dedicados. Você pode controlar ainda mais quais usuários podem criar ou escalar pools de SQL dedicados com as funções RBAC do Azure.

- Pools de SQL sem servidor – Você tem controles de monitoramento e de gerenciamento de custos que permitem que você limite os gastos em um nível diário, semanal e mensal. Confira [Gerenciamento de custos para o pool de SQL sem servidor](./sql/data-processed.md) para obter mais informações. 

- Pools do Spark sem servidor – Você pode restringir quem pode criar pools do Spark com as funções RBAC do Azure Synapse.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>P: O workspace do Azure Synapse dará suporte à organização de objetos e à granularidade em GA?

A: Os workspaces do Azure Synapse dão suporte a pastas definidas pelo usuário.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>P: Posso vincular mais de um workspace do Power BI a um workspace do Azure Synapse?
    
A: No momento, você só pode vincular um workspace do Power BI a um Workspace do Azure Synapse. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>P: O Link do Synapse para o Cosmos DB está em GA?

A: O Link do Synapse para Apache Spark está em GA. O Link do Synapse para o pool de SQL sem servidor está em Versão Prévia Pública.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>P: O workspace do Azure Synapse dá suporte ao CI/CD? 

A: Sim. Todos os artefatos de Pipeline, notebooks, scripts SQL e definições de trabalho do Spark residirão no Git. Todas as definições de pool serão armazenadas no Git como modelos do ARM. Os objetos do pool de SQL dedicados (esquemas, tabelas, exibições etc.) serão gerenciados com os projetos de banco de dados com o suporte ao CI/CD.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>P: Como fazer para garantir que eu saiba qual credencial está sendo usada para executar um pipeline? 

A: Cada atividade em um Pipeline do Azure Synapse é executada usando as credenciais especificadas no serviço vinculado.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>P: Os SSIS IRs têm suporte na integração do Azure Synapse?

 R: Não no momento. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>P: Como fazer para migrar pipelines existentes do Azure Data Factory para um workspace do Azure Synapse?

A: Neste momento, você precisa recriar manualmente seus pipelines do Azure Data Factory e artefatos relacionados exportando o JSON do pipeline original e importando-o para o workspace do Azure Synapse.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>P: Qual é a diferença entre o Apache Spark para Azure Synapse e o Apache Spark?

A: O Apache Spark para Azure Synapse é o Apache Spark com um suporte adicional para integrações com outros serviços (AAD, AzureML etc.) e bibliotecas adicionais (mssparktuils e Hummingbird), além de configurações de desempenho predefinidas.

Qualquer carga de trabalho atualmente em execução no Apache Spark será executada no Apache Spark para o Azure Synapse sem alteração. 

### <a name="q-what-versions-of-spark-are-available"></a>P: Quais versões do Spark estão disponíveis?

A: O Apache Spark para Azure Synapse dá suporte completo ao Spark 2.4. Para obter uma lista completa dos componentes principais e da versão atualmente compatível, confira [Suporte à versão do Apache Spark](./spark/apache-spark-version-support.md).

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>P: Há um equivalente ao DButils no Spark para Azure Synapse?

A: Sim, o Apache Spark para Azure Synapse fornece a biblioteca **mssparkutils**. Para obter a documentação completa do utilitário, confira [Introdução aos utilitários do Microsoft Spark](./spark/microsoft-spark-utilities.md).

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>P: Como fazer para definir parâmetros de sessão no Apache Spark?

A: Para definir parâmetros de sessão, use o comando magic %%configure disponível. Uma reinicialização de sessão é necessária para que os parâmetros entrem em vigor. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>P: Como fazer para definir parâmetros de nível de cluster em um Pool do Spark sem servidor?

A: Para definir os parâmetros de nível de cluster, você pode fornecer um arquivo spark.conf para o Pool do Spark. Esse pool obedecerá aos parâmetros passados no arquivo de configuração. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>P: Posso executar um Cluster do Spark de vários usuários no Azure Synapse Analytics?
 
A: O Azure Synapse fornece mecanismos desenvolvidos especificamente para casos de uso específicos. O Apache Spark para Azure Synapse é projetado como um serviço de trabalho e não como um modelo de cluster. Há dois cenários em que as pessoas solicitam um modelo de cluster de vários usuários.

**Cenário #1: Muitos usuários acessam um cluster para manutenção de dados para fins de BI.**

A maneira mais fácil de realizar essa tarefa é processar os dados com o Spark e aproveitar as funcionalidades do serviço do SQL do Synapse para que conectar o Power BI a esses conjuntos de dados.

**Cenário 2: Ter vários desenvolvedores em um cluster para economizar dinheiro.**
 
Para atender a esse cenário, você deve dar a cada desenvolvedor um Pool do Spark sem servidor definido para usar um pequeno número de recursos do Spark. Como os pools do Spark sem servidor são gratuitos até que eles sejam ativamente usados, eles minimizam o custo quando há vários desenvolvedores. Os pools compartilham metadados (tabelas do Spark) para que eles possam trabalhar facilmente entre si.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>P: Como fazer para incluir, gerenciar e instalar bibliotecas?

A:  Você pode instalar pacotes externos por meio de um arquivo requirements.txt ao criar o Pool do Spark, no workspace do Azure Synapse ou no portal do Azure. Confira [Gerenciar bibliotecas do Apache Spark no Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Pools de SQL dedicados

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>P: Quais são as diferenças funcionais entre pools de SQL dedicados e pools sem servidor?

A: Você pode encontrar uma lista completa das diferenças em [Diferenças do recurso T-SQL no SQL do Synapse](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>P: Agora que o Azure Synapse está em GA, como fazer para mover meus pools de SQL dedicados que anteriormente eram autônomos para o Azure Synapse? 

A: Não é possível "mover" nem "migrar". Você pode optar por habilitar novos recursos do workspace nos seus pools existentes. Se você fizer isso, não haverá alterações da falha, em vez disso, você poderá usar novos recursos como o Synapse Studio, o Spark e os pools de SQL sem servidor.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>P: Qual é a implantação padrão de pools de SQL dedicados agora? 

A: Por Padrão, todos os novos pools de SQL dedicados serão implantados em um workspace; no entanto, se você precisar, ainda poderá criar um pool de SQL dedicado (anteriormente conhecido como SQL DW) em um fator forma autônomo. 

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar um workspace](quickstart-create-workspace.md)
* [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)
