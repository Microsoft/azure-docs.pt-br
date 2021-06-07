---
title: 'Acesso ao banco de dados SQL do Azure: guia de migração'
description: Neste guia, você aprende a migrar seus bancos de dados do Microsoft Access para um banco de dados SQL do Azure usando o Assistente de Migração do SQL Server para acesso (SSMA para Access).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f9fa2426e371ab9fd99e88979cbcbbb34adb00d6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643595"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Guia de migração: acesso ao banco de dados SQL do Azure

Neste guia, você aprende a migrar seu banco de dados do Microsoft Access para um banco de dados SQL do Azure usando Assistente de Migração do SQL Server para acesso (SSMA para Access).

Para obter outros guias de migração, consulte [Guia de migração de banco de dados do Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a migrar seu banco de dados do Access para um banco de dados SQL, faça o seguinte:

- Verifique se o seu ambiente de origem tem suporte. 
- Baixe e instale o [Assistente de migração do SQL Server para acesso](https://www.microsoft.com/download/details.aspx?id=54255).
- Verifique se você tem conectividade e permissões suficientes para acessar a origem e o destino.

## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da sua migração.


### <a name="assess"></a>Avaliar 

Use o SSMA para obter acesso para revisar objetos e dados de banco e para a migração. 

Para criar uma avaliação, faça o seguinte: 

1. Abra o [SSMA para acesso](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Selecione **arquivo** e, em seguida, selecione **novo projeto**. 
1. Forneça um nome de projeto e um local para seu projeto e, na lista suspensa, selecione **banco de dados SQL do Azure** como o destino de migração. 
1. Selecione **OK**. 

   ![Captura de tela do painel "novo projeto" para inserir o nome e o local do projeto de migração.](./media/access-to-sql-database-guide/new-project.png)

1. Selecione **Adicionar bancos de dados** e, em seguida, selecione os bancos de dados a serem adicionados ao novo projeto. 

   ![Captura de tela da guia "Adicionar bancos de dados" no SSMA para Access.](./media/access-to-sql-database-guide/add-databases.png)

1. No painel **Access Metadata Explorer** , clique com o botão direito do mouse em um banco de dados e selecione **criar relatório**. Como alternativa, você pode selecionar a guia **criar relatório** no canto superior direito.

   ![Captura de tela do comando "criar relatório" no Gerenciador de metadados do Access.](./media/access-to-sql-database-guide/create-report.png)

1. Examine o relatório HTML para entender as estatísticas de conversão e quaisquer erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos do Access e entender o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects. Por exemplo:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Captura de tela de um exemplo de avaliação de relatório de banco de dados no SSMA.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Validar os tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base em seus requisitos, se necessário. Para fazer isso:

1. No SSMA para Access, selecione **ferramentas** e, em seguida, selecione **configurações do projeto**. 
1. Selecione a guia **mapeamento de tipo** . 

   ![Captura de tela do painel "mapeamento de tipo" no SSMA para Access.](./media/access-to-sql-database-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando o nome da tabela no painel **Gerenciador de metadados do Access** .


### <a name="convert-the-schema"></a>Converter o esquema

Para converter objetos de banco de dados, faça o seguinte: 

1. Selecione a guia **conectar-se ao banco de dados SQL do Azure** e, em seguida, faça o seguinte:

   a. Insira os detalhes para se conectar ao banco de dados SQL.  
   b. Na lista suspensa, selecione o banco de dados SQL de destino. Ou você pode inserir um novo nome, caso em que um banco de dados será criado no servidor de destino.  
   c. Forneça detalhes de autenticação.   
   d. Selecione **Conectar**.

   ![Captura de tela do painel "conectar ao banco de dados SQL do Azure" para inserir detalhes de conexão.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. No painel do **Gerenciador de metadados do Access** , clique com o botão direito do mouse no banco de dados e selecione **converter esquema**. Como alternativa, você pode selecionar seu banco de dados e, em seguida, selecionar a guia **converter esquema** .

   ![Captura de tela do comando "converter esquema" no painel "Access Metadata Explorer".](./media/access-to-sql-database-guide/convert-schema.png)

1. Depois que a conversão for concluída, compare os objetos convertidos com os objetos originais para identificar possíveis problemas e resolva os problemas com base nas recomendações.

   ![Captura de tela mostrando uma comparação dos objetos convertidos para os objetos de origem.](./media/access-to-sql-database-guide/table-comparison.png)

    Compare o texto Transact-SQL convertido com o código original e examine as recomendações.

   ![Captura de tela mostrando uma comparação de consultas convertidas para o código-fonte.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Adicional Para converter um objeto individual, clique com o botão direito do mouse no objeto e selecione **converter esquema**. Os objetos convertidos aparecem em negrito no **Gerenciador de metadados do Access**: 

   ![Captura de tela mostrando que os objetos no Gerenciador de metadados do Access são convertidos.](./media/access-to-sql-database-guide/converted-items.png)
 
1. No painel **saída** , selecione o ícone **examinar resultados** e examine os erros no painel lista de **erros** . 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Para fazer isso, selecione **arquivo**  >  **salvar projeto**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicá-los no banco de dados SQL.

## <a name="migrate-the-databases"></a>Migrar os bancos de dados

Depois de avaliar seus bancos de dados e solucionar quaisquer discrepâncias, você pode executar o processo de migração. A migração de dados é uma operação de carregamento em massa que move linhas de dados para um banco de dado SQL do Azure em transações. O número de linhas a serem carregadas em seu banco de dados SQL em cada transação é definido nas configurações do projeto.

Para publicar o esquema e migrar os dados usando o SSMA para Access, faça o seguinte: 

1. Se você ainda não tiver feito isso, selecione **conectar-se ao banco de dados SQL do Azure** e forneça detalhes de conexão. 

1. Publique o esquema. No painel **Gerenciador de metadados do banco de dados SQL do Azure** , clique com o botão direito do mouse no banco de dados com o qual você está trabalhando e selecione **sincronizar com Banco de dados**. Essa ação publica o esquema MySQL no banco de dados SQL.

1. No painel **sincronizar com o banco de dados** , examine o mapeamento entre seu projeto de origem e seu destino:

   ![Captura de tela do painel "sincronizar com o banco de dados" para revisar a sincronização com o banco de dados.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. No painel **Access Metadata Explorer** , marque as caixas de seleção ao lado dos itens que você deseja migrar. Para migrar todo o banco de dados, marque a caixa de seleção ao lado do banco de dados. 

1. Migre os dados. Clique com o botão direito do mouse no banco de dados ou objeto que você deseja migrar e, em seguida, selecione **migrar data**. Como alternativa, você pode selecionar a guia **migrar dados** no canto superior direito.  

   Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dado, expanda **tabelas** e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção.

    ![Captura de tela do comando "migrar dados" no painel "Access Metadata Explorer".](./media/access-to-sql-database-guide/migrate-data.png)

1. Após a conclusão da migração, exiba o **relatório de migração de dados**.  

    ![Captura de tela do painel "migrar relatório de dados" mostrando um relatório de exemplo para revisão.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Conecte-se ao banco de dados SQL do Azure usando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)e valide a migração examinando o esquema e os esquemas.

   ![Captura de tela de SQL Server Management Studio pesquisador de objetos para validar sua migração no SSMA.](./media/access-to-sql-database-guide/validate-data.png)

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

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos e tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Banco de Dados SQL do Azure, confira:
   - [Uma visão geral do banco de dados SQL](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, confira:
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para custos e dimensionamento de cargas de trabalho para migração para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para obter informações sobre como executar testes de camada A/B de acesso A dados, consulte [visão geral do assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).
