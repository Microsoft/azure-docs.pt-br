---
title: 'MySQL para banco de dados SQL do Azure: guia de migração'
description: Neste guia, você aprende a migrar seus bancos de dados MySQL para um banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para MySQL (SSMA para MySQL).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 8952e6e1bda8763d403c02dcd5815f1e1c0941e8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641865"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>Guia de migração: MySQL para banco de dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Neste guia, você aprende a migrar seu banco de dados MySQL para um banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para MySQL (SSMA para MySQL). 

Para obter outros guias de migração, consulte [Guia de migração de banco de dados do Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar seu banco de dados MySQL para um banco de dados SQL, faça o seguinte:

- Verifique se o seu ambiente de origem tem suporte. Atualmente, há suporte para MySQL 5,6 e 5,7. 
- Baixe e instale o [Assistente de migração do SQL Server para MySQL](https://www.microsoft.com/download/details.aspx?id=54257).
- Verifique se você tem conectividade e permissões suficientes para acessar a origem e o destino.

## <a name="pre-migration"></a>Pré-migração 

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.

### <a name="assess"></a>Avaliar 

Use o Assistente de Migração do SQL Server (SSMA) para MySQL para revisar objetos de banco de dados e data e avaliar os bancos de dado para migração. 

Para criar uma avaliação, faça o seguinte:

1. Abra o [SSMA para MySQL](https://www.microsoft.com/download/details.aspx?id=54257). 
1. Selecione **arquivo** e, em seguida, selecione **novo projeto**. 
1. No painel **novo projeto** , insira um nome e um local para seu projeto e, na lista suspensa **migrar para** , selecione **banco de dados SQL do Azure**. 
1. Selecione **OK**.

   ![Captura de tela do painel "novo projeto" para inserir o nome, o local e o destino do projeto de migração.](./media/mysql-to-sql-database-guide/new-project.png)

1. Selecione a guia **conectar-se ao MySQL** e forneça detalhes para conectar seu servidor MySQL. 

   ![Captura de tela do painel "conectar ao MySQL" para especificar conexões com a origem.](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. No painel **Gerenciador de metadados do MySQL** , clique com o botão direito do mouse no esquema MySQL e selecione **criar relatório**. Como alternativa, você pode selecionar a guia **criar relatório** no canto superior direito.

   ![Captura de tela dos links "criar relatório" no SSMA para MySQL.](./media/mysql-to-sql-database-guide/create-report.png)

1. Examine o relatório HTML para entender as estatísticas de conversão, os erros e os avisos. Analise-o para entender os problemas de conversão e resoluções. 
   Você também pode abrir o relatório no Excel para obter um inventário de objetos MySQL e entender o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects. Por exemplo: 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![Captura de tela de um exemplo de relatório de conversão no SSMA.](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>Validar os tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso: 

1. Selecione **ferramentas** e, em seguida, selecione **configurações do projeto**.  
1. Selecione a guia **mapeamentos de tipo** . 

   ![Captura de tela do painel "mapeamento de tipo" no SSMA para MySQL.](./media/mysql-to-sql-database-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando o nome da tabela no painel do **Explorador de metadados do MySQL** . 

### <a name="convert-the-schema"></a>Converter o esquema 

Para converter o esquema, faça o seguinte: 

1. Adicional Para converter consultas dinâmicas ou especializadas, clique com o botão direito do mouse no nó e selecione **Adicionar instrução**. 

1. Selecione a guia **conectar-se ao banco de dados SQL do Azure** e, em seguida, faça o seguinte:

   a. Insira os detalhes para se conectar ao banco de dados SQL.  
   b. Na lista suspensa, selecione o banco de dados SQL de destino. Ou você pode fornecer um novo nome, caso em que um banco de dados será criado no servidor de destino.  
   c. Forneça detalhes de autenticação.  
   d. Selecione **Conectar**.

   ![Captura de tela do painel "conectar ao banco de dados SQL do Azure" no SSMA para MySQL.](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. Clique com o botão direito do mouse no esquema com o qual você está trabalhando e selecione **converter esquema**. Como alternativa, você pode selecionar a guia **converter esquema** no canto superior direito.

   ![Captura de tela do comando "converter esquema" no painel "Gerenciador de metadados do MySQL".](./media/mysql-to-sql-database-guide/convert-schema.png)

1. Depois que a conversão for concluída, examine e compare os objetos convertidos com os objetos originais para identificar possíveis problemas e solucioná-los com base nas recomendações. 

   ![Captura de tela mostrando uma comparação dos objetos convertidos com os objetos originais.](./media/mysql-to-sql-database-guide/table-comparison.png)

   Compare o texto Transact-SQL convertido com o código original e examine as recomendações.

   ![Captura de tela mostrando uma comparação de consultas convertidas para o código-fonte.](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. No painel **saída** , selecione **examinar resultados** e examine os erros no painel **lista de erros** . 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Para fazer isso, selecione **arquivo**  >  **salvar projeto**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no banco de dados SQL.

   Compare os procedimentos convertidos com os procedimentos originais, conforme mostrado aqui: 

   ![Captura de tela mostrando uma comparação dos procedimentos convertidos para os procedimentos originais.](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>Migrar os bancos de dados 

Depois de avaliar seus bancos de dados e solucionar quaisquer discrepâncias, você pode executar o processo de migração. A migração envolve duas etapas: publicar o esquema e migrar os dados. 

Para publicar o esquema e migrar os dados, faça o seguinte: 

1. Publique o esquema. No painel **Gerenciador de metadados do banco de dados SQL do Azure** , clique com o botão direito do mouse no banco de dados e selecione **sincronizar com Banco de dados**. Essa ação publica o esquema do MySQL em seu banco de dados SQL.

   ![Captura de tela do painel "sincronizar com o banco de dados" para revisar o mapeamento do banco de dados.](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. Migre os dados. No painel **Gerenciador de metadados do MySQL** , clique com o botão direito do mouse no esquema do MySQL que você deseja migrar e selecione **migrar dados**. Como alternativa, você pode selecionar a guia **migrar dados** no canto superior direito.

   Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dado, expanda **tabelas** e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção.

   ![Captura de tela do comando "migrar dados" no painel "Gerenciador de metadados do MySQL".](./media/mysql-to-sql-database-guide/migrate-data.png)

1. Depois que a migração for concluída, exiba o **relatório de migração de dados**.
   
   ![Captura de tela do relatório de migração de dados.](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. Conecte-se ao banco de dados SQL usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração revisando os data e o esquema.

   ![Captura de tela de SQL Server Management Studio.](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

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

## <a name="migration-assets"></a>Ativos de migração

Para obter mais assistência com a conclusão deste cenário de migração, consulte o recurso a seguir. Ele foi desenvolvido para dar suporte a um envolvimento no mundo real do projeto de migração.

| Título | Descrição |
| --- | --- |
| [Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Fornece plataformas de destino "melhor ajuste" sugeridas, preparação para a nuvem e níveis de correção de aplicativo/banco de dados para cargas de trabalho especificadas. Ele oferece um cálculo simples, com um clique e geração de relatórios que ajuda a acelerar avaliações de grandes imóveis fornecendo um processo de decisão de plataforma de destino automatizado e uniforme. |

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.

## <a name="next-steps"></a>Próximas etapas 

- Para ajudar a estimar a economia de custos que você pode obter migrando suas cargas de trabalho para o Azure, consulte a [calculadora de custo total de Propriedade do Azure](https://aka.ms/azure-tco).

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos e tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para obter outros guias de migração, consulte [Guia de migração de banco de dados do Azure](https://datamigration.microsoft.com/). 

- Para vídeos de migração, consulte [visão geral da jornada de migração e ferramentas e serviços recomendados de migração e avaliação](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).
