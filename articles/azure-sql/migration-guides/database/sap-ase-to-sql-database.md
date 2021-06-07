---
title: 'SAP ASE para banco de dados SQL do Azure: guia de migração'
description: Neste guia, você aprenderá a migrar seus bancos de dados do SAP ASE para um Azure SQL usando o Assistente de Migração do SQL Server para SAP Adapter Server Enterprise.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 138a23b610ab96194424bb0f88cf94f516c2d223
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626445"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Guia de migração: SAP ASE para banco de dados SQL do Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Neste guia, você aprenderá a migrar seus bancos de dados do ASE (servidor de adaptador SAP Enterprise) para um Azure SQL Database usando o Assistente de Migração do SQL Server para SAP Adapter Server Enterprise.

Para obter outros guias de migração, consulte [Guia de migração de banco de dados do Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos 

Antes de começar a migrar seu banco de dados do SAP SE para o banco de dados SQL, faça o seguinte:

- Verifique se o seu ambiente de origem tem suporte. 
- Baixe e instale o [Assistente de migração do SQL Server para SAP Adaptive Server Enterprise (anteriormente conhecido como SAP Sybase ase)](https://www.microsoft.com/en-us/download/details.aspx?id=54256).
- Verifique se você tem conectividade e permissões suficientes para acessar a origem e o destino.

## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.

### <a name="assess"></a>Avaliar

Usando o [Assistente de migração do SQL Server (SSMA) para SAP Adaptive Server Enterprise (o SAP Sybase ase)](https://www.microsoft.com/en-us/download/details.aspx?id=54256), você pode revisar objetos e dados de banco de dados, avaliar bancos de dado para migração, migrar objetos de banco do dados Sybase para o banco de dados SQL e, em seguida, migrar dados para o banco de dado SQL. Para saber mais, confira [Assistente de Migração do SQL Server para Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Para criar uma avaliação, faça o seguinte: 

1. Abra SSMA para Sybase. 
1. Selecione **arquivo** e, em seguida, selecione **novo projeto**. 
1. No painel **novo projeto** , insira um nome e um local para seu projeto e, na lista suspensa **migrar para** , selecione **banco de dados SQL do Azure**. 
1. Selecione **OK**.
1. No painel **conectar-se ao Sybase** , insira os detalhes da conexão SAP. 
1. Clique com o botão direito do mouse no banco de dados SAP que você deseja migrar e selecione **criar relatório**. Isso gera um relatório HTML. Como alternativa, você pode selecionar a guia **criar relatório** no canto superior direito.
1. Examine o relatório HTML para entender as estatísticas de conversão e quaisquer erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos do SAP ASE e o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects. Por exemplo:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>Validar os mapeamentos de tipo

Antes de executar a conversão de esquema, valide os mapeamentos de tipo de dados padrão ou altere-os com base nos requisitos. Você pode fazer isso selecionando **ferramentas**  >  **configurações de projeto** ou pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados do SAP ase**.

### <a name="convert-the-schema"></a>Converter o esquema

Para converter o esquema, faça o seguinte:

1. Adicional Para converter consultas dinâmicas ou especializadas, clique com o botão direito do mouse no nó e selecione **Adicionar instrução**. 
1. Selecione a guia **conectar-se ao banco de dados SQL do Azure** e insira os detalhes do banco de dados SQL. Você pode optar por se conectar a um banco de dados existente ou fornecer um novo nome. Nesse caso, um banco de dados será criado no servidor de destino.
1. No painel **Gerenciador de metadados do Sybase** , clique com o botão direito do mouse no esquema do SAP ase com o qual você está trabalhando e, em seguida, selecione **converter esquema**. 
1. Depois que o esquema tiver sido convertido, compare e revise a estrutura convertida para a estrutura original identificar possíveis problemas. 

   Após a conversão do esquema, você pode salvar este projeto localmente para um exercício de correção de esquema offline. Para fazer isso, selecione **arquivo**  >  **salvar projeto**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL.

1. No painel **saída** , selecione **examinar resultados** e revise todos os erros no painel **lista de erros** . 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Para fazer isso, selecione **arquivo**  >  **salvar projeto**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL.

## <a name="migrate-the-databases"></a>Migrar os bancos de dados 

Depois de ter os pré-requisitos necessários em vigor e concluir as tarefas associadas *ao estágio de pré-migração,* você estará pronto para executar a migração de esquema e de dados.

Para publicar o esquema e migrar os dados, faça o seguinte: 

1. Publique o esquema. No painel **Gerenciador de metadados do banco de dados SQL do Azure** , clique com o botão direito do mouse no banco de dados e selecione **sincronizar com Banco de dados**. Essa ação publica o esquema do SAP ASE em seu banco de dados SQL.

1. Migre os dados. No painel do **Gerenciador de metadados do SAP ase** , clique com o botão direito do mouse no banco de dados ou objeto do SAP ase que você deseja migrar e selecione **migrar dados**. Como alternativa, você pode selecionar a guia **migrar dados** no canto superior direito. 

   Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dado, expanda **tabelas** e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção. 
1. Depois que a migração for concluída, exiba o **relatório de migração de dados**. 
1. Valide a migração examinando os dados e o esquema. Para fazer isso, conecte-se ao banco de dados SQL usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de *migração* , você precisa concluir uma série de tarefas de pós-implantação para garantir que tudo esteja funcionando da maneira mais tranqüila e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.

1. **Configurar um ambiente de teste**: o ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.

1. **Executar testes de validação**: executar testes de validação em relação à origem e ao destino e, em seguida, analisar os resultados.

1. **Executar testes de desempenho**: executar testes de desempenho em relação à origem e ao destino e, em seguida, analisar e comparar os resultados.


### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar quaisquer problemas de precisão de dados, verificar a integridade e resolver problemas de desempenho com a carga de trabalho.

Para obter mais informações sobre esses problemas e as etapas para atenuá-los, consulte o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos e tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o banco de dados SQL do Azure, consulte:
   - [Uma visão geral do banco de dados SQL](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/)  

- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, confira:
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para custos e dimensionamento de cargas de trabalho para migração para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).
