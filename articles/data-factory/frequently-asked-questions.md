---
title: 'Azure Data Factory: Perguntas frequentes | Microsoft Docs'
description: Obtenha resposta a perguntas frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345747"
---
# <a name="azure-data-factory-faq"></a>Perguntas frequentes sobre o Azure Data Factory
Este artigo fornece respostas a perguntas frequentes sobre o Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure? 
O Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado que automatiza a movimentação e a transformação dos dados. Assim como uma fábrica que usa equipamentos para transformar matérias-primas em produtos, o Azure Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato. 

Usando o Azure Data Factory, você pode criar fluxos de trabalho conduzidos por dados para mover dados entre armazenamentos de dados locais e na nuvem. E você pode processar e transformar dados usando serviços de computação como Azure HDInsight, Azure Data Lake Analytics e o tempo de execução de integração do SQL Server Integration Services (SSIS). 

Com o Data Factory, você pode executar o processamento de dados em um serviço de nuvem baseado no Azure ou em seu próprio ambiente de computação auto-hospedado, como SSIS, SQL Server ou Oracle. Depois de criar um pipeline que executa a ação que você precisa, você pode programá-lo para ser executado periodicamente (por hora, diária ou semanal, por exemplo), janela de tempo de agendamento ou disparar o pipeline em uma ocorrência de evento. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Fluxos de controle e escala 
Para dar suporte à padrões e fluxos de integração diversos no moderno data warehouse, permite que o Data Factory pipeline modelagem de dados de flexíveis. Isso envolve o fluxo de controle total paradigmas de programação, que incluem a execução condicional, a ramificação em pipelines de dados e a capacidade de transmitir explicitamente parâmetros dentro e entre esses fluxos. Fluxo de controle também abrange a transformação de dados por meio de expedição de atividade para mecanismos de execução externos e recursos de fluxo de dados, incluindo a movimentação de dados em grande escala, por meio da atividade de cópia.

O Data Factory fornece liberdade para modelar qualquer estilo de fluxo que seja necessário para integração de dados e que possa ser enviado sob demanda ou repetidamente em um agendamento. Alguns fluxos comuns que esse modelo permite são:   

- Fluxos de controle:
    - As atividades podem ser encadeadas juntos em uma sequência em um pipeline.
    - Atividades podem ser transformadas em um pipeline.
    - Parâmetros:
        - Parâmetros podem ser definidos no nível do pipeline e argumentos podem ser passados enquanto você invoca o pipeline sob demanda ou de um gatilho.
        - As atividades podem consumir os argumentos passados para o pipeline.
    - Passagem de estado personalizada:
        - Saídas de atividade, incluindo o estado, podem ser consumidas por uma atividade subsequente no pipeline.
    - Contêineres de looping:
        - A atividade foreach irá iterar em uma coleção especificada de atividades em um loop. 
- Fluxos baseados em gatilho:
    - Os pipelines podem ser acionados sob demanda ou por hora.
- Fluxos delta:
    - Parâmetros podem ser usados para definir seu água marca alta para cópia de delta ao mover tabelas de referência ou dimensão de um repositório relacional, localmente ou na nuvem, para carregar os dados no lake. 

Para obter mais informações, confira [Tutorial: Fluxos de controle](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Dados transformados em escala com pipelines sem código
A nova experiência de ferramentas baseada em navegador fornece criação e implantação de pipeline sem código, com uma experiência moderna e interativa baseada na Web.

Para desenvolvedores de visual de dados e engenheiros de dados, o interface do usuário da web de Data Factory é o ambiente de design sem código que você usará para criar pipelines. Ele é totalmente integrado com o Git do Visual Studio Online e fornece integração com o CI/CD e desenvolvimento iterativo com as opções de depuração.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>SDKs de plataforma cruzada avançada para usuários avançados
Data Factory V2 fornece um conjunto avançado de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines usando seu IDE favorito, incluindo:
* SDK do Python
* CLI do PowerShell
* SDK do C#

Os usuários também podem usar as APIs REST documentadas para fazer interface com o Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Desenvolvimento iterativo e depuração usando as ferramentas do visual
Ferramentas do visual Data Factory do Azure permitem o desenvolvimento iterativo e depuração. Você pode criar seus pipelines e faça execuções de teste usando o **depurar** funcionalidade na tela de pipeline sem escrever uma única linha de código. Você pode exibir os resultados de suas execuções de teste na **saída** janela de tela de pipeline. Depois que a execução de teste for bem-sucedida, você pode adiciona mais atividades ao seu pipeline e continuar a depuração de maneira iterativa. Você também pode cancelar suas execuções de teste depois que eles estão em andamento. 

Não é necessário para publicar suas alterações antes de selecionar o serviço data factory **depurar**. Isso é útil em cenários em que você deseja para certificar-se de que as novas adições ou alterações funcionará conforme o esperado antes de atualizar seus fluxos de trabalho de fábrica de dados em ambientes de desenvolvimento, teste ou produção. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Capacidade de implantar pacotes do SSIS no Azure 
Se você quiser mover as cargas de trabalho do SSIS, poderá criar um Data Factory e provisionar um tempo de execução de integração do Azure-SSIS. Um tempo de execução de integração do Azure-SSIS é um cluster totalmente gerenciado das VMs do Azure (nós) dedicados para executar seus pacotes SSIS na nuvem. Para obter instruções passo a passo, consulte o tutorial [Implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). 
 
### <a name="sdks"></a>SDKs
Se você for um usuário avançado e estiver procurando por uma interface de programação, o Data Factory fornece um conjunto avançado de SDKs que você pode usar para criar, gerenciar ou monitorar pipelines usando seu IDE favorito. O suporte à linguagem inclui .NET, PowerShell, Python e REST.

### <a name="monitoring"></a>Monitoramento
É possível monitorar os Data Factories por meio do PowerShell, SDK ou das Ferramentas de Monitoramento Visual na interface do usuário do navegador. Você pode monitorar e gerenciar fluxos personalizados sob demanda, com base no gatilho e controlado por relógio de forma eficiente e efetiva. Cancelar as tarefas existentes, consulte falhas em um relance, fazer uma busca detalhada para obter mensagens de erro detalhadas e depurar os problemas, tudo a partir de um único painel sem contexto alternância de ou para trás e navegar entre telas. 

### <a name="new-features-for-ssis-in-data-factory"></a>Novos recursos do SSIS no Data Factory
Uma vez que a versão no 2017 de visualização do público inicial, o Data Factory adicionou os seguintes recursos para SSIS:

-   O suporte para três mais configurações/variantes de banco de dados SQL Azure para hospedar o banco de dados do SSIS (SSISDB) de pacotes/projetos:
-   Banco de dados SQL com pontos de extremidade de serviço de rede virtual
-   Instância gerenciada
-   Pool elástico
-   Suporte para uma rede virtual do Azure Resource Manager na parte superior de uma rede virtual clássica preterido no futuro, que lhe permite injetar/junção seu tempo de execução de integração do Azure-SSIS a uma rede virtual configurado para o banco de dados SQL com o serviço de rede virtual acesso a dados de pontos de extremidade/MI/local. Para obter mais informações, consulte também [unir um tempo de execução de integração do Azure-SSIS a uma rede virtual](join-azure-ssis-integration-runtime-virtual-network.md).
-   Suporte para autenticação do Active Directory do Azure (AD Azure) e autenticação do SQL para se conectar ao SSISDB, permitindo que a autenticação do AD do Azure com sua identidade gerenciada do Data Factory para recursos do Azure
-   Suporte para trazer sua própria licença do SQL Server local para ganhar uma economia substancial da opção de benefício híbrido do Azure
-   Suporte para a edição Enterprise do Azure-SSIS integration runtime que lhe permite usar os recursos de advanced/premium, uma interface de instalação personalizada para instalar componentes adicionais/extensões e um ecossistema de parceiros. Para obter mais informações, consulte também [Enterprise Edition, a instalação personalizada e 3ª extensibilidade de terceiros para SSIS no ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Integração mais profunda do SSIS no Data Factory que permite invocar/gatilho atividades de primeira classe executar pacote do SSIS em pipelines do Data Factory e agendá-los por meio do SSMS. Para obter mais informações, consulte também [Modernize e estender seus fluxos de trabalho ETL/ELT com atividades SSIS em pipelines ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>O que é o tempo de execução de integração?
O tempo de execução de integração é a infraestrutura de computação do Azure Data Factory usa para fornecer as seguintes funcionalidades de integração de dados em vários ambientes de rede:

- **Movimentação de dados**: Para a movimentação de dados, o tempo de execução de integração move os dados entre os armazenamentos de dados de origem e destino, enquanto fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e com bom desempenho e transferência de dados escalonável.
- **Expedição de atividades**: Para a transformação, o integration runtime fornece a capacidade de executar pacotes SSIS nativamente.
- **Executar pacotes SSIS**: O tempo de execução de integração executa nativamente pacotes SSIS em um ambiente de computação gerenciada do Azure. O tempo de execução de integração também dá suporte à expedição e ao monitoramento de atividades de transformação executadas em uma variedade de serviços de computação, como Azure HDInsight, Machine Learning do Azure, banco de dados SQL e SQL Server.

Você pode implantar uma ou mais instâncias do integration runtime conforme necessário para movimentar e transformar dados. O integration runtime pode executar em uma rede pública do Azure ou em uma rede privada (local, rede Virtual do Azure ou nuvem privada virtual [VPC] do Amazon Web Services). 

Para obter mais informações, consulte [Tempo de execução de integração no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite do número de tempos de execução de integração?
Não há nenhum limite rígido quanto ao número de instâncias de tempo de execução de integração que você pode ter em um data factory. Entretanto, há um limite de número de núcleos de VM que o tempo de execução de integração pode usar por assinatura para a execução de pacotes do SSIS. Para saber mais, confira [Limites do Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Quais são os conceitos de nível superior do Azure Data Factory?
Uma assinatura do Azure pode ter uma ou mais instâncias (ou data factories) do Azure Data Factory. O Azure Data Factory contém quatro componentes principais que trabalham juntos como uma plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para movimentação e transformação dos dados.

### <a name="pipelines"></a>Pipelines
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que ingerem dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados. O benefício é que você pode usar um pipeline para gerenciar as atividades como um conjunto, em vez de gerenciar cada atividade individualmente. Você pode encadear as atividades em um pipeline para operá-las em sequência ou pode operá-las de modo independente, em paralelo.

### <a name="activities"></a>Atividades
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro armazenamento de dados. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle.

### <a name="datasets"></a>Conjunto de dados
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam para ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense desta forma: Um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão para conectar-se à conta de Armazenamento do Azure. E um conjunto de dados de BLOBs do Azure Especifica o contêiner de blob e a pasta que contém os dados.

Serviços vinculados têm duas finalidades no Data Factory:

- Para representar um *armazenamento de dados* que inclui, mas não é limitado a, uma instância do SQL Server local, uma instância de banco de dados Oracle, um compartilhamento de arquivos ou uma conta de armazenamento de Blobs do Azure. Para obter uma lista de repositórios de dados com suporte, consulte [Atividade de cópia no Azure Data Factory](copy-activity-overview.md).
- Para representar um *recurso de computação* que pode hospedar a execução de uma atividade. Por exemplo, a atividade do Hive do HDInsight é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, consulte [Transformar dados no Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Gatilhos
Gatilhos representam as unidades de processamento que determinam quando a execução de um pipeline é iniciada. Existem diferentes tipos de gatilhos para diferentes tipos de eventos. 

### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da uma execução do pipeline. Normalmente, você instancia uma execução do pipeline passando argumentos para os parâmetros definidos no pipeline. Você pode passar os argumentos manualmente ou dentro da definição do gatilho.

### <a name="parameters"></a>parâmetros
Os parâmetros são pares chave-valor em uma configuração somente leitura. Você define os parâmetros em um pipeline e passa os argumentos para os parâmetros definidos durante a execução de um contexto de execução. O contexto de execução é criado por um gatilho ou de um pipeline que você executa manualmente. As atividades no pipeline consomem os valores de parâmetro.

Um conjunto de dados é um parâmetro fortemente tipado e uma entidade que você pode reutilizar ou consultar. Uma atividade pode referenciar conjuntos de dados, e ele pode consumir as propriedades que são definidas na definição do conjunto de dados.

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. Também é uma entidade que você pode reutilizar ou consultar.

### <a name="control-flows"></a>Fluxos de controle
Fluxos de controle coordenam as atividades de pipeline, incluindo encadeamento de atividades em uma sequência, ramificação, parâmetros que você define no nível do pipeline e argumentos passados conforme você invoca o pipeline sob demanda ou de um gatilho. Fluxos de controle também incluem o estado personalizado passando e contêineres de looping (ou seja, iteradores de foreach).


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços Data Factory?
Para os detalhes de preço do Azure Data Factory, consulte [Detalhes de preço do Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso me manter informado sobre o Data Factory?
Para obter as informações mais atualizadas sobre o Azure Data Factory, vá para os seguintes sites:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page da documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Análise técnica aprofundada 

### <a name="how-can-i-schedule-a-pipeline"></a>Como fazer para agendar um pipeline? 
Você pode usar o gatilho do agendador ou gatilho de janela de tempo para agendar um pipeline. O gatilho usa uma agenda de calendário de relógio, que pode agendar pipelines periodicamente ou padrões recorrentes baseados no calendário, por exemplo, em (segundas-feiras às 6:00) e quintas-feiras às 9:00. Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Posso passar parâmetros para uma execução de pipeline?
Sim, os parâmetros são um conceito de primeira classe de nível superior no Data Factory. Você pode definir parâmetros no nível do pipeline e passar argumentos durante a execução do pipeline sob demanda ou usando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>É possível definir valores padrão para os parâmetros do pipeline? 
Sim. Você pode definir valores padrão para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Uma atividade em um pipeline pode consumir argumentos passados para uma execução de pipeline? 
Sim. Cada atividade no pipeline pode consumir o valor do parâmetro passado para o pipeline e ser executada com o constructo `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída de uma atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida em uma atividade subsequente com o constructo `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como tratar normalmente o valores nulos em uma saída de atividade? 
Você pode usar o constructo `@coalesce` nas expressões para manipular valores nulos normalmente. 

## <a name="mapping-data-flows"></a>Fluxos de dados de mapeamento

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Qual versão do Data Factory usar para criar fluxos de dados?
Use a versão do Data Factory V2 para criar fluxos de dados.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Eu era um cliente de visualização privada anterior que usaram fluxos de dados, e eu usei a versão de visualização do Data Factory V2 para fluxos de dados.
Esta versão agora é obsoleta. Use o Data Factory V2 para fluxos de dados.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>O que mudou na versão prévia privada para visualização pública limitada em relação ao fluxos de dados?
Você não terá mais trazer seus próprios clusters do Azure Databricks. Data Factory irá gerenciar a criação do cluster e a desmontagem. Conjuntos de dados de blob e conjuntos de dados do armazenamento do Azure Data Lake Gen2 são separados em texto delimitado e conjuntos de dados Apache Parquet. Ainda, você pode usar o armazenamento de BLOBs e Data Lake armazenamento Gen2 para armazenar esses arquivos. Use o serviço vinculado apropriado para os mecanismos de armazenamento.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Posso migrar meu fábricas de visualização privada ao Data Factory V2?

Sim. [Siga as instruções](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Preciso de ajuda para solucionar minha lógica de fluxo de dados. Quais informações precisa fornecer para obter ajuda?

Quando a Microsoft fornece ajuda ou solução de problemas com os fluxos de dados, forneça o plano de código DSL. Para fazer isso, siga estas etapas:

1. No Designer de fluxo de dados, selecione **código** no canto superior direito. Isso exibirá o código JSON editável para o fluxo de dados.
2. Na exibição de código, selecione **planejar** no canto superior direito. Essa alternância será alternado de JSON para somente leitura formatada DSL script plano.
3. Copiar e colar esse script ou salvá-lo em um arquivo de texto.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Como acessar dados usando os outros 80 tipos do conjunto de dados no Data Factory?

O recurso de mapeamento de fluxo de dados atualmente permite que Azure SQL Database, Azure SQL Data Warehouse, delimitados por arquivos de texto do armazenamento de BLOBs do Azure ou Azure Data Lake armazenamento Gen2 e arquivos do Parquet do armazenamento de BLOBs ou Data Lake armazenamento Gen2 nativamente para origem e coletor. 

Use a atividade de cópia para transferir dados de qualquer um dos outros conectores e, em seguida, executar uma atividade de fluxo de dados para transformar os dados depois que ele foi preparado. Por exemplo, seu pipeline primeiro copiará no armazenamento de BLOBs e, em seguida, uma atividade de fluxo de dados será usar um conjunto de dados na fonte para transformar os dados.

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um data factory, consulte os tutoriais a seguir:

- [Início Rápido: Criar um data factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Copiar dados na nuvem](tutorial-copy-data-dot-net.md)
