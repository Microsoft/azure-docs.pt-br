---
title: Migrar cargas de trabalho do SSIS (SQL Server Integration Services locais) para o SSIS no Azure Data Factory (ADF)
description: Migre cargas de trabalho do SSIS locais para o SSIS no ADF.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373599"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrar cargas de trabalho do SSIS local para o SSIS no ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Visão geral

Ao migrar suas cargas de trabalho de banco de dados do SQL Server local para os serviços de banco de dados do Azure, ou seja, o banco de dados SQL do Azure ou o SQL Instância Gerenciada do Azure, suas cargas de trabalho de ETL no SQL Server Integration Services (SSIS) como um dos serviços de valor agregado primário também precisarão ser migradas.

Azure-SSIS Integration Runtime (IR) no Azure Data Factory (ADF) dá suporte a pacotes SSIS em execução. Depois que Azure-SSIS IR for provisionado, você poderá usar ferramentas familiares, como SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) e utilitários de linha de comando, como dtinstall/dtutil/dtexec, para implantar e executar seus pacotes no Azure. Para obter mais informações, consulte [visão geral de elevação e deslocamento do Azure SSIS](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Este artigo destaca o processo de migração de suas cargas de trabalho de ETL do SSIS local para o SSIS no ADF. O processo de migração consiste em duas fases: **Avaliação** e **Migração**.

## <a name="assessment"></a>Avaliação

Para estabelecer um plano de migração completo, uma avaliação completa ajudará a identificar problemas com os pacotes SSIS de origem que impedirão uma migração bem-sucedida.

O AMD (Assistente de Migração de Dados) é uma ferramenta gratuita que pode ser baixada para essa finalidade e ser instalada e executada localmente. O projeto de avaliação DMA do tipo **Integration Services** pode ser criado para avaliar pacotes do SSIS em lotes e identificar problemas de compatibilidade que são apresentados nas seguintes categorias:

- Bloqueadores de migração: problemas de compatibilidade que bloqueiam a execução de pacotes de origem de migração em Azure-SSIS IR. O DMA fornece orientação para ajudá-lo a resolver esses problemas.

- Problemas informativos: recursos com suporte parcial ou preteridos que são usados em pacotes de origem. O DMA fornece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e etapas de mitigação para resolver.

### <a name="four-storage-types-for-ssis-packages"></a>Quatro tipos de armazenamento para pacotes SSIS

- Catálogo do SSIS (SSISDB). Introduzido com SQL Server 2012 e contém um conjunto de procedimentos armazenados, exibições e funções com valor de tabela usados para trabalhar com projetos/pacotes do SSIS.
- Sistema de arquivos.
- Banco de dados do sistema SQL Server (MSDB).
- Repositório de pacotes SSIS. Uma camada de gerenciamento de pacotes na parte superior de dois subtipos:
  - MSDB, que é um banco de dados do sistema em SQL Server usado para armazenar pacotes SSIS.
  - Sistema de arquivos gerenciado, que é uma pasta específica em SQL Server caminho de instalação usado para armazenar pacotes SSIS.

Atualmente, o DMA dá suporte à avaliação em lote de pacotes armazenados no **sistema de arquivos**, no **repositório de pacotes** e no catálogo do **SSIS** desde o **DMA versão v 5.0**.

Obtenha o [DMA](/sql/dma/dma-overview)e [realize sua avaliação de pacote com ele](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migração

Dependendo dos tipos de [armazenamento](#four-storage-types-for-ssis-packages) dos pacotes SSIS de origem e do destino de migração das cargas de trabalho de banco de dados, as etapas para migrar  **pacotes SSIS** e **SQL Server Agent trabalhos** que agendam as execuções de pacote SSIS podem variar. Há dois cenários:

- [**Azure SQL instância gerenciada** como destino de carga de trabalho de banco de dados](#azure-sql-managed-instance-as-database-workload-destination)
- [**Banco de dados SQL do Azure** como destino de carga de trabalho](#azure-sql-database-as-database-workload-destination)

Também é uma maneira prática de usar as [ferramentas de DevOps do SSIS](/sql/integration-services/devops/ssis-devops-overview)para fazer a reimplantação do pacote em lote para o destino de migração.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL instância gerenciada** como destino de carga de trabalho de banco de dados

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como migrar trabalhos do SSIS em lote|
|-|-|-|
|SSISDB|[Migrar **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Migrar trabalhos do SSIS para o agente do SQL Instância Gerenciada do Azure](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Arquivos|Reimplante-os em compartilhamentos de arquivos/arquivos do Azure por meio de dtinstall/dtutil/cópia manual ou para manter os sistemas de arquivos a serem acessados por meio de IR para VNet/auto-hospedado. Para obter mais informações, consulte [Utilitário dtutil](/sql/integration-services/dtutil-utility).|<li>[Migrar trabalhos do SSIS para o agente do SQL Instância Gerenciada do Azure](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrar com o [Assistente de migração de trabalho SSIS no SSMS](how-to-migrate-ssis-job-ssms.md) <li>Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportá-los para sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure por meio do SSMS/dtutil. Para obter mais informações, consulte [exportando pacotes SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Repositório de pacotes|Exporte-os para o repositório de pacotes por meio do SSMS/dtutil ou reimplante-os no repositório de pacotes por meio de dtinstall/dtutil/cópia manual. Para obter mais informações, consulte [gerenciar pacotes com Azure-SSIS Integration Runtime repositório de pacotes](azure-ssis-integration-runtime-package-store.md).|<li>[Migrar trabalhos do SSIS para o agente do SQL Instância Gerenciada do Azure](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Banco de dados SQL do Azure** como destino de carga de trabalho

| **Tipo de armazenamento de pacote** |Como migrar pacotes SSIS em lote|Como migrar trabalhos em lote|
|-|-|-|
|SSISDB|Reimplante no Azure-SSISDB por meio de SSDT/SSMS. Para obter mais informações, consulte [implantando pacotes do SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Sistema de Arquivos|Reimplante-os em compartilhamentos de arquivos/arquivos do Azure por meio de dtinstall/dtutil/cópia manual ou para manter os sistemas de arquivos a serem acessados por meio de IR para VNet/auto-hospedado. Para obter mais informações, consulte [Utilitário dtutil](/sql/integration-services/dtutil-utility).|<li> Migrar com o [Assistente de migração de trabalho SSIS no SSMS](how-to-migrate-ssis-job-ssms.md) <li> Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportá-los para sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure por meio do SSMS/dtutil. Para obter mais informações, consulte [exportando pacotes SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Repositório de pacotes|Exportá-los para sistemas de arquivos/compartilhamentos de arquivos/arquivos do Azure por meio do SSMS/dtutil ou reimplantá-los em compartilhamentos de arquivos/arquivos do Azure por meio de dtinstall/dtutil/cópia manual ou mantê-los em sistemas de arquivos para acesso via VNet/infravermelho auto-hospedado. Para obter mais informações, consulte Utilitário dtutil. Para obter mais informações, consulte [Utilitário dtutil](/sql/integration-services/dtutil-utility).|Converta-os em pipelines/atividades/gatilhos do ADF por meio de scripts/SSMS/ADF Portal. Para obter mais informações, consulte [recurso de agendamento do SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Recursos adicionais

- [Azure Data Factory](./introduction.md)
- [Assistente de Migração de Dados](/sql/dma/dma-overview)
- [Aumentar e deslocar cargas de trabalho do SSIS para a nuvem](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Ferramentas do DevOps do SSIS](/sql/integration-services/devops/ssis-devops-overview)
- [Migrar pacotes SSIS para Instância Gerenciada de SQL do Azure](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Reimplantar pacotes no banco de dados SQL do Azure](../dms/how-to-migrate-ssis-packages.md)

- [Acesso a dados local de Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Personalizar instalação para um Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Acessar armazenamentos de dados e compartilhamentos de arquivos com autenticação do Windows de pacotes do SSIS no Azure](ssis-azure-connect-with-windows-auth.md)
- [Usar a autenticação de identidade gerenciada](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Usar o Cofre de Chaves do Azure](store-credentials-in-key-vault.md)
- [Configurar o Azure-SSIS Integration Runtime para alto desempenho](configure-azure-ssis-integration-runtime-performance.md)
- [Como iniciar e parar o Azure-SSIS Integration Runtime em um agendamento](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Próximas etapas

- [Validar pacotes do SSIS implantados no Azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Executar pacotes do SSIS implantados no Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime de monitor](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Agendar execuções de pacote SSIS no Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)