---
title: 'Instância Gerenciada do DB2 para o SQL do Azure: guia de migração'
description: Este guia ensina a migrar seus bancos de dados DB2 para o Azure SQL Instância Gerenciada usando o Assistente de Migração do SQL Server para DB2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: cb6a20d91458d2e4a6a60ced519531bd3c4356c5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642395"
---
# <a name="migration-guide-db2-to-azure-sql-managed-instance"></a>Guia de migração: Instância Gerenciada do DB2 para o SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Este guia ensina a migrar seus bancos de dados DB2 para o Azure SQL Instância Gerenciada usando o Assistente de Migração do SQL Server para DB2. 

Para obter outros guias de migração, confira [Migração de banco de dados](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar seu banco de dados DB2 para o SQL Instância Gerenciada, você precisa:

- para verificar se [há suporte](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites)para o ambiente de origem.
- para baixar o [Assistente de migração do SQL Server (SSMA) para DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- um Instância Gerenciada de destino [do Azure SQL](../../managed-instance/instance-create-quickstart.md).
- Conectividade e permissões suficientes para acessar a origem e o destino. 



## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração. 

### <a name="assess-and-convert"></a>Avaliar e converter



Crie uma avaliação usando o SSMA (Assistente de Migração do SQL Server). 

Para criar uma avaliação, siga estas etapas:

1. Abra o Assistente de Migração do SQL Server (SSMA) para DB2. 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar seu projeto e, em seguida, selecione Azure SQL Instância Gerenciada como o destino de migração no menu suspenso. Selecione **OK**:

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Forneça os detalhes do projeto e selecione OK para salvar.":::


1. Insira os valores para os detalhes de conexão do DB2 na caixa de diálogo **conectar ao DB2** :

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Conectar-se à sua instância do DB2":::


1. Clique com o botão direito do mouse no esquema do DB2 que você deseja migrar e escolha **criar relatório**. Isso vai gerar um relatório HTML. Como alternativa, você pode escolher **criar relatório** na barra de navegação depois de selecionar o esquema:

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Clicar com o botão direito do mouse no esquema e escolher criar relatório":::

1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos DB2 e o esforço necessário para executar conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Examinar o relatório para identificar erros ou avisos":::


### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas: 

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **mapeamentos de tipo** :

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Selecionar o esquema e o mapeamento de tipo":::

1. Você pode alterar o mapeamento de tipo para cada tabela selecionando a tabela no **Gerenciador de metadados do DB2**. 

### <a name="schema-conversion"></a>Conversão de esquema 

Para converter o esquema, siga estas etapas:

1. (Opcional) Adicione consultas dinâmicas ou ad hoc a instruções. Clique com o botão direito do mouse no nó e escolha **Adicionar instruções**. 
1. Selecione **conectar-se ao Azure SQL instância gerenciada**. 
    1. Insira os detalhes de conexão para se conectar ao seu Instância Gerenciada SQL do Azure. 
    1. Escolha o banco de dados de destino na lista suspensa ou forneça um novo nome, caso em que um banco de dados será criado no servidor de destino. 
    1. Forneça detalhes de autenticação. 
    1. Selecione **conectar**:

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Preencher os detalhes para se conectar ao SQL Server":::


1. Clique com o botão direito do mouse no esquema e escolha **Converter esquema**. Como alternativa, você pode escolher **converter esquema** na barra de navegação superior depois de selecionar o esquema:

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Clicar com o botão direito do mouse no esquema e escolher converter esquema":::

1. Após a conclusão da conversão, compare e revise a estrutura do esquema para identificar possíveis problemas e solucioná-los com base nas recomendações:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Comparar e examinar a estrutura do esquema para identificar possíveis problemas e solucioná-los com base nas recomendações.":::

1. Selecione **Examinar resultados** no painel Saída e examine os erros no painel **Lista de erros**. 
1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. Isso lhe dá a oportunidade de avaliar os esquemas de origem e de destino offline e executar a correção antes de publicar o esquema no SQL Instância Gerenciada.


## <a name="migrate"></a>Migrar

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração.

Para publicar o esquema e migrar seus dados, siga estas etapas:

1. Publicar o esquema: clique com o botão direito do mouse no banco de dados do nó **bancos** de dados no **Gerenciador de metadados do SQL instância gerenciada do Azure** e escolha **sincronizar com o banco de dados**:

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Clicar com o botão direito do mouse no banco de dados e escolher sincronizar com o banco de dados":::

1. Migrar os dados: clique com o botão direito do mouse no objeto ou banco de dado que você deseja migrar no **Gerenciador de metadados do DB2** e escolha **migrar dados**. Como alternativa, você pode selecionar **Migrar Dados** na barra de navegação de linha superior. Para migrar dados para um banco de dado inteiro, marque a caixa de seleção ao lado do nome do banco de dados. Para migrar dados de tabelas individuais, expanda o banco de dados, expanda Tabelas e marque a caixa de seleção ao lado da tabela. Para omitir dados de tabelas individuais, desmarque a caixa de seleção:

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Clicar com o botão direito do mouse no esquema e escolher migrar dados":::

1. Forneça detalhes de conexão para DB2 e SQL Instância Gerenciada. 
1. Após a conclusão da migração, exiba o **Relatório da Migração de Dados**:  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Examinar o relatório de migração de dados":::

1. Conecte-se ao seu Instância Gerenciada SQL do Azure usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e valide a migração examinando os dados e o esquema:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Comparar o esquema no SSMS":::

## <a name="post-migration"></a>Após a migração 

Depois de concluir com êxito o estágio de migração, você precisa passar por uma série de tarefas de pós-migração para garantir que tudo esteja funcionando da maneira mais estável e eficiente possível.

### <a name="remediate-applications"></a>Corrigir aplicativos 

Depois que os dados são migrados para o ambiente de destino, todos os aplicativos que antes consumiam a origem, precisam começar a consumir o destino. Em alguns casos isso exigirá alterações nos aplicativos.


### <a name="perform-tests"></a>Executar testes

A abordagem de teste para a migração de banco de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração do banco de dados, você precisa usar consultas SQL. Você deve criar as consultas de validação para executar nos bancos de dados de origem e de destino. Suas consultas de validação devem abranger o escopo que você definiu.
1. **Configurar ambiente de teste**: O ambiente de teste deve conter uma cópia do banco de dados de origem e do banco de dados de destino. Lembre-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Execute os testes de validação na origem e no destino e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: Execute o teste de desempenho na origem e no destino e, em seguida, analise e compare os resultados.


## <a name="leverage-advanced-features"></a>Aproveite os recursos avançados 

Não se esqueça de aproveitar os recursos avançados baseados em nuvem oferecidos pelo Azure SQL Instância Gerenciada, como [alta disponibilidade interna](../../database/high-availability-sla.md), [detecção de ameaças](../../database/azure-defender-for-sql.md)e [monitoramento e ajuste da carga de trabalho](../../database/monitor-tune-overview.md). 


Alguns recursos SQL Server só estarão disponíveis quando o [nível de compatibilidade do banco de dados](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) for alterado para o nível de compatibilidade mais recente (150). 

## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional, consulte os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real:

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.|
|[Pacote de avaliação e descoberta de ativos de dados do DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Depois de executar o script SQL em um banco de dados, você pode exportar os resultados para um arquivo no sistema de arquivos. Há compatibilidade com vários formatos de arquivo, incluindo *.csv, para que você possa capturar os resultados em ferramentas externas, como planilhas. Esse método pode ser útil caso você queira compartilhar facilmente os resultados com equipes que não têm a área de trabalho instalada.|
|[Artefatos e scripts de inventário do IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20Db2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Este ativo inclui uma consulta SQL que atinge as tabelas do sistema IBM DB2 LUW versão 11,1 e fornece uma contagem de objetos por esquema e tipo de objeto, uma estimativa aproximada de ' dados brutos ' em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em um formato CSV.|
|[Escala pura do DB2 LUW no Azure – guia de instalação](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Db2%20PureScale%20on%20Azure.pdf)|Este guia serve como ponto de partida para um plano de implementação do DB2. Embora os requisitos de negócios sejam diferentes, o mesmo padrão básico se aplica. Esse padrão de arquitetura também pode ser usado para aplicativos OLAP no Azure.|

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [serviços e ferramentas para a migração de dados](../../../dms/dms-tools-matrix.md).

- Para saber mais sobre o Azure SQL Instância Gerenciada consulte:
   - [Uma visão geral do SQL Instância Gerenciada](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Calculadora de custo total de Propriedade do Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre a estrutura e o ciclo de adoção para migrações na nuvem, consulte
   -  [Cloud Adoption Framework para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Práticas recomendadas para o custo e o dimensionamento de cargas de trabalho migrar para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso do aplicativo, consulte [Kit de ferramentas de migração de acesso a dados (versão prévia)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para obter detalhes sobre como executar testes de camada A/B de acesso A dados, consulte [Assistente para experimentos de banco de dados](/sql/dea/database-experimentation-assistant-overview).