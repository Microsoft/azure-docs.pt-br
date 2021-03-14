---
title: SQL Server para SQL Server em VMs do Azure (guia de migração)
description: Siga este guia para migrar seus bancos de dados SQL Server individuais para SQL Server em VMs (máquinas virtuais) do Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: d95da29b732e2d520b3413628c9b4a1c403abed6
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488216"
---
# <a name="migration-guide-sql-server-to-sql-server-on-azure-vms"></a>Guia de migração: do SQL Server para o SQL Server nas VMs do Azure 
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Este guia de migração ensina a **descobrir**, **avaliar** e **migrar** seus bancos de dados de usuário de SQL Server para uma instância do SQL Server em VMS (máquinas virtuais) do Azure usando o backup e restauração e o envio de logs utilizando o [DMA (banco de dados assistente de migração)](/sql/dma/dma-overview) para avaliação. 

Você pode migrar SQL Server em execução no local ou em:

- SQL Server em máquinas virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de banco de dados relacional do Amazon (AWS RDS) 
- Mecanismo de computação (Google Cloud Platform-GCP)

Para obter informações sobre estratégias de migração adicionais, consulte a [visão geral da migração de VM SQL Server](sql-server-to-sql-on-azure-vm-migration-overview.md).

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-migration-overview/migration-process-flow-small.png" alt-text="Fluxo do processo de migração":::

## <a name="prerequisites"></a>Pré-requisitos

Migrar para o SQL Server em VMs do Azure requer o seguinte: 

- [Assistente de migração de banco de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
- Um [projeto de migrações para Azure](../../../migrate/create-manage-projects.md).
- Um SQL Server de destino preparado [na VM do Azure](../../virtual-machines/windows/create-sql-vm-portal.md) que é da mesma versão ou posterior à do SQL Server de origem.
- [Conectividade entre o Azure e o local](/azure/architecture/reference-architectures/hybrid-networking).
- [Escolhendo uma estratégia de migração apropriada](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate).

## <a name="pre-migration"></a>Pré-migração

Antes de começar a migração, descubra a topologia do seu ambiente SQL e avalie a viabilidade da sua migração pretendida. 

### <a name="discover"></a>Descobrir

As migrações para Azure avaliam a adequação de migração de computadores locais, executam o dimensionamento baseado em desempenho e fornecem estimativas de custo para execução local. Para planejar a migração, use as migrações para Azure para [identificar as fontes de dados existentes e os detalhes sobre os recursos que](../../../migrate/concepts-assessment-calculation.md) suas instâncias de SQL Server usam. Esse processo envolve a verificação da rede para identificar todas as suas instâncias de SQL Server em sua organização com a versão e os recursos em uso. 

> [!IMPORTANT]
> Ao escolher uma máquina virtual de destino do Azure para sua instância de SQL Server, considere as [diretrizes de desempenho para SQL Server em VMs do Azure](../../virtual-machines/windows/performance-guidelines-best-practices.md).

Para obter ferramentas de descoberta adicionais, consulte [serviços e ferramentas](../../../dms/dms-tools-matrix.md#business-justification-phase) disponíveis para cenários de migração de dados.


### <a name="assess"></a>Avaliar

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Depois de descobrir todas as fontes de dados, use o [Assistente de migração de dados (DMA)](/sql/dma/dma-overview) para avaliar SQL Server instância (s) local migrando para uma instância do SQL Server na VM do Azure para entender as lacunas entre as instâncias de origem e de destino. 


> [!NOTE]
> Se você _não_ estiver atualizando a versão do SQL Server, ignore esta etapa e vá para a seção [migrar](#migrate) . 


#### <a name="assess-user-databases"></a>Avaliar bancos de dados de usuário 

O Assistente de Migração de Dados (DMA) ajuda sua migração para uma plataforma de dados moderna detectando problemas de compatibilidade que podem afetar a funcionalidade do banco de dado em sua nova versão do SQL Server. O DMA recomenda melhorias de desempenho e confiabilidade para seu ambiente de destino e também permite que você mova o esquema, os dados e os objetos de logon do seu servidor de origem para o servidor de destino.

Consulte [avaliação](/sql/dma/dma-migrateonpremsql) para saber mais. 


> [!IMPORTANT]
>Com base no tipo de avaliação, as permissões necessárias no SQL Server de origem podem ser diferentes. 
   > - Para o supervisor de **paridade de recurso** , as credenciais fornecidas para se conectar ao banco de dados de SQL Server de origem devem ser membros da função de servidor *sysadmin* .
   > - Para o supervisor de problemas de compatibilidade, as credenciais fornecidas devem ter pelo menos `CONNECT SQL` `VIEW SERVER STATE` e `VIEW ANY DEFINITION` permissões.
   > - O DMA destacará as permissões necessárias para o supervisor escolhido antes de executar a avaliação.


#### <a name="assess-applications"></a>Avaliar aplicativos

Normalmente, uma camada de aplicativo acessa os bancos de dados de usuário para persistir e modificar o dado.  O DMA pode avaliar a camada de acesso a dados de um aplicativo de duas maneiras: 

- Usando [eventos estendidos](/sql/relational-databases/extended-events/extended-events) capturados ou [SQL Server Profiler rastreamentos ](/sql/tools/sql-server-profiler/create-a-trace-sql-server-profiler) de seus bancos de dados de usuário. Você também pode usar o [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-capture-trace) para criar um log de rastreamento que também pode ser usado para testes a/B.

- Usando o [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) (DAMT), que fornece descoberta e avaliação de consultas SQL dentro do código e é usado para migrar o código-fonte de um aplicativo de uma plataforma de banco de dados para outra. Essa ferramenta dá suporte a uma variedade de tipos de arquivos populares, incluindo C# e Java, XML e texto simples. Para obter um guia sobre como executar uma avaliação do DAMT, consulte o blog [usar o DMAT](https://techcommunity.microsoft.com/t5/microsoft-data-migration/using-data-migration-assistant-to-assess-an-application-s-data/ba-p/990430) .

Use o DMA para [importar](/sql/dma/dma-assesssqlonprem#add-databases-and-extended-events-trace-to-assess) arquivos de rastreamento capturados ou arquivos DAMT durante a avaliação de bancos de dados de usuário. 


#### <a name="scale-assessments"></a>Avaliações de escala

Se você tiver vários servidores que exigem uma avaliação DMA, poderá automatizar o processo por meio da [interface de linha de comando](/sql/dma/dma-commandline). Usando a interface, você pode preparar comandos de avaliação com antecedência para cada instância de SQL Server no escopo para migração. 

Para relatórios resumidos em grandes imóveis, as avaliações de Assistente de Migração de Dados (DMA) agora podem ser [consolidadas em migrações para Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb).

#### <a name="refactor-databases-with-dma"></a>Refatorar bancos de dados com DMA

Com base nos resultados da avaliação de DMA, você pode ter uma série de recomendações para garantir que seus bancos de dados do usuário executem e funcionem corretamente após a migração. O DMA fornece detalhes sobre os objetos afetados, bem como recursos de como resolver cada problema. É recomendável que todas as alterações significativas e alterações de comportamento sejam resolvidas antes da migração de produção.

Para recursos preteridos, você pode optar por executar seus bancos de dados de usuário em seu modo de [compatibilidade](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) original se desejar evitar fazer essas alterações e acelerar a migração. No entanto, isso impedirá a [atualização da compatibilidade do banco de dados](/sql/database-engine/install-windows/compatibility-certification#compatibility-levels-and-database-engine-upgrades) até que os itens preteridos tenham sido resolvidos.

É altamente recomendável que todas as correções de DMA sejam inseridas em script e aplicadas ao banco de dados de SQL Server de destino durante a [pós-implantação](#post-migration).

> [!CAUTION]
> Nem todas as versões do SQL Server dão suporte a todos os modos de compatibilidade. Verifique se a [versão de SQL Server de destino](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) dá suporte à compatibilidade de banco de dados escolhida. Por exemplo, SQL Server 2019 não oferece suporte a bancos de dados com compatibilidade de nível 90 (que é SQL Server 2005). Esses bancos de dados exigirão, pelo menos, uma atualização para o nível de compatibilidade 100.
>

## <a name="migrate"></a>Migrar

Depois de concluir as etapas de pré-migração, você estará pronto para migrar os bancos de dados e componentes do usuário. Migre seus bancos de dados usando seu [método de migração](sql-server-to-sql-on-azure-vm-migration-overview.md#migrate)preferencial.  

Veja a seguir as etapas para executar uma migração usando backup e restauração, ou uma migração de tempo de inatividade mínima usando backup e restauração, juntamente com o envio de logs. 

### <a name="backup-and-restore"></a>Backup e restauração

Para executar uma migração padrão usando backup e restauração, siga estas etapas: 

1. Configure a conectividade com o SQL Server de destino na VM do Azure, com base em suas necessidades. Consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure (Gerenciador de Recursos)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Pausar/parar qualquer aplicativo que esteja usando bancos de dados destinados à migração. 
1. Verifique se os bancos de dados do usuário estão inativos usando o [modo de usuário único](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
1. Execute um backup de banco de dados completo para um caminho local.
1. Copie seus arquivos de backup locais para sua VM usando a área de trabalho remota, o [Data Explorer do Azure](/azure/data-explorer/data-explorer-overview)ou o [Utilitário de linha de comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md) (recomenda-se backups de 2 TB >).
1. Restaure backups completos de banco de dados para o SQL Server na VM do Azure.

### <a name="log-shipping--minimize-downtime"></a>Envio de logs (minimizar tempo de inatividade)

Para executar uma migração de tempo de inatividade mínima usando backup, restauração e envio de logs, siga estas etapas: 

1. Configure a conectividade com o SQL Server de destino na VM do Azure, com base em suas necessidades. Consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure (Gerenciador de Recursos)](../../virtual-machines/windows/ways-to-connect-to-sql.md).
1. Verifique se os bancos de dados do usuário local a serem migrados estão em um modelo de recuperação completa ou bulk-logged.
1. Execute um backup de banco de dados completo em uma localização local e modifique quaisquer trabalhos de backup de banco de dados completos existentes para usar [COPY_ONLY](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) palavra-chave para preservar a cadeia de logs.
1. Copie seus arquivos de backup locais para sua VM usando a área de trabalho remota, o [Data Explorer do Azure](/azure/data-explorer/data-explorer-overview)ou o [Utilitário de linha de comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md) (>recomendado backups de 1 TB).
1. Restaure backups completos de banco de dados no SQL Server na VM do Azure.
1. Configure o [envio de logs](/sql/database-engine/log-shipping/configure-log-shipping-sql-server) entre o banco de dados local e o SQL Server de destino na VM do Azure. Certifique-se de não reinicializar os bancos de dados, pois isso já foi concluído nas etapas anteriores.
1. **Recortar** para o servidor de destino. 
   1. Pausar/parar aplicativos usando bancos de dados a serem migrados. 
   1. Verifique se os bancos de dados do usuário estão inativos usando o [modo de usuário único](/sql/relational-databases/databases/set-a-database-to-single-user-mode). 
   1. Quando estiver pronto, execute um [failover controlado](/sql/database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server) por envio de logs de bancos de dados locais para direcionar SQL Server na VM do Azure.



### <a name="migrating-objects-outside-user-databases"></a>Migrando objetos fora de banco (s) de dados do usuário

Pode haver objetos SQL Server adicionais que são necessários para a operação direta dos bancos de dados de usuário após a migração. 

A tabela a seguir fornece uma lista de componentes e métodos de migração recomendados que podem ser concluídos antes ou após a migração dos bancos de dados de usuário: 


| **Recurso** | **Componente** | **Método (s) de migração** |
| --- | --- | --- |
| **Bancos de dados** | Modelar  | Script com SQL Server Management Studio |
|| TempDB | Planeje mover o TempDB para o [disco temporário da VM do Azure (SSD](../../virtual-machines/windows/performance-guidelines-best-practices.md#temporary-disk)) para obter o melhor desempenho. Certifique-se de escolher um tamanho de VM que tenha um SSD local suficiente para acomodar seu TempDB. |
|| Bancos de dados de usuário com FileStream |  Use os métodos de [backup e restauração](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#back-up-and-restore) para a migração. O DMA não oferece suporte a bancos de dados com FileStream. |
| **Segurança** | Logons SQL Server e do Windows | Use o DMA para [migrar logons de usuário](/sql/dma/dma-migrateserverlogins). |
|| SQL Server funções | Script com SQL Server Management Studio |
|| Provedores criptográficos | Recomenda- [se converter para usar Azure Key Vault serviço](../../virtual-machines/windows/azure-key-vault-integration-configure.md). Este procedimento usa o [provedor de recursos de VM do SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md). |
| **Objetos de servidor** | Dispositivos de backup | Substitua pelo backup de banco de dados usando o [serviço de backup do Azure](../../../backup/backup-sql-server-database-azure-vms.md) ou backups de gravação no [armazenamento do azure](../../virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md) (SQL Server 2012 SP1 Cu2 +). Este procedimento usa o [provedor de recursos de VM do SQL](../../virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).|
|| Servidores vinculados | Script com SQL Server Management Studio. |
|| Gatilhos do servidor | Script com SQL Server Management Studio. |
| **Replicação** | Publicações locais | Script com SQL Server Management Studio. |
|| Assinantes locais | Script com SQL Server Management Studio. |
| **Polybase** | PolyBase | Script com SQL Server Management Studio. |
| **Gerenciamento** | Database Mail | Script com SQL Server Management Studio. |
| **SQL Server Agent** | Trabalhos | Script com SQL Server Management Studio. |
|| Alertas | Script com SQL Server Management Studio. |
|| Operadores | Script com SQL Server Management Studio. |
|| Proxies | Script com SQL Server Management Studio. |
| **Sistema operacional** | Arquivos, compartilhamentos de arquivos | Anote quaisquer arquivos ou compartilhamentos de arquivos adicionais usados pelos servidores SQL e replique-os no destino da VM do Azure. |


## <a name="post-migration"></a>Pós-migração

Depois de concluir com êxito o estágio de migração, passe por uma série de tarefas de pós-implantação para garantir que tudo esteja funcionando da maneira mais tranqüila e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Fazer isso pode, em alguns casos, exigir alterações nos aplicativos.

Aplique qualquer banco de dados Assistente de Migração correções recomendadas para os bancos de dados do usuário. É recomendável que eles sejam incluídos no script para garantir a consistência e permitir a automação.

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste em executar as seguintes atividades:

1. **Desenvolver testes de validação.**  Use consultas SQL para testar migrações de banco de dados. Crie consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.
2. **Configure o ambiente de teste.**  O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.
3. **Executar testes de validação.**  Execute os testes de validação em relação à origem e ao destino e, em seguida, analise os resultados.
4. **Executar testes de desempenho.**  Execute o teste de desempenho em relação à origem e ao destino e, em seguida, analise e compare os resultados.

> [!TIP]
> Use o [Assistente para experimentos de banco de dados (DEA)](/sql/dea/database-experimentation-assistant-overview) para auxiliar na avaliação do desempenho de SQL Server de destino.
>

### <a name="optimize"></a>Otimizar

A fase pós-implantação é crucial para reconciliar quaisquer problemas com a precisão e a exatidão dos dados, bem como resolver possíveis problemas de desempenho com a carga de trabalho.

Para obter mais informações sobre esses problemas e etapas específicas para atenuá-los, consulte os seguintes recursos:

- [Guia de validação e otimização após a migração.](/sql/relational-databases/post-migration-validation-and-optimization-guide)
- [Ajustando o desempenho em máquinas virtuais do Azure SQL](../../virtual-machines/windows/performance-guidelines-best-practices.md).
- [Centro de otimização de custos do Azure](https://azure.microsoft.com/overview/cost-optimization/).

## <a name="next-steps"></a>Próximas etapas

- Para verificar a disponibilidade dos serviços aplicáveis ao SQL Server consulte o [centro de infraestrutura global do Azure](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte o artigo [serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre o SQL Azure, consulte:
   - [Opções de implantação](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server em VMs do Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para obter informações sobre licenciamento, consulte
   - [Traga sua própria licença com o Benefício Híbrido do Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Obtenha suporte estendido gratuito para SQL Server 2008 e SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).