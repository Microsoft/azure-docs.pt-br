---
title: 'Oracle para SQL Server na VM do Azure: guia de migração'
description: Este guia ensina a migrar seus esquemas Oracle para SQL Server em VMs do Azure usando Assistente de Migração do SQL Server para Oracle.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 8f034492568a7525f8f75f5f2add1a732c3ad896
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644281"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Guia de migração: Oracle para SQL Server na VM do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina a migrar seus esquemas Oracle para SQL Server na VM do Azure usando Assistente de Migração do SQL Server para Oracle. 

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar seu esquema Oracle para SQL Server na VM do Azure, você precisa de:

- Verificar se seu ambiente de origem é compatível.
- Baixar o [SSMA (Assistente de Migração do SQL Server) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- Uma [VM de SQL Server](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)de destino.
- As [permissões necessárias para o SSMA para Oracle](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) e para o [provedor](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- Conectividade e permissões suficientes para acessar a origem e o destino. 


## <a name="pre-migration"></a>Pré-migração

Ao se preparar para migrar para a nuvem, verifique se o seu ambiente de origem tem suporte e se você resolveu todos os pré-requisitos. Isso ajudará a garantir uma migração eficiente e bem-sucedida.

Essa parte do processo envolve a execução de um inventário dos bancos de dados que você precisa migrar, a avaliação desses bancos de dados quanto a possíveis problemas de migração ou bloqueadores e, em seguida, a resolução dos itens que você possa ter descoberto. 

### <a name="discover"></a>Descobrir

Use o [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) para identificar as fontes de dados existentes e os detalhes sobre os recursos que estão sendo usados pela sua empresa para obter uma compreensão melhor da migração e planejá-la. Esse processo envolve a verificação da rede para identificar todas as instâncias do Oracle da sua organização junto com a versão e os recursos em uso.

Para usar o MAP Toolkit para executar uma verificação de inventário, siga estas etapas: 

1. Abra o [MAP Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Selecione **criar/selecionar banco de dados**:

   ![Selecionar banco de dados](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Selecione **criar um banco de dados de inventário**, insira um nome para o novo banco de dados de inventário que você está criando, forneça uma breve descrição e, em seguida, selecione **OK**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Criar um banco de dados de inventário":::

1. Selecione **coletar dados de inventário** para abrir o **Assistente de inventário e avaliação**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Coletar dados de inventário":::

1. No **Assistente de inventário e avaliação**, escolha **Oracle** e, em seguida, selecione **Avançar**:

   ![Escolha o Oracle](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Escolha a opção de pesquisa de computador mais adequada às suas necessidades de negócios e ao seu ambiente e, em seguida, selecione **Avançar**: 

   ![Escolha a opção de pesquisa do computador mais adequada às suas necessidades de negócios](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Insira as credenciais ou crie novas credenciais para os sistemas que você deseja explorar e, em seguida, selecione **Avançar**:

    ![Inserir as credenciais](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Defina a ordem das credenciais e, em seguida, selecione **Avançar**:

   ![Definir ordem de credencial](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Especifique as credenciais para cada computador que você deseja descobrir. Você pode usar credenciais exclusivas para cada computador/máquina ou pode optar por usar a lista **todas as credenciais do computador** :


   ![Especifique as credenciais para cada computador que você deseja descobrir](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Verifique seu resumo de seleção e, em seguida, selecione **concluir**:

   ![Examinar o resumo](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. Depois que a verificação for concluída, exiba o relatório resumido da **Coleta de dados**. A verificação pode levar alguns minutos e depende do número de bancos de dados. Selecione **fechar** quando terminar:

   ![Relatório de resumo da coleção](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Selecione **Opções** para gerar um relatório sobre os detalhes da avaliação e do banco de dados do Oracle. Selecione as duas opções (uma a uma) para gerar o relatório.


### <a name="assess"></a>Avaliar

Depois de identificar as fontes de dados, use o [SSMA (Assistente de Migração do SQL Server) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) para avaliar as instâncias do Oracle que fazem a migração para a VM do SQL Server para que você compreenda as lacunas entre as duas. Usando o assistente de migração, você pode examinar objetos e dados de banco de dados, avaliar bancos de dados para migração, migrar objetos de banco de dados para o SQL Server e migrar os dados para o SQL Server.

Para criar uma avaliação, siga estas etapas: 

1. Abra o [SSMA (Assistente de Migração do SQL Server) para Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o projeto e, em seguida, selecione um destino de migração do SQL Server na lista suspensa. Selecione **OK**:

   ![Novo Projeto](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Selecione **conectar-se ao Oracle**. Insira valores nos detalhes da conexão Oracle na caixa de diálogo **conectar ao Oracle** :

   ![Conectar-se ao Oracle](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Selecione os esquemas do Oracle que você deseja migrar: 

   ![Selecionar esquema Oracle](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. Clique com o botão direito do mouse no esquema Oracle que você deseja migrar no **Gerenciador de metadados Oracle** e escolha **criar relatório**. Isso vai gerar um relatório HTML. Como alternativa, você pode escolher **criar relatório** na barra de navegação depois de selecionar o banco de dados:

   ![Criar Relatório](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. No **Gerenciador de Metadados do Oracle**, selecione o esquema do Oracle e selecione **Criar Relatório** para gerar um relatório HTML com estatísticas de conversão e erros/avisos, se houver.
1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário dos objetos Oracle e o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects. 

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`
    
   ![Relatório de Conversão](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)

### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas: 

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **mapeamentos de tipo** :

   ![Mapeamentos de Tipo](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Você pode alterar o mapeamento de tipo de cada tabela selecionando a tabela no **Gerenciador de Metadados do Oracle**. 

### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estas etapas: 

1. (Opcional) Para converter consultas dinâmicas ou ad hoc, clique com o botão direito do mouse no nó e escolha **Adicionar instrução**.
1. Selecione **conectar-se a SQL Server** na barra de navegação de linha superior. 
     1. Insira os detalhes de conexão para seu SQL Server na VM do Azure. 
     1. Escolha o banco de dados de destino na lista suspensa ou forneça um novo nome, caso em que um banco de dados será criado no servidor de destino. 
     1. Forneça detalhes de autenticação. 
     1. Selecione **Conectar**. 

   ![Conectar-se ao SQL](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Clique com o botão direito do mouse no esquema Oracle no **Gerenciador de metadados Oracle** e escolha **converter esquema**. Como alternativa, você pode selecionar **converter esquema** na barra de navegação de linha superior:

   ![Converter esquema](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Após a conclusão da conversão, compare e revise os objetos convertidos para os objetos originais para identificar possíveis problemas e solucioná-los com base nas recomendações:

   ![Revisar recomendações](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Compare o texto Transact-SQL convertido com os procedimentos armazenados originais e examine as recomendações: 

   ![Examinar o código de recomendações](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   Você pode salvar o projeto localmente para realizar um exercício de correção de esquema offline. Você pode fazer isso selecionando **Salvar Projeto** no menu **Arquivo**. Isso dá a você a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no SQL Server.

1. Selecione **Examinar resultados** no painel Saída e examine os erros no painel **Lista de erros**. 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema para SQL Server na VM do Azure.


## <a name="migrate"></a>Migrações

Depois de implementar os pré-requisitos necessários e concluir as tarefas associadas à fase de **pré-migração**, você já poderá executar a migração de dados e de esquema. A migração envolve duas etapas: publicação do esquema e migração dos dados. 


Para publicar o esquema e migrar os dados, siga estas etapas: 

1. Publicar o esquema: clique com o botão direito do mouse no **Gerenciador de metadados SQL Server**  e escolha **sincronizar com Banco de dados**. Esta ação publica o esquema Oracle para SQL Server na VM do Azure:

   ![Sincronizar com Banco de dados](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Examine o mapeamento entre seu projeto de origem e seu destino:

   ![Examinar o status da sincronização](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Migrar os dados: clique com o botão direito do mouse no objeto ou no Database que você deseja migrar no **Gerenciador de metadados Oracle** e escolha **migrar dados**. Como alternativa, você pode selecionar **Migrar Dados** na barra de navegação de linha superior. Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dados, expanda Tabelas e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção:

   ![Migrar dados](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Forneça detalhes de conexão para Oracle e SQL Server na VM do Azure na caixa de diálogo.
1. Após a conclusão da migração, exiba o **Relatório da Migração de Dados**:  

    ![Relatório de migração de dados](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Conecte-se ao seu SQL Server na instância de VM do Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração examinando os dados e o esquema:

   ![Validar no SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)


Além de usar o SSMA, você também pode usar SSIS (SQL Server Integration Services) para migrar os dados. Para obter mais informações, consulte: 
- O artigo [Introdução ao SQL Server Integration Services](//sql/integration-services/sql-server-integration-services).
- O white paper [SQL Server Integration Services: SSIS para o Azure e Movimentação de Dados Híbridos](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>Pós-migração 

Depois de concluir com êxito o estágio de **migração**, você precisará passar por uma série de tarefas de pós-migração para garantir que tudo esteja funcionando da maneira mais estável e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

O [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) é uma extensão para Visual Studio Code que permite analisar o código-fonte Java e detectar consultas e chamadas à API de acesso a dados, fornecendo uma visão de painel único do que precisa ser resolvido para dar suporte ao novo back-end do banco de dados. Para saber mais, confira o blog [Migrar nosso aplicativo Java do Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste na execução das seguintes atividades:

1. **Desenvolver testes de validação**. Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.

2. **Configurar ambiente de teste**. O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.

3. **Executar testes de validação**. Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.

4. **Executar testes de desempenho**. Execute o teste de desempenho na origem e no destino e, em seguida, analise e compare os resultados.

### <a name="optimize"></a>Otimizar

A fase de pós-migração é crucial para reconciliar problemas de precisão de dados e verificar a integridade dos dados, além de resolver problemas de desempenho com a carga de trabalho.

> [!Note]
> Para obter detalhes adicionais sobre esses problemas e etapas específicas para atenuá-los, confira o [Guia de validação e otimização após a migração](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional com a conclusão desse cenário de migração, confira os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real.

| **Título/link**                                                                                                                                          | **Descrição**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e Ferramenta de Avaliação de Carga de Trabalho de Dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam muito a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.                                                          |
| [Artefatos de script de inventário do Oracle](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Esse ativo inclui uma consulta PL/SQL que afeta as tabelas do sistema Oracle e fornece uma contagem de objetos por tipo de esquema, tipo de objeto e status. Ele também fornece uma estimativa aproximada de "dados brutos" em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em um formato CSV.                                                                                                               |
| [Automatizar a Coleta e Fusão de Avaliação da Oracle do SSMA](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Esse conjunto de recursos usa um arquivo .csv como entrada (sources.csv nas pastas do projeto) para produzir os arquivos xml necessários para executar a avaliação do SSMA no modo de console. O source.csv é fornecido pelo cliente com base em um inventário de instâncias existentes do Oracle. Os arquivos de saída são AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml e VariableValueFile.xml.|
| [Erros comuns do SSMA para Oracle e como corrigi-los](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Com o Oracle, você pode atribuir uma condição não escalar na cláusula WHERE. No entanto, SQL Server não dá suporte a esse tipo de condição. Como resultado, o SSMA (Assistente de Migração do SQL Server) para Oracle não converte consultas com uma condição não escalar na cláusula WHERE, em vez de gerar um erro O2SS0001. Este white paper fornece mais detalhes sobre o problema e as maneiras de resolvê-lo.          |
| [Manual de migração do Oracle para SQL Server](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Este documento se concentra nas tarefas associadas à migração de um esquema Oracle para a versão mais recente do SQL Server. Se a migração exigir alterações nos recursos/funcionalidades, o possível impacto de cada alteração nos aplicativos que usam o banco de dados precisa ser considerado com cuidado.                                                     |

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.

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