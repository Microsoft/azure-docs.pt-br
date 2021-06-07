---
title: 'Db2 para Banco de Dados SQL do Azure: guia de migração'
description: Este guia ensina a migrar bancos de dados IBM Db2 para o Banco de Dados SQL do Azure usando o SSMA (Assistente de Migração do SQL Server) para Db2.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5dd6735b5ef17f97de1d2272bd98f6b87b0bc84b
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553066"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-database"></a>Guia de migração: IBM Db2 para Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Este guia ensina a migrar bancos de dados IBM Db2 para o Banco de Dados SQL do Azure usando o Assistente de Migração do SQL Server para Db2. 

Para obter outros guias de migração, confira [Guias de Migração do Banco de Dados do Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar o banco de dados Db2 para o Banco de Dados SQL, é necessário:

- verificar se o [ambiente de origem é compatível](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites);
- baixar o [SSMA (Assistente de Migração do SQL Server) para Db2](https://www.microsoft.com/download/details.aspx?id=54254);
- um banco de dados de destino no [Banco de Dados SQL do Azure](../../database/single-database-create-quickstart.md);
- conectividade e permissões suficientes para acessar a origem e o destino. 

## <a name="pre-migration"></a>Pré-migração

Depois de cumprir esses pré-requisitos, tudo estará pronto para descobrir a topologia do ambiente e avaliar a viabilidade da migração. 

### <a name="assess-and-convert"></a>Avaliar e converter

Use o SSMA para Db2 para analisar os objetos e os dados de banco de dados, bem como avaliar os bancos de dados para migração. 

Para criar uma avaliação, siga estas etapas:

1. Abra o [SSMA para Db2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Clique em **Arquivo** > **Novo Projeto**. 
1. Escolha um nome para o projeto e um local para salvá-lo. Em seguida, selecione o Banco de Dados SQL do Azure como o destino da migração na lista suspensa e clique em **OK**.

   :::image type="content" source="media/db2-to-sql-database-guide/new-project.png" alt-text="Captura de tela mostra os detalhes do projeto a serem especificados.":::


1. Em **Conectar-se ao Db2**, insira os valores das informações de conexão do Db2. 

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-db2.png" alt-text="Captura de tela mostra opções para se conectar à instância do Db2.":::


1. Clique com o botão direito do mouse no esquema do Db2 que você deseja migrar e escolha **Criar relatório**. Isso vai gerar um relatório HTML. Como alternativa, você pode escolher **Criar relatório** na barra de navegação depois de selecionar o esquema.

   :::image type="content" source="media/db2-to-sql-database-guide/create-report.png" alt-text="Captura de tela mostra como criar um relatório.":::

1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Também é possível abrir o relatório no Excel para ver um inventário de objetos do Db2 e o esforço necessário para executar as conversões de esquema. O local padrão para o relatório está na pasta de relatórios em *SSMAProjects*.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-database-guide/report.png" alt-text="Captura de tela do relatório que você analisa para identificar erros ou avisos.":::


### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas: 

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **Mapeamentos de tipo**.

   :::image type="content" source="media/db2-to-sql-database-guide/type-mapping.png" alt-text="Captura de tela mostra a seleção do esquema e o mapeamento de tipo.":::

1. Selecione a tabela no **Gerenciador de Metadados do Db2** para alterar o tipo de mapeamento dela. 

### <a name="convert-schema"></a>Converter esquema

Para converter o esquema, siga estas etapas:

1. (Opcional) Adicione consultas dinâmicas ou ad hoc a instruções. Clique com o botão direito do mouse no nó e escolha **Adicionar instruções**. 
1. Clique em **Conectar-se ao Banco de Dados SQL do Azure**. 
    1. Digite as informações necessárias para conectar o banco de dados ao Banco de Dados SQL do Azure.
    1. Escolha o Banco de Dados SQL de destino na lista suspensa. Outra opção é inserir um novo nome. Nesse caso, um banco de dados será criado no servidor de destino. 
    1. Insira as informações de autenticação. 
    1. Selecione **Conectar**.

   :::image type="content" source="media/db2-to-sql-database-guide/connect-to-sql-database.png" alt-text="Captura de tela mostra as informações necessárias para se conectar ao servidor lógico no Azure.":::


1. Clique com o botão direito do mouse no esquema e escolha **Converter Esquema**. Como alternativa, você pode escolher **Converter esquema** na barra de navegação superior depois de selecionar o esquema.

   :::image type="content" source="media/db2-to-sql-database-guide/convert-schema.png" alt-text="Captura de tela mostra a seleção do esquema e a conversão dele.":::

1. Após a conversão, compare e analise a estrutura do esquema para identificar possíveis problemas. Solucione os problemas de acordo com as recomendações.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-review-schema-structure.png" alt-text="Captura de tela mostra a comparação e a análise da estrutura do esquema para identificar possíveis problemas.":::

1. No painel **Saída**, select **Examinar resultados**. No painel **Lista de erros**, analise os erros. 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. No menu **Arquivo**, clique em **Salvar Projeto**. Assim, você tem a oportunidade de avaliar os esquemas de origem e de destino offline e fazer a correção antes de publicar o esquema no Banco de Dados SQL.

## <a name="migrate"></a>Migrações

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração.

Para publicar o esquema e migrar seus dados, siga estas etapas:

1. Publique o esquema. No **Gerenciador de Metadados do Banco de Dados SQL do Azure**, no nó **Banco de Dados**, clique com o botão direito do mouse no banco de dados. Em seguida, clique em **Sincronizar com Banco de Dados**.

   :::image type="content" source="media/db2-to-sql-database-guide/synchronize-with-database.png" alt-text="Captura de tela mostra a opção de sincronizar com o banco de dados.":::

1. Migre os dados. Clique com o botão direito do mouse no objeto ou banco de dados que você deseja migrar no **Gerenciador de Metadados do Db2** e escolha **Migrar dados**. Outra opção é clicar em **Migrar Dados** na barra de navegação. Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dados, expanda **Tabelas** e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção.

   :::image type="content" source="media/db2-to-sql-database-guide/migrate-data.png" alt-text="Captura de tela mostra a seleção do esquema e a como fazer a migração de dados.":::

1. Insira as informações de conexão do Db2 e do Banco de Dados SQL do Azure. 
1. Após a migração, veja o **Relatório de Migração de Dados**.  

   :::image type="content" source="media/db2-to-sql-database-guide/data-migration-report.png" alt-text="Captura de tela mostra onde analisar o relatório de migração de dados.":::

1. Conecte-se ao banco de dados no Banco de Dados SQL do Azure usando o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Analise os dados e o esquema para validar a migração.

   :::image type="content" source="media/db2-to-sql-database-guide/compare-schema-in-ssms.png" alt-text="Captura de tela mostra a comparação do esquema no SQL Server Management Studio.":::

## <a name="post-migration"></a>Pós-migração 

Após a migração, é necessário realizar uma série de tarefas para garantir que tudo está funcionando da maneira mais estável e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos 

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.

### <a name="perform-tests"></a>Executar testes

Durante os testes, é preciso realizar as seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.
1. **Configurar ambiente de teste**: o ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: execute os testes de desempenho na origem e no destino, analise e compare os resultados subsequentes.

## <a name="advanced-features"></a>Recursos avançados 

Aproveite os recursos avançados baseados em nuvem oferecidos pelo Banco de Dados SQL, como [alta disponibilidade interna](../../database/high-availability-sla.md), [detecção de ameaças](../../database/azure-defender-for-sql.md) e [monitoramento e ajuste da carga de trabalho](../../database/monitor-tune-overview.md). 

Alguns recursos do SQL Server só ficam disponíveis quando o [nível de compatibilidade do banco de dados](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) é alterado para o nível de compatibilidade mais recente. 

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional, consulte os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real:

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.|
|[Pacote de avaliação e descoberta de ativos de dados do Db2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Depois de executar o script SQL em um banco de dados, você pode exportar os resultados para um arquivo no sistema de arquivos. Há compatibilidade com vários formatos de arquivo, incluindo *.csv, para que você possa capturar os resultados em ferramentas externas, como planilhas. Esse método pode ser útil caso você queira compartilhar facilmente os resultados com equipes que não têm a área de trabalho instalada.|
|[Artefatos e scripts de inventário do IBM Db2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Este ativo inclui uma consulta SQL que alcança as tabelas do sistema do IBM Db2 LUW versão 11.1 e oferece uma contagem de objetos por esquema e por tipo de objeto, uma estimativa aproximada de "dados brutos" em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em formato CSV.|
|[Escala pura do Db2 LUW no Azure – guia de instalação](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|Este guia serve como ponto de partida para um plano de implementação do Db2. Embora os requisitos de negócios sejam diferentes, o mesmo padrão básico é aplicado. Esse padrão de arquitetura também pode ser usado para aplicativos OLAP no Azure.|

A equipe de engenharia de dados do SQL desenvolveu esses recursos. A principal responsabilidade dessa equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Microsoft Azure.



## <a name="next-steps"></a>Próximas etapas

- Para ter acesse a serviços e ferramentas de terceiros e da Microsoft que ajudam em diversos cenários de migração de dados e banco de dados, confira [Serviços e ferramentas de migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Banco de Dados SQL do Azure, confira:
   - [Visão geral do Banco de Dados SQL](../../database/sql-database-paas-overview.md)
   - [Calculadora de custo total de propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, confira:
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Melhores práticas para estimar os custos e dimensionar as cargas de trabalho migradas para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, confira [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Para ver detalhes de como executar testes A/B na camada de acesso a dados, confira [Assistente para Experimentos de Banco de Dados](/sql/dea/database-experimentation-assistant-overview).
