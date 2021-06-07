---
title: Solucionar problemas de desempenho da atividade de cópia
description: Saiba mais sobre como solucionar problemas de desempenho da atividade de cópia no Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/07/2021
ms.openlocfilehash: ce7c97abfb879e9298edac5f38540bbc026274da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584388"
---
# <a name="troubleshoot-copy-activity-performance"></a>Solucionar problemas de desempenho da atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como solucionar problemas de desempenho da atividade de cópia no Azure Data Factory. 

Depois de executar uma atividade de cópia, você pode coletar o resultado da execução e as estatísticas de desempenho no modo de exibição [monitoramento da atividade de cópia](copy-activity-monitoring.md) . A seguir, é mostrado um exemplo.

![Monitorar detalhes da execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Dicas de ajuste de desempenho

Em alguns cenários, ao executar uma atividade de cópia no Data Factory, você verá **"dicas de ajuste de desempenho"** na parte superior, conforme mostrado no exemplo acima. As dicas informam o afunilamento identificado pelo ADF para essa execução de cópia específica, juntamente com a sugestão de como impulsionar a taxa de transferência de cópia. Tente fazer a alteração refeita em comando e execute a cópia novamente.

Como referência, no momento as dicas de ajuste de desempenho fornecem sugestões para os seguintes casos:

| Categoria              | Dicas de ajuste de desempenho                                      |
| --------------------- | ------------------------------------------------------------ |
| Específico do armazenamento de dados   | Carregando dados no **Azure Synapse Analytics**: sugira usar o polybase ou a instrução de cópia se ele não for usado. |
| &nbsp;                | Copiando dados de/para o banco de dado **SQL do Azure**: quando DTU está sob alta utilização, sugira atualizar para uma camada superior. |
| &nbsp;                | Copiando dados de/para **Azure Cosmos DB**: quando ru está sob alta utilização, sugira atualizar para ru maior. |
|                       | Copiando dados da **tabela SAP**: ao copiar uma grande quantidade de dados, sugira aproveitar a opção de partição do conector SAP para habilitar a carga paralela e aumentar o número máximo de partições. |
| &nbsp;                | Ingestão de dados do **Amazon redshift**: sugira o uso de Unload se ele não for usado. |
| Limitação do repositório de dados | Se várias operações de leitura/gravação forem limitadas pelo armazenamento de dados durante a cópia, sugira verificar e aumentar a taxa de solicitação permitida para o armazenamento de dados ou reduzir a carga de trabalho simultânea. |
| Tempo de execução de integração  | Se você usar um **ir (autohospedado Integration Runtime)** e a atividade de cópia aguardar tempo na fila até que o ir tenha o recurso disponível para ser executado, sugira escalar horizontalmente/verticalmente o ir. |
| &nbsp;                | Se você usar um **Azure Integration Runtime** que esteja em uma região não ideal, resultando em leitura/gravação lenta, sugira configurar para usar um ir em outra região. |
| Tolerância a falhas       | Se você configurar a tolerância a falhas e ignorar linhas incompatíveis resultar em desempenho lento, sugira garantir que os dados de origem e de coletor sejam compatíveis. |
| Cópia em etapas           | Se a cópia preparada estiver configurada, mas não for útil para seu par de coletor de origem, sugira removê-la. |
| Retomar                | Quando a atividade de cópia é retomada do último ponto de falha, mas você altera a configuração DIU após a execução original, observe que a nova configuração DIU não tem efeito. |

## <a name="understand-copy-activity-execution-details"></a>Entender os detalhes da execução da atividade de cópia

Os detalhes e as durações de execução na parte inferior da exibição de monitoramento da atividade de cópia descrevem os principais estágios pelos quais sua atividade de cópia passa (consulte o exemplo no início deste artigo), que é especialmente útil para solucionar problemas de desempenho de cópia. O afunilamento de sua execução de cópia é aquele com a duração mais longa. Consulte a tabela a seguir na definição de cada estágio e saiba como [solucionar problemas de atividade de cópia em Azure ir](#troubleshoot-copy-activity-on-azure-ir) e [solucionar problemas de atividade de cópia em ir via hospedagem interna](#troubleshoot-copy-activity-on-self-hosted-ir) com tais informações.

| Estágio           | Descrição                                                  |
| --------------- | ------------------------------------------------------------ |
| Fila           | O tempo decorrido até que a atividade de cópia realmente seja iniciada no Integration Runtime. |
| Script de pré-cópia | O tempo decorrido entre a atividade de cópia a partir do IR e a atividade de cópia concluindo a execução do script de pré-cópia no repositório de dados do coletor. Aplique quando você configurar o script de pré-cópia para coletores de banco de dados, por exemplo, ao gravar dados no banco de dados SQL do Azure, faça uma limpeza antes de copiar novos dados. |
| Transferência        | O tempo decorrido entre o fim da etapa anterior e o IR transferindo todos os dados da origem para o coletor. <br/>Observe que as subetapas em transferência são executadas em paralelo, e algumas operações não são mostradas agora, por exemplo, analisando/gerando o formato de arquivo.<br><br/>- **Tempo até o primeiro byte:** O tempo decorrido entre o fim da etapa anterior e a hora em que o IR recebe o primeiro byte do armazenamento de dados de origem. Aplica-se a fontes não baseadas em arquivo.<br>- **Fonte de listagem:** A quantidade de tempo gasto na enumeração de arquivos de origem ou partições de dados. O último se aplica quando você configura opções de partição para fontes de banco de dados, por exemplo, ao copiar dados de bancos de dados como Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Lendo da origem:** A quantidade de tempo gasto na recuperação de dados do armazenamento de dados de origem.<br/>- **Gravando no coletor:** A quantidade de tempo gasto na gravação de dados no armazenamento de dados do coletor. Observe que alguns conectores não têm essa métrica no momento, incluindo Pesquisa Cognitiva do Azure, Azure Data Explorer, armazenamento de tabelas do Azure, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, nuvem do serviço Salesforce/Salesforce. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Solucionar problemas de atividade de cópia no Azure IR

Siga as [etapas de ajuste de desempenho](copy-activity-performance.md#performance-tuning-steps) para planejar e conduzir o teste de desempenho para seu cenário. 

Quando o desempenho da atividade de cópia não atender à sua expectativa, para solucionar problemas de atividade de cópia única em execução no Azure Integration Runtime, se você vir [dicas de ajuste de desempenho](#performance-tuning-tips) mostradas na exibição monitoramento de cópia, aplique a sugestão e tente novamente. Caso contrário, [entenda os detalhes da execução da atividade de cópia](#understand-copy-activity-execution-details), verifique qual estágio tem a duração mais **longa** e aplique as diretrizes abaixo para melhorar o desempenho da cópia:

- **"Script de pré-cópia" teve duração longa:** significa que o script de pré-cópia em execução no banco de dados do coletor demora muito para ser concluído. Ajuste a lógica do script de pré-cópia especificada para aprimorar o desempenho. Se precisar de ajuda adicional para melhorar o script, entre em contato com sua equipe de banco de dados.

- **"Tempo de transferência até o primeiro byte" com duração de trabalho longa**: isso significa que sua consulta de origem demora muito para retornar qualquer dado. Verifique e otimize a consulta ou o servidor. Se precisar de mais ajuda, entre em contato com sua equipe de armazenamento de dados.

- **"Origem da listagem de transferência" sofreu duração de trabalho longa**: isso significa que a enumeração de arquivos de origem ou partições de dados de banco de dado de origem está lenta.
  - Ao copiar dados da fonte baseada em arquivo, se você usar o **filtro curinga** no caminho da pasta ou no nome do arquivo ( `wildcardFolderPath` ou `wildcardFileName` ), ou usar o **filtro de tempo da última modificação do arquivo** ( `modifiedDatetimeStart` ou `modifiedDatetimeEnd` ), observe que esse filtro resultaria na atividade de cópia listando todos os arquivos na pasta especificada para o lado do cliente e, em seguida, aplicaria o filtro. Tal enumeração de arquivo poderia se tornar o afunilamento, especialmente quando apenas pequenos conjuntos de arquivos atingirem a regra de filtro.

    - Verifique se você pode [copiar arquivos com base no caminho ou nome do arquivo particionado DateTime](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Dessa forma, não traz a responsabilidade de listar a origem.

    - Verifique se você pode usar o filtro nativo do repositório de dados em vez disso, especificamente "**prefixo**" para o Amazon S3/blob do Azure/armazenamento de arquivos do Azure e "**listAfter/listBefore**" para ADLS Gen1. Esses filtros são um filtro do lado do servidor do armazenamento de dados e teriam um desempenho muito melhor.

    - Considere dividir um único conjunto de dados grande em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente, cada um resolve a parte dos dados. Você pode fazer isso com Lookup/GetMetadata + ForEach + copiar. Consulte [copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md) ou [migrar dados do Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md) modelos de solução como exemplo geral.

  - Verifique se o ADF relata qualquer erro de limitação na origem ou se o armazenamento de dados está sob o estado de alta utilização. Nesse caso, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do armazenamento de dados para aumentar o limite de limitação ou o recurso disponível.

  - Use Azure IR na mesma ou próxima de sua região de armazenamento de dados de origem.

- **"Transferência-leitura da origem" duração de trabalho longa experiente**: 

  - Adote a prática recomendada de carregamento de dados específicos do conector se aplicável. Por exemplo, ao copiar dados do [Amazon redshift](connector-amazon-redshift.md), configure o para usar o descarregamento do redshift.

  - Verifique se o ADF relata qualquer erro de limitação na origem ou se o armazenamento de dados está sob alta utilização. Nesse caso, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do armazenamento de dados para aumentar o limite de limitação ou o recurso disponível.

  - Verifique a fonte de cópia e o padrão de coletor: 

    - Se o padrão de cópia der suporte a mais de 4 DIUs (unidades de integração de dados) – consulte [esta seção](copy-activity-performance-features.md#data-integration-units) sobre detalhes, geralmente você pode tentar aumentar o DIUs para obter um melhor desempenho. 

    - Caso contrário, considere dividir um único conjunto de dados grande em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente, cada um resolve a parte dos dados. Você pode fazer isso com Lookup/GetMetadata + ForEach + copiar. Consulte [copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md), [migrar dados do Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md)ou [copiar em massa com](solution-template-bulk-copy-with-control-table.md) modelos de solução de tabela de controle como exemplo geral.

  - Use Azure IR na mesma ou próxima de sua região de armazenamento de dados de origem.

- **"Transferência-gravação no coletor" duração de trabalho longa com experiência**:

  - Adote a prática recomendada de carregamento de dados específicos do conector se aplicável. Por exemplo, ao copiar dados para o [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md), use o polybase ou a instrução de cópia. 

  - Verifique se o ADF relata qualquer erro de limitação no coletor ou se o armazenamento de dados está sob alta utilização. Nesse caso, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do armazenamento de dados para aumentar o limite de limitação ou o recurso disponível.

  - Verifique a fonte de cópia e o padrão de coletor: 

    - Se o padrão de cópia der suporte a mais de 4 DIUs (unidades de integração de dados) – consulte [esta seção](copy-activity-performance-features.md#data-integration-units) sobre detalhes, geralmente você pode tentar aumentar o DIUs para obter um melhor desempenho. 

    - Caso contrário, ajuste gradualmente as [cópias paralelas](copy-activity-performance-features.md), observe que muitas cópias paralelas podem até mesmo prejudicar o desempenho.

  - Use Azure IR na mesma ou próxima de sua região de armazenamento de dados do coletor.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Solucionar problemas de atividade de cópia no IR auto-hospedado

Siga as [etapas de ajuste de desempenho](copy-activity-performance.md#performance-tuning-steps) para planejar e conduzir o teste de desempenho para seu cenário. 

Quando o desempenho da cópia não atender à sua expectativa, para solucionar problemas de atividade de cópia única em execução no Azure Integration Runtime, se você vir [dicas de ajuste de desempenho](#performance-tuning-tips) mostradas na exibição monitoramento de cópia, aplique a sugestão e tente novamente. Caso contrário, [entenda os detalhes da execução da atividade de cópia](#understand-copy-activity-execution-details), verifique qual estágio tem a duração mais **longa** e aplique as diretrizes abaixo para melhorar o desempenho da cópia:

- **"Fila" sofreu duração longa:** isso significa que a atividade de cópia aguarda tempo na fila até que o ir auto-hospedado tenha o recurso a ser executado. Verifique a capacidade e o uso do IR e [aumente ou](create-self-hosted-integration-runtime.md#high-availability-and-scalability) reduza de acordo com sua carga de trabalho.

- **"Tempo de transferência até o primeiro byte" com duração de trabalho longa**: isso significa que sua consulta de origem demora muito para retornar qualquer dado. Verifique e otimize a consulta ou o servidor. Se precisar de mais ajuda, entre em contato com sua equipe de armazenamento de dados.

- **"Origem da listagem de transferência" sofreu duração de trabalho longa**: isso significa que a enumeração de arquivos de origem ou partições de dados de banco de dado de origem está lenta.

  - Verifique se a máquina IR auto-hospedada tem baixa latência conectando-se ao armazenamento de dados de origem. Se sua fonte estiver no Azure, você poderá usar [essa ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina ir hospedada internamente para a região do Azure, o que é menos o melhor.

  - Ao copiar dados da fonte baseada em arquivo, se você usar o **filtro curinga** no caminho da pasta ou no nome do arquivo ( `wildcardFolderPath` ou `wildcardFileName` ), ou usar o **filtro de tempo da última modificação do arquivo** ( `modifiedDatetimeStart` ou `modifiedDatetimeEnd` ), observe que esse filtro resultaria na atividade de cópia listando todos os arquivos na pasta especificada para o lado do cliente e, em seguida, aplicaria o filtro. Tal enumeração de arquivo poderia se tornar o afunilamento, especialmente quando apenas pequenos conjuntos de arquivos atingirem a regra de filtro.

    - Verifique se você pode [copiar arquivos com base no caminho ou nome do arquivo particionado DateTime](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Dessa forma, não traz a responsabilidade de listar a origem.

    - Verifique se você pode usar o filtro nativo do repositório de dados em vez disso, especificamente "**prefixo**" para o Amazon S3/blob do Azure/armazenamento de arquivos do Azure e "**listAfter/listBefore**" para ADLS Gen1. Esses filtros são um filtro do lado do servidor do armazenamento de dados e teriam um desempenho muito melhor.

    - Considere dividir um único conjunto de dados grande em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente, cada um resolve a parte dos dados. Você pode fazer isso com Lookup/GetMetadata + ForEach + copiar. Consulte [copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md) ou [migrar dados do Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md) modelos de solução como exemplo geral.

  - Verifique se o ADF relata qualquer erro de limitação na origem ou se o armazenamento de dados está sob o estado de alta utilização. Nesse caso, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do armazenamento de dados para aumentar o limite de limitação ou o recurso disponível.

- **"Transferência-leitura da origem" duração de trabalho longa experiente**: 

  - Verifique se a máquina IR auto-hospedada tem baixa latência conectando-se ao armazenamento de dados de origem. Se sua fonte estiver no Azure, você poderá usar [essa ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina ir hospedada internamente para as regiões do Azure, o que é menos o melhor.

  - Verifique se a máquina IR auto-hospedada tem largura de banda de entrada suficiente para ler e transferir os dados com eficiência. Se o armazenamento de dados de origem estiver no Azure, você poderá usar [essa ferramenta](https://www.azurespeed.com/Azure/Download) para verificar a velocidade de download.

  - Verifique a tendência de uso de CPU e memória do IR auto-hospedado em portal do Azure > sua página de visão geral de data factory >. Considere [aumentar/reduzir o ir](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se o uso da CPU for alto ou se a memória disponível estiver baixa.

  - Adote a prática recomendada de carregamento de dados específicos do conector se aplicável. Por exemplo:

    - Ao copiar dados do [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), da [tabela SAP](connector-sap-table.md#sap-table-as-source)e do [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), habilite as opções de partição de dados para copiar dados em paralelo.

    - Ao copiar dados do [HDFS](connector-hdfs.md), configure o para usar o DistCp.

    - Ao copiar dados do [Amazon redshift](connector-amazon-redshift.md), configure o para usar o descarregamento do redshift.

  - Verifique se o ADF relata qualquer erro de limitação na origem ou se o armazenamento de dados está sob alta utilização. Nesse caso, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do armazenamento de dados para aumentar o limite de limitação ou o recurso disponível.

  - Verifique a fonte de cópia e o padrão de coletor: 

    - Se você copiar dados de armazenamentos de dados habilitados para opção de partição, considere ajustar gradualmente as [cópias paralelas](copy-activity-performance-features.md), observe que muitas cópias paralelas podem até mesmo prejudicar o desempenho.

    - Caso contrário, considere dividir um único conjunto de dados grande em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente, cada um resolve a parte dos dados. Você pode fazer isso com Lookup/GetMetadata + ForEach + copiar. Consulte [copiar arquivos de vários contêineres](solution-template-copy-files-multiple-containers.md), [migrar dados do Amazon S3 para ADLS Gen2](solution-template-migration-s3-azure.md)ou [copiar em massa com](solution-template-bulk-copy-with-control-table.md) modelos de solução de tabela de controle como exemplo geral.

- **"Transferência-gravação no coletor" duração de trabalho longa com experiência**:

  - Adote a prática recomendada de carregamento de dados específicos do conector se aplicável. Por exemplo, ao copiar dados para o [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md), use o polybase ou a instrução de cópia. 

  - Verifique se a máquina IR auto-hospedada tem baixa latência conectando-se ao armazenamento de dados do coletor. Se o coletor estiver no Azure, você poderá usar [essa ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina ir hospedada internamente para a região do Azure, o que é menos o melhor.

  - Verifique se a máquina IR auto-hospedada tem largura de banda de saída suficiente para transferir e gravar os dados com eficiência. Se o armazenamento de dados do coletor estiver no Azure, você poderá usar [essa ferramenta](https://www.azurespeed.com/Azure/UploadLargeFile) para verificar a velocidade de upload.

  - Verifique se a tendência de uso de CPU e memória do IR auto-hospedado em portal do Azure > sua página de visão geral de data factory >. Considere [aumentar/reduzir o ir](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se o uso da CPU for alto ou se a memória disponível estiver baixa.

  - Verifique se o ADF relata qualquer erro de limitação no coletor ou se o armazenamento de dados está sob alta utilização. Nesse caso, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do armazenamento de dados para aumentar o limite de limitação ou o recurso disponível.

  - Considere o ajuste gradual das [cópias paralelas](copy-activity-performance-features.md), observe que muitas cópias paralelas podem até mesmo prejudicar o desempenho.


## <a name="connector-and-ir-performance"></a>Desempenho do conector e do IR 

Esta seção explora alguns guias de solução de problemas de desempenho para determinado tipo de conector ou tempo de execução de integração.

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>O tempo de execução da atividade varia de acordo com o Azure IR o IR do Azure VNet

O tempo de execução da atividade varia quando o conjunto de base é baseado em diferentes Integration Runtime.

- **Sintomas**: simplesmente alternar a lista suspensa serviço vinculado no conjunto de um executa as mesmas atividades de pipeline, mas tem tempos de execução drasticamente diferentes. Quando o conjunto de informações é baseado na Integration Runtime rede virtual gerenciada, leva mais tempo em média do que a execução quando com base no Integration Runtime padrão.  

- **Causa**: verificando os detalhes das execuções de pipeline, você pode ver que o pipeline lento está em execução no ir (rede virtual) da VNet gerenciada enquanto o normal está em execução no Azure ir. Por design, o IR da VNet gerenciada leva tempo de fila maior do que Azure IR, pois não estamos reservando um nó de computação por data factory, portanto, há um aquecimento para cada atividade de cópia iniciar e ocorre principalmente na junção VNet, em vez de Azure IR. 



    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>Baixo desempenho durante o carregamento de dados no Azure SQL Database

- **Sintomas**: a cópia de dados para o Azure SQL Database torna-se lenta.

- **Causa**: a causa raiz do problema é disparada principalmente pelo afunilamento do lado do banco de dados SQL do Azure. Veja a seguir algumas causas possíveis:

    - A camada do banco de dados SQL do Azure não é alta o suficiente.

    - O uso de DTU do banco de dados SQL do Azure está próximo de 100%. Você pode [monitorar o desempenho](../azure-sql/database/monitor-tune-overview.md) e considerar a atualização da camada do banco de dados SQL do Azure.

    - Os índices não estão definidos corretamente. Remova todos os índices antes de carregar os dados e recrie-os após a conclusão da carga.

    - WriteBatchSize não é grande o suficiente para ajustar o tamanho da linha do esquema. Tente aumentar a propriedade para o problema.

    - Em vez da inserção em massa, o procedimento armazenado está sendo usado, o que deve ter um desempenho pior. 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Tempo limite ou desempenho lento ao analisar arquivo grande do Excel

- **Sintomas**:

    - Quando você cria o conjunto de dados do Excel e importa o esquema de conexões/armazenamento, visualização de data, lista ou atualização de planilhas, você pode atingir um erro de tempo limite se o arquivo do Excel for grande em tamanho.

    - Quando você usa a atividade de cópia para copiar dados de um arquivo grande do Excel (>= 100 MB) para outro armazenamento de dados, você pode enfrentar um problema de desempenho ou OOM lento.

- **Causa**: 

    - Para operações como importação de esquema, visualização de dados e listagem de planilhas no conjunto do dados do Excel, o tempo limite é de 100 s e estáticos. Para grandes arquivos do Excel, essas operações podem não ser concluídas dentro do valor de tempo limite.

    - A atividade de cópia do ADF lê o arquivo inteiro do Excel na memória e, em seguida, localiza a planilha e as células especificadas para ler os dados. Esse comportamento ocorre devido ao uso do ADF do SDK subjacente.

- **Resolução**: 

    - Para importar o esquema, você pode gerar um arquivo de exemplo menor, que é um subconjunto do arquivo original, e escolher "importar esquema do arquivo de exemplo" em vez de "importar esquema da conexão/armazenamento".

    - Para a planilha de listagem, no menu suspenso da planilha, você pode clicar em "Editar" e inserir o nome/índice da planilha em vez disso.

    - Para copiar um arquivo grande do Excel (>100 MB) em outro repositório, você pode usar a origem do Excel de fluxo de dados que utiliza streaming de esporte e desempenho melhor.
    
## <a name="other-references"></a>Outras referências

Aqui estão as referências de monitoramento e ajuste do desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento de blob do Azure: [escalabilidade e metas de desempenho para armazenamento de BLOBs](../storage/blobs/scalability-targets.md) , [desempenho e lista de verificação de escalabilidade para armazenamento de BLOBs](../storage/blobs/storage-performance-checklist.md).
* Armazenamento de tabelas do Azure: [escalabilidade e metas de desempenho para armazenamento de tabelas](../storage/tables/scalability-targets.md) , [desempenho e lista de verificação de escalabilidade para armazenamento de tabelas](../storage/tables/storage-performance-checklist.md).
* Banco de dados SQL do Azure: você pode [monitorar o desempenho](../azure-sql/database/monitor-tune-overview.md) e verificar a porcentagem de DTU (unidade de transação do banco de dados).
* Análise de Synapse do Azure: seu recurso é medido em unidades de data warehouse (DWUs). Consulte [gerenciar poder de computação no Azure Synapse Analytics (visão geral)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server: [monitorar e ajustar o desempenho](/sql/relational-databases/performance/monitor-and-tune-for-performance).
* Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](/previous-versions//dn567661(v=vs.85)).

## <a name="next-steps"></a>Próximas etapas
Confira os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Guia de desempenho e escalabilidade da atividade de cópia](copy-activity-performance.md)
- [Recursos de otimização de desempenho da atividade de cópia](copy-activity-performance-features.md)
- [Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
