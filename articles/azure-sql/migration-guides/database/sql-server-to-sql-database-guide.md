---
title: 'SQL Server ao banco de dados SQL do Azure: guia de migração'
description: Siga este guia para migrar seus bancos de dados SQL Server para o banco de dados SQL do Azure.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 03/19/2021
ms.openlocfilehash: e2de694a153276dcace1070d35af44dec1056e03
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564918"
---
# <a name="migration-guide-sql-server-to-azure-sql-database"></a>Guia de migração: SQL Server para o banco de dados SQL do Azure
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Este guia ajuda você a migrar sua instância de SQL Server para o banco de dados SQL do Azure. 

Você pode migrar SQL Server em execução no local ou em: 

- SQL Server em Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de banco de dados relacional do Amazon (AWS RDS) 
- Mecanismo de computação (Google Cloud Platform-GCP)  
- SQL de nuvem para SQL Server (Google Cloud Platform – GCP) 

Para obter mais informações de migração, consulte [visão geral da migração](sql-server-to-sql-database-overview.md). Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Fluxo do processo de migração":::

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar seu SQL Server para o banco de dados SQL do Azure, verifique se você tem os seguintes pré-requisitos: 

- Um [método de migração](sql-server-to-sql-database-overview.md#compare-migration-options) escolhido e as ferramentas correspondentes.
- [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) instalado em um computador que pode se conectar ao seu SQL Server de origem.
- Um [banco de dados SQL do Azure](../../database/single-database-create-quickstart.md)de destino. 
- Conectividade e as permissões adequadas para acessar a origem e o destino. 



## <a name="pre-migration"></a>Pré-migração

Depois de verificar se o seu ambiente de origem tem suporte, comece com o estágio pré-migração. Descubra todas as fontes de dados existentes, avalie a viabilidade da migração e identifique quaisquer problemas de bloqueio que possam impedir sua migração. 

### <a name="discover"></a>Descobrir

Na fase de descoberta, verifique a rede para identificar todas as instâncias de SQL Server e os recursos usados pela sua organização. 

Use as [migrações para Azure](../../../migrate/migrate-services-overview.md) para avaliar a adequação de migração de servidores locais, executar o dimensionamento com base no desempenho e fornecer estimativas de custo para executá-los no Azure. 

Como alternativa, use o [Kit de ferramentas de avaliação e planejamento da Microsoft (o "MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) para avaliar sua infraestrutura de ti atual. O kit de ferramentas fornece uma poderosa ferramenta de inventário, avaliação e relatórios para simplificar o processo de planejamento de migração. 

Para obter mais informações sobre as ferramentas disponíveis para uso na fase de descoberta, consulte [serviços e ferramentas disponíveis para cenários de migração de dados](../../../dms/dms-tools-matrix.md). 

### <a name="assess"></a>Avaliar 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Depois que as fontes de dados tiverem sido descobertas, avalie qualquer banco de SQL Server local que possa ser migrado para o banco de dados SQL do Azure para identificar os bloqueios de migração ou os problemas de compatibilidade. 

Você pode usar o Assistente de Migração de Dados (versão 4,1 e posterior) para avaliar os bancos de dados a serem obtidos: 

- [Recomendações de destino do Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Recomendações de SKU do Azure](/sql/dma/dma-sku-recommend-sql-db)

Para avaliar seu ambiente usando a avaliação de migração de banco de dados, siga estas etapas: 

1. Abra o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selecione **arquivo** e, em seguida, escolha **nova avaliação**. 
1. Especifique um nome de projeto, selecione SQL Server como o tipo de servidor de origem e, em seguida, selecione banco de dados SQL do Azure como o tipo de servidor de destino. 
1. Selecione os tipos de relatórios de avaliação que você deseja gerar. Por exemplo, compatibilidade de banco de dados e paridade de recurso. Com base no tipo de avaliação, as permissões necessárias no SQL Server de origem podem ser diferentes.  O DMA destacará as permissões necessárias para o supervisor escolhido antes de executar a avaliação.
    - A categoria de **paridade de recursos** fornece um conjunto abrangente de recomendações, alternativas disponíveis no Azure e etapas de mitigação para ajudá-lo a planejar seu projeto de migração. (permissões de sysadmin necessárias)
    - A categoria de **problemas de compatibilidade** identifica problemas de compatibilidade de recursos com suporte parcial ou sem suporte que podem bloquear a migração, bem como recomendações para solucioná-los ( `CONNECT SQL` , `VIEW SERVER STATE` e `VIEW ANY DEFINITION` permissões necessárias).
1. Especifique os detalhes de conexão de origem para seu SQL Server e conecte-se ao banco de dados de origem.
1. Selecione **Iniciar avaliação**. 
1. Após a conclusão do processo, selecione e examine os relatórios de avaliação para bloqueio de migração e problemas de paridade de recursos. O relatório de avaliação também pode ser exportado para um arquivo que pode ser compartilhado com outras equipes ou pessoal da sua organização. 
1. Determine o nível de compatibilidade do banco de dados que minimiza os esforços após a migração.  
1. Identifique a melhor SKU do banco de dados SQL do Azure para sua carga de trabalho local. 

Para saber mais, consulte [executar uma avaliação de migração SQL Server com assistente de migração de dados](/sql/dma/dma-assesssqlonprem).

Se a avaliação encontrar vários bloqueadores para confirmar se seu banco de dados não está pronto para uma migração de banco de dados SQL do Azure, em seguida, considere:

- [Azure SQL instância gerenciada](../managed-instance/sql-server-to-managed-instance-overview.md) se houver várias dependências com escopo de instância
- [SQL Server em máquinas virtuais do Azure](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) se o banco de dados SQL e o SQL instância gerenciada falharem em destinos adequados. 



#### <a name="scaled-assessments-and-analysis"></a>Avaliações e análises dimensionadas
O Assistente de Migração de Dados dá suporte à execução de avaliações dimensionadas e à consolidação dos relatórios de avaliação para análise. 

Se você tiver vários servidores e bancos de dados que precisam ser avaliados e analisados em escala para fornecer uma visão mais ampla do estado dos data, consulte os links a seguir para saber mais:

- [Executando avaliações dimensionadas usando o PowerShell](/sql/dma/dma-consolidatereports)
- [Analisando relatórios de avaliação usando o Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> A execução de avaliações em escala para vários bancos de dados, especialmente grandes, também pode ser automatizada usando o [Utilitário de linha de comando DMA](/sql/dma/dma-commandline) e carregada para [migrações para Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) para análise adicional e preparação de destino.

## <a name="migrate"></a>Migrações

Depois de concluir as tarefas associadas ao estágio de pré-migração, você estará pronto para executar o esquema e a migração de dados. 

Migre seus dados usando o [método de migração](sql-server-to-sql-database-overview.md#compare-migration-options)escolhido. 

Este guia descreve as duas opções mais populares-Assistente de Migração de Dados e o serviço de migração de banco de dados do Azure. 

### <a name="data-migration-assistant-dma"></a>AMD (Assistente de Migração de Dados)

Para migrar um banco de dados do SQL Server para o banco de dados SQL do Azure usando DMA, siga estas etapas: 

1. Baixe e instale o [Assistente de migração do banco de dados](https://www.microsoft.com/download/details.aspx?id=53595).
1. Crie um novo projeto e selecione **migração** como o tipo de projeto.
1. Defina o tipo de servidor de origem como **SQL Server** e o tipo de servidor de destino como banco de **dados**  **SQL do Azure**
1. No projeto de migração, especifique os detalhes do servidor de origem, como o nome do servidor, as credenciais para se conectar ao servidor e ao banco de dados de origem para migrar.
1. Nos detalhes do servidor de destino, especifique o nome do servidor do banco de dados SQL do Azure, as credenciais para se conectar ao servidor e ao banco de dados de destino para migrar para o.
1. Selecione os objetos de esquema e implante-os no banco de dados SQL do Azure de destino.
1. Por fim, selecione **Iniciar migração de dados** e monitore o progresso da migração.

Para obter um tutorial detalhado, consulte [migrar SQL Server locais ou SQL Server em VMs do Azure para o banco de dados SQL do Azure usando o assistente de migração de dados](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Dimensione seu banco de dados para uma camada de serviço mais alta e o tamanho de computação durante o processo de importação para maximizar a velocidade de importação fornecendo mais recursos. Em seguida, você poderá reduzir verticalmente após a importação ser realizada.</br>
> - O nível de compatibilidade do banco de dados importado é baseado no nível de compatibilidade do banco de dados de origem. 


### <a name="azure-database-migration-service-dms"></a>DMS (Serviço de Migração de Banco de Dados) do Azure

Para migrar os bancos de dados do SQL Server para o Azure SQL usando o DMS, siga as etapas abaixo:

1. Se você ainda não fez isso, registre o provedor de recursos **Microsoft. Datamigration** em sua assinatura. 
1. Crie uma instância do serviço de migração de banco de dados do Azure em um local desejado de sua escolha (preferencialmente na mesma região que o banco de dados SQL do Azure de destino). Selecione uma rede virtual existente ou crie uma nova para hospedar sua instância do DMS.
1. Depois que a instância DMS for criada, crie um novo projeto de migração e especifique o tipo de servidor de origem como **SQL Server** e o tipo de servidor de destino como **banco de dados SQL do Azure**. Escolha **migração de dados offline** como o tipo de atividade na folha de criação do projeto de migração.
1. Especifique a fonte SQL Server detalhes na página detalhes da **origem de migração** e os detalhes do banco de dados SQL do Azure de destino na página detalhes do **destino de migração** .
1. Mapeie os bancos de dados de origem e de destino para migração e, em seguida, selecione as tabelas que você deseja migrar.
1. Examine o resumo da migração e selecione **executar migração**. Em seguida, você pode monitorar a atividade de migração e verificar o progresso da migração do banco de dados.

Para obter um tutorial detalhado, consulte [migrar SQL Server para um banco de dados SQL do Azure usando DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Sincronização de dados e transferência

Ao usar opções de migração que replicam/sincronizam alterações de dados de origem para o destino continuamente, os dados de origem e o esquema podem ser alterados e descompassos do destino. Durante a sincronização de dados, certifique-se de que todas as alterações na origem sejam capturadas e aplicadas ao destino durante o processo de migração. 

Depois de verificar se os dados são iguais tanto na origem quanto no destino, você pode fazer a transferência da origem para o ambiente de destino. É importante planejar o processo de transferência com equipes de negócios/aplicativos para garantir que a interrupção mínima durante a transferência não afete a continuidade dos negócios. 

> [!IMPORTANT]
> Para obter detalhes sobre as etapas específicas associadas à execução de uma transferência como parte das migrações usando DMS, consulte [executando a transferência de migração](../../../dms/tutorial-sql-server-to-azure-sql.md).

## <a name="migration-recommendations"></a>Recomendações de migração

Para acelerar a migração para o banco de dados SQL do Azure, você deve considerar as seguintes recomendações:

|  | Contenção de recursos | Recomendação |
|--|--|--|
| **Fonte (normalmente no local)** |O afunilamento principal durante a migração na origem é a e/s de dados e a latência no arquivo de dados que precisa ser monitorado com cuidado.  |Com base em e/s de dados e latência de arquivo de dados e, dependendo de ser uma máquina virtual ou um servidor físico, você precisará envolver o administrador de armazenamento e explorar as opções para mitigar o afunilamento. |
|**Destino (banco de dados SQL do Azure)**|O maior fator de limitação é a taxa de geração de log e a latência no arquivo de log. Com o banco de dados SQL do Azure, você pode obter um máximo de 96 MB/s taxa de geração de log. | Para acelerar a migração, escale verticalmente o banco de BD SQL de destino para Comercialmente Crítico Gen5 8 vCore para obter a taxa de geração de log máxima de 96 MB/s e também obter baixa latência para o arquivo de log. A camada de serviço de [hiperescala](../../database/service-tier-hyperscale.md) fornece a taxa de logs de 100 MB/s, independentemente do nível de serviço escolhido |
|**Rede** |A largura de banda de rede necessária é igual à taxa máxima de ingestão de logs 96 MB/s (768 MB/s) |Dependendo da conectividade de rede do seu data center local para o Azure, verifique a largura de banda da rede (normalmente [Azure ExpressRoute](../../../expressroute/expressroute-introduction.md#bandwidth-options)) para acomodar a taxa máxima de ingestão de logs. |
|**Máquina virtual usada para Assistente de Migração de Dados (DMA)** |CPU é o afunilamento principal para a máquina virtual que executa o DMA |Itens a serem considerados para acelerar a migração de dados usando o </br>-VMs com computação intensiva do Azure </br>-Usar pelo menos F8s_v2 (8 VCORE) VM para executar DMA </br>-Verifique se a VM está em execução na mesma região do Azure que o destino |
|**DMS (Serviço de Migração de Banco de Dados do Azure)** |Consideração da contenção de recursos de computação e objetos de banco de dados para DMS |Use o vCore Premium 4. O DMS cuida automaticamente de objetos de banco de dados como chaves estrangeiras, gatilhos, restrições e índices não clusterizados e não precisa de intervenção manual.  |


## <a name="post-migration"></a>Pós-migração

Depois de concluir com êxito o estágio de migração, passe por uma série de tarefas de pós-atualização para garantir que tudo esteja funcionando de forma tranqüila e eficiente. 

A fase de pós-migração é crucial para reconciliar problemas de precisão de dados e verificar a integridade dos dados, além de resolver problemas de desempenho com a carga de trabalho. 

### <a name="remediate-applications"></a>Corrigir aplicativos 

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Fazer isso irá, em alguns casos, exigir alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.
1. **Configurar ambiente de teste**: O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: Execute o teste de desempenho na origem e no destino e, em seguida, analise e compare os resultados.


## <a name="leverage-advanced-features"></a>Aproveite os recursos avançados 

Não se esqueça de aproveitar os recursos avançados baseados em nuvem oferecidos pelo banco de dados SQL, como [alta disponibilidade interna](../../database/high-availability-sla.md), detecção de [ameaças](../../database/azure-defender-for-sql.md)e [monitoramento e ajuste da carga de trabalho](../../database/monitor-tune-overview.md). 

Alguns recursos SQL Server só estarão disponíveis quando o [nível de compatibilidade do banco de dados](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) for alterado para o nível de compatibilidade mais recente (150). 

Para saber mais, consulte [Gerenciando o banco de dados SQL do Azure após a migração](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o banco de dados SQL, consulte:
    - [Uma visão geral do banco de dados SQL do Azure](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).
