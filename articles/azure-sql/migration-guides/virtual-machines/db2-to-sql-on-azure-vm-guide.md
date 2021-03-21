---
title: 'DB2 para SQL Server em VMs do Azure: guia de migração'
description: Este guia ensina a migrar seu banco de dados DB2 para SQL Server em VMs do Azure usando o Assistente de Migração do SQL Server para DB2.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: aadfd2d96e2ed610ee1e3eaaec9276212e912b0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562222"
---
# <a name="migration-guide-db2-to-sql-server-on-azure-vms"></a>Guia de migração: DB2 para SQL Server em VMs do Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Este guia de migração ensina a migrar seus bancos de dados de usuário do DB2 para o SQL Server em VMs do Azure usando o Assistente de Migração do SQL Server para DB2. 

Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 


## <a name="prerequisites"></a>Pré-requisitos

Para migrar seu banco de dados DB2 para o SQL Server, você precisará:

- verificar se seu ambiente de origem é compatível.
- [SSMA (Assistente de Migração do SQL Server) para DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- [Conectividade](../../virtual-machines/windows/ways-to-connect-to-sql.md) entre seu ambiente de origem e sua VM SQL Server no Azure. 



## <a name="pre-migration"></a>Pré-migração

Depois de atender aos pré-requisitos, você estará pronto para descobrir a topologia do seu ambiente e avaliar a viabilidade da migração. 

### <a name="assess"></a>Avaliar 

Crie uma avaliação usando o SSMA (Assistente de Migração do SQL Server). 

Para criar uma avaliação, siga estas etapas:

1. Abra o SSMA (Assistente de Migração do SQL Server) para DB2. 
1. Selecione **Arquivo** e, em seguida, escolha **Novo Projeto**. 
1. Forneça um nome de projeto, um local para salvar o projeto e, em seguida, selecione um destino de migração do SQL Server na lista suspensa. Selecione **OK**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="Forneça os detalhes do projeto e selecione OK para salvar.":::


1. Insira valores para os detalhes de conexão do DB2 na caixa de diálogo **Conectar ao DB2**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-db2.png" alt-text="Conectar-se à sua instância do DB2":::


1. Clique com o botão direito do mouse no esquema do DB2 que você deseja migrar e escolha **Criar relatório**. Isso vai gerar um relatório HTML. Como alternativa, você pode escolher **Criar relatório** na barra de navegação depois de selecionar o esquema. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Clicar com o botão direito do mouse no esquema e escolher criar relatório":::

1. Examine o relatório HTML para entender as estatísticas de conversão e outros erros ou avisos. Você também pode abrir o relatório no Excel para obter um inventário de objetos do DB2 e o esforço necessário para executar as conversões de esquema. O local padrão do relatório está na pasta de relatório em SSMAProjects.

   Por exemplo: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="Examinar o relatório para identificar erros ou avisos":::


### <a name="validate-data-types"></a>Validar tipos de dados

Valide os mapeamentos de tipo de dados padrão e altere-os com base nos requisitos, se necessário. Para fazer isso, siga estas etapas: 

1. Selecione **Ferramentas** no menu. 
1. Selecione **Configurações do Projeto**. 
1. Selecione a guia **Mapeamentos de tipo**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Selecionar o esquema e o mapeamento de tipo":::

1. Você pode alterar o mapeamento de tipo de cada tabela selecionando a tabela no **Gerenciador de metadados do DB2**. 

### <a name="convert-schema"></a>Converter esquema 

Para converter o esquema, siga estas etapas:

1. (Opcional) Adicione consultas dinâmicas ou ad hoc a instruções. Clique com o botão direito do mouse no nó e escolha **Adicionar instruções**. 
1. Selecione **Conectar ao SQL Server**. 
    1. Insira os detalhes de conexão para se conectar à sua instância do SQL Server na VM do Azure. 
    1. Opte por se conectar a um banco de dados existente no servidor de destino ou forneça um novo nome para criar um banco de dados no servidor de destino. 
    1. Selecione **Conectar**. 

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Conectar-se ao seu SQL Server na VM do Azure":::


1. Clique com o botão direito do mouse no esquema e escolha **Converter esquema**. Como alternativa, você pode escolher **Converter esquema** na barra de navegação superior depois de selecionar o esquema. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Clicar com o botão direito do mouse no esquema e escolher converter esquema":::

1. Após a conclusão da conversão, compare e examine a estrutura do esquema para identificar possíveis problemas e solucioná-los com base nas recomendações. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Comparar e examinar a estrutura do esquema para identificar possíveis problemas e solucioná-los com base nas recomendações.":::

1. Salve o projeto localmente para realizar um exercício de correção de esquema offline. Selecione **Salvar Projeto** no menu **Arquivo**. 


## <a name="migrate"></a>Migrar

Depois de concluir a avaliação de seus bancos de dados e resolver quaisquer discrepâncias, a próxima etapa é executar o processo de migração.

Para publicar o esquema e migrar seus dados, siga estas etapas:

1. Publicar o esquema: Clique com o botão direito do mouse no banco de dados do nó **Bancos de Dados** no **Gerenciador de Metadados do SQL Server** e escolha **Sincronizar com o Banco de Dados**.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Clicar com o botão direito do mouse no banco de dados e escolher sincronizar com o banco de dados":::

1. Migrar os dados: Clique com o botão direito do mouse no esquema do **Gerenciador de Metadados do DB2** e escolha **Migrar Dados**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Clicar com o botão direito do mouse no esquema e escolher migrar dados":::

1. Forneça os detalhes da conexão das instâncias do DB2 e do SQL Server. 
1. Veja o **Relatório de migração de dados**. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Examinar o relatório de migração de dados":::

1. Conecte-se à sua instância do SQL Server usando o SQL Server Management Studio e valide a migração examinando os dados e o esquema. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Comparar o esquema no SSMS":::

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


## <a name="migration-assets"></a>Ativos de migração 

Para obter assistência adicional, consulte os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real:

|Ativo  |Descrição  |
|---------|---------|
|[Modelo e ferramenta de avaliação de carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado.|
|[Pacote de avaliação e descoberta de ativos de dados do DB2 da zOS](https://github.com/Microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Depois de executar o script SQL em um banco de dados, você pode exportar os resultados para um arquivo no sistema de arquivos. Há compatibilidade com vários formatos de arquivo, incluindo *.csv, para que você possa capturar os resultados em ferramentas externas, como planilhas. Esse método pode ser útil caso você queira compartilhar facilmente os resultados com equipes que não têm a área de trabalho instalada.|
|[Artefatos e scripts de inventário do IBM DB2 LUW](https://github.com/Microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Este ativo inclui uma consulta SQL que alcança as tabelas do sistema do IBM DB2 LUW versão 11.1 e fornece uma contagem de objetos por esquema e por tipo de objeto, uma estimativa aproximada de "dados brutos" em cada esquema e o dimensionamento de tabelas em cada esquema, com resultados armazenados em um formato CSV.|
|[Escala pura do DB2 LUW no Azure – guia de instalação](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Este guia serve como ponto de partida para um plano de implementação do DB2. Embora os requisitos de negócios sejam diferentes, o mesmo padrão básico se aplica. Esse padrão de arquitetura também pode ser usado para aplicativos OLAP no Azure.|

Esses recursos foram desenvolvidos como parte do programa Data SQL Ninja, que é patrocinado pela equipe de engenharia do Grupo de Dados do Azure. A principal responsabilidade do programa Data SQL Ninja é desbloquear e acelerar as oportunidades complexas e diversas de migração da plataforma de dados para a plataforma de Dados do Azure da Microsoft. Se você acredita que sua organização tem interesse em participar do programa Data SQL Ninja, entre em contato com sua equipe de contas e peça que eles enviem uma indicação.

## <a name="next-steps"></a>Próximas etapas

Após a migração, examine o [Guia de validação e otimização pós-migração](/sql/relational-databases/post-migration-validation-and-optimization-guide). 

Para obter uma matriz de serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para ajudá-lo com vários cenários de migração de banco de dados e de aplicativos, bem como tarefas de especialidade, consulte [Serviços e ferramentas de migração de dados](../../../dms/dms-tools-matrix.md).

Para obter outros guias de migração, confira [Migração de banco de dados](https://datamigration.microsoft.com/). 

Para ver conteúdo de vídeo, consulte:
- [Visão geral do percurso de migração](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)