---
title: 'Banco de dados SQL do Oracle para o Azure: guia de migração'
description: Este guia ensina a migrar seu esquema Oracle para o banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para Oracle (SSMA para Oracle).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 65307baa6e7d3216f011b82b177602da532d3fc6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644935"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Guia de migração: Oracle para banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina a migrar seus esquemas Oracle para o banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para Oracle.

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu esquema Oracle para o banco de dados SQL, você precisa: 

- Verificar se seu ambiente de origem é compatível. 
- Baixar o [SSMA (Assistente de Migração do SQL Server) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
- Um [banco de dados SQL do Azure](../../database/single-database-create-quickstart.md)de destino. 
- As [permissões necessárias para o SSMA para Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e para o [provedor](/sql/ssma/oracle/connect-to-oracle-oracletosql).
 

## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração. Essa parte do processo envolve a execução de um inventário dos bancos de dados que você precisa migrar, a avaliação desses bancos de dados quanto a possíveis problemas de migração ou bloqueadores e, em seguida, a resolução dos itens que você possa ter descoberto.



### <a name="assess"></a>Avaliar 


Use o Assistente de Migração do SQL Server (SSMA) para Oracle para revisar objetos e dados de banco de dados, avaliar bancos de dado para migração, migrar objetos de banco de dados para o banco de dados SQL do Azure e, finalmente, migrar dados para o banco de dado. 

Para criar uma avaliação, siga estas etapas: 

1. Abra [Assistente de migração do SQL Server para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar seu projeto e, em seguida, selecione banco de dados SQL do Azure como o destino de migração no menu suspenso. Selecione **OK**:

   ![Novo Projeto](./media/oracle-to-sql-database-guide/new-project.png)

1. Selecione **conectar-se ao Oracle**. Insira valores nos detalhes da conexão Oracle na caixa de diálogo **conectar ao Oracle** :

   ![Conectar-se ao Oracle](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   Selecione os esquemas do Oracle que você deseja migrar: 

   ![Selecionar esquema Oracle](./media/oracle-to-sql-database-guide/select-schema.png)

1. Clique com o botão direito do mouse no esquema Oracle que você deseja migrar no **Gerenciador de metadados Oracle** e escolha **criar relatório**. Isso vai gerar um relatório HTML. Como alternativa, você pode escolher **criar relatório** na barra de navegação depois de selecionar o banco de dados:

   ![Criar Relatório](./media/oracle-to-sql-database-guide/create-report.png)

1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário dos objetos Oracle e o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Relatório de avaliação](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas:

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **mapeamentos de tipo** : 

   ![Mapeamentos de Tipo](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados Oracle**.

### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estas etapas: 

1. (Opcional) Adicione consultas dinâmicas ou ad hoc a instruções. Clique com o botão direito do mouse no nó e escolha **Adicionar instruções**.
1. Selecione **conectar ao banco de dados SQL do Azure**. 
    1. Insira os detalhes de conexão para conectar seu banco de dados no banco de dados SQL do Azure.
    1. Escolha o banco de dados SQL de destino na lista suspensa ou forneça um novo nome, caso em que um banco de dados será criado no servidor de destino. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **conectar**:

    ![Conectar-se ao Banco de Dados SQL](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. Clique com o botão direito do mouse no esquema Oracle no **Gerenciador de metadados Oracle** e escolha **converter esquema**. Como alternativa, você pode escolher **converter esquema** na barra de navegação superior depois de selecionar o esquema:

   ![Converter esquema](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Após a conclusão da conversão, compare e revise os objetos convertidos para os objetos originais para identificar possíveis problemas e solucioná-los com base nas recomendações:

   ![Analisar esquema de recomendações](./media/oracle-to-sql-database-guide/table-mapping.png)

   Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e examine as recomendações:

   ![Revisar recomendações](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Selecione **Examinar resultados** no painel Saída e examine os erros no painel **Lista de erros**. 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL.

## <a name="migrate"></a>Migrar

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração. A migração envolve duas etapas: publicação do esquema e migração dos dados. 

Para publicar o esquema e migrar seus dados, siga estas etapas:

1. Publicar o esquema: clique com o botão direito do mouse no banco de dados do nó **bancos** de dados no **Gerenciador de metadados do banco de dados SQL do Azure** e escolha **sincronizar com o banco de dados**:

   ![Sincronizar com o Banco de Dados](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   Examine o mapeamento entre seu projeto de origem e seu destino:

   ![Sincronizar com a revisão do banco de dados](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. Migrar os dados: clique com o botão direito do mouse no objeto ou no Database que você deseja migrar no **Gerenciador de metadados Oracle** e escolha **migrar dados**. Como alternativa, você pode selecionar **Migrar Dados** na barra de navegação de linha superior. Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dados, expanda Tabelas e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção:

   ![Migrar dados](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Forneça detalhes de conexão para Oracle e banco de dados SQL do Azure.
1. Após a conclusão da migração, exiba o **Relatório da Migração de Dados**:  

   ![Relatório de migração de dados](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Conecte-se ao banco de dados SQL do Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração revisando os data e o esquema:

   ![Validar no SSMA](./media/oracle-to-sql-database-guide/validate-data.png)

Como alternativa, você também pode usar SQL Server Integration Services (SSIS) para executar a migração. Para obter mais informações, consulte: 

- [Introdução com SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: SSIS para Azure e movimentação de dados híbridos](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de **migração**, você precisará passar por uma série de tarefas de pós-migração para garantir que tudo esteja funcionando da maneira mais estável e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

O [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para Visual Studio Code que permite analisar o código-fonte Java e detectar consultas e chamadas à API de acesso a dados, fornecendo uma visão de painel único do que precisa ser resolvido para dar suporte ao novo back-end do banco de dados. Para saber mais, confira o blog [Migrar nosso aplicativo Java do Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 



### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste na execução das seguintes atividades:

1.  **Desenvolver testes de validação**. Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.

2.  **Configurar ambiente de teste**. O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.

3.  **Executar testes de validação**. Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.

4.  **Executar testes de desempenho**. Execute o teste de desempenho na origem e no destino e, em seguida, analise e compare os resultados.


### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar problemas de precisão de dados e verificar a integridade dos dados, além de resolver problemas de desempenho com a carga de trabalho.

> [!NOTE]
> Para obter detalhes adicionais sobre esses problemas e etapas específicas para atenuá-los, confira o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional com a conclusão desse cenário de migração, confira os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real.

| **Título/link**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e Ferramenta de Avaliação de Carga de Trabalho de Dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam muito a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.                                                          |
| [Artefatos de script de inventário do Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Esse ativo inclui uma consulta PL/SQL que afeta as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e status. Ele também fornece uma estimativa aproximada de "dados brutos" em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em um formato CSV.                                                                                                               |
| [Automatizar a Coleta e Fusão de Avaliação da Oracle do SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Esse conjunto de recursos usa um arquivo .csv como entrada (sources.csv nas pastas do projeto) para produzir os arquivos xml necessários para executar a avaliação do SSMA no modo de console. O source.csv é fornecido pelo cliente com base em um inventário de instâncias existentes do Oracle. Os arquivos de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [Erros comuns do SSMA para Oracle e como corrigi-los](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com o Oracle, você pode atribuir uma condição não escalar na cláusula WHERE. No entanto, SQL Server não dá suporte a esse tipo de condição. Como resultado, o SSMA (Assistente de Migração do SQL Server) para Oracle não converte consultas com uma condição não escalar na cláusula WHERE, em vez de gerar um erro O2SS0001. Este white paper fornece mais detalhes sobre o problema e as maneiras de resolvê-lo.          |
| [Manual de migração do Oracle para SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento se concentra nas tarefas associadas à migração de um esquema do Oracle para a versão mais recente da base do SQL Server. Se a migração exigir alterações nos recursos/funcionalidades, o possível impacto de cada alteração nos aplicativos que usam o banco de dados precisa ser considerado com cuidado.                                                     |

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudar você com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, confira o artigo [Serviços e ferramentas para migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o banco de dados SQL do Azure, consulte: 
  - [Uma visão geral do banco de dados SQL do Azure](../../database/sql-database-paas-overview.md)
  - [Calculadora do custo total de propriedade (TCO) do Azure](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para ver conteúdo de vídeo, consulte: 
    - [Visão geral da jornada de migração e das ferramentas/serviços recomendados para executar a avaliação e a migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)