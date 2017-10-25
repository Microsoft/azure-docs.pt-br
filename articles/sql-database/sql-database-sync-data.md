---
title: "Sincronização de dados (Visualização) | Microsoft Docs"
description: "Esta visão geral apresenta a Sincronização de Dados SQL do Azure (Visualização)."
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.openlocfilehash: ed2266004e60843749233f92c8f4b069e4c17ba5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL

Sincronização de Dados SQL é um serviço baseado no Banco de Dados SQL do Azure que lhe permite sincronizar os dados que você selecionar bidirecionalmente em vários bancos de dados SQL e instâncias do SQL Server.

A Sincronização de Dados é baseada em torno do conceito de um Grupo de Sincronização. Um Grupo de Sincronização é um grupo de bancos de dados que você deseja sincronizar.

Um Grupo de Sincronização tem as seguintes propriedades:

-   O **Esquema de Sincronização** descreve quais dados estão sendo sincronizados.

-   A **Direção da Sincronização** pode ser bidirecional ou pode fluir em uma única direção. Ou seja, a Direção da Sincronização pode ser *Hub para Membro* ou *Membro para Hub*, ou ambos.

-   O **Intervalo de Sincronização** é a frequência com a qual ocorre a sincronização.

-   A **Política de Resolução de Conflito** é uma política em nível de grupo, que pode ser *Hub ganha* ou *Membro ganha*.

A Sincronização de Dados usa uma topologia hub-spoke para sincronizar os dados. Defina um dos bancos de dados no grupo como o Banco de Dados Hub. O restante dos bancos de dados são bancos de dados de membros. A sincronização ocorre apenas entre o Hub e membros individuais.
-   O **Banco de Dados Hub** deve ser um Banco de Dados SQL do Azure.
-   Os **bancos de dados de membro** podem ser Bancos de Dados SQL, bancos de dados locais do SQL Server ou instâncias do SQL Server em máquinas virtuais do Azure.
-   O **Banco de Dados de Sincronização** contém os metadados e o log de Sincronização de Dados. O Banco de Dados de Sincronização deve ser um Banco de Dados SQL do Azure localizado na mesma região do Banco de Dados Hub. O Banco de Dados de Sincronização é criado pelo cliente e é propriedade do cliente.

> [!NOTE]
> Se você estiver usando um banco de dados local, você precisa [configurar um agente local.](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)

![Sincronizar dados entre bancos de dados](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Quando usar a Sincronização de Dados

A Sincronização de Dados é útil em casos onde os dados precisam ser atualizados em vários Bancos de Dados SQL do Azure ou Bancos de Dados do SQL Server. Estes são os casos de uso principais para Sincronização de Dados:

-   **Sincronização de Dados Híbrida:** com a Sincronização de Dados, você pode manter os dados sincronizados entre seus bancos de dados local e os Bancos de Dados SQL do Azure para habilitar aplicativos híbridos. Esse recurso pode ser atraente para clientes que estejam avaliando a mudança para a nuvem e gostariam de colocar alguns dos seus aplicativos no Azure.

-   **Aplicativos Distribuídos:** Em muitos casos, é útil separar diferentes cargas de trabalho em bancos de dados diferentes. Por exemplo, se você tiver um banco de dados de produção grande, mas você também precisa executar uma carga de trabalho de relatório ou análise de dados, é útil ter um segundo banco de dados para essa carga de trabalho adicional. Essa abordagem minimiza o impacto no desempenho da sua carga de trabalho de produção. Você pode usar a Sincronização de Dados para manter esses dois bancos de dados sincronizados.

-   **Aplicativos Distribuídos Globalmente:** Muitas empresas abrangem várias regiões e até mesmo vários países. Para minimizar a latência de rede, é melhor ter seus dados em uma região perto de você. Com a sincronização de dados, você pode facilmente manter os bancos de dados em regiões de todo o mundo sincronizados.

Não recomendamos a Sincronização de Dados para os seguintes cenários:

-   Recuperação de desastre

-   Escala de Leitura

-   ETL (OLTP para OLAP)

-   Migração do SQL Server local para um Banco de Dados SQL do Azure

## <a name="how-does-data-sync-work"></a>Como funciona a Sincronização de Dados? 

-   **Controle de alterações de dados:** A Sincronização de Dados controla alterações usando os gatilhos inserir, atualizar e excluir. As alterações são registradas em uma tabela secundária do banco de dados do usuário.

-   **Sincronização de dados:** a Sincronização de Dados é criada em um modelo Hub-Spoke. O Hub é sincronizado com cada membro individualmente. As alterações do Hub são baixadas para o membro e, em seguida, as alterações do membro são carregadas para o Hub.

-   **Resolução de conflitos:** A Sincronização de Dados fornece duas opções para a resolução de conflito, *Hub ganha* ou *Membro ganha*.
    -   Se você selecionar *Hub ganha*, as alterações no hub sempre substituem as alterações no membro.
    -   Se você selecionar *Membro ganha*, as alterações no membro sempre substituem as alterações no hub. Se houver mais de um membro, o valor final depende de qual membro será sincronizado pela primeira vez.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="performance-impact"></a>Impacto sobre o desempenho
A Sincronização de Dados usa os gatilhos inserir, atualizar e excluir para controlar as alterações. Ela cria tabelas secundárias no banco de dados do usuário para controle de alterações. Essas atividades de controle de alterações têm um impacto sobre sua carga de trabalho do banco de dados. Avalie sua camada de serviço e faça a atualização necessário.

### <a name="eventual-consistency"></a>Consistência eventual
Como a Sincronização de Dados é baseada no gatilho, a consistência transacional não é garantida. A Microsoft garante que todas as alterações são feitas, eventualmente, e que a Sincronização de Dados não causa perda de dados.

### <a name="unsupported-data-types"></a>Tipos de dados sem suporte

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (suporte para XML)

-   Cursor, Timestamp, Hierarchyid

### <a name="requirements"></a>Requisitos

-   Cada tabela deve ter uma chave primária. Não altere o valor da chave primária em nenhuma linha. Se você precisar fazer isso, exclua a linha e recrie-a com o novo valor de chave primária. 

-   Uma tabela não pode uma coluna de identidade que não seja a chave primária.

-   Os nomes de objetos (bancos de dados, tabelas e colunas) não podem conter os caracteres imprimíveis ponto (.), colchete esquerdo ([) ou colchete direito (]).

-   O isolamento de instantâneo deve estar habilitado. Para obter mais informações, consulte [Isolamento de instantâneo no SQL Server](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões de serviço e do banco de dados

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **Dimensões**                                                      | **Limite**              | **Solução alternativa**              |
| Número máximo de grupos de sincronização aos quais qualquer banco de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos de extremidade em um único grupo de sincronização              | 30                     | Criar vários grupos de sincronização |
| Número máximo de pontos de extremidade locais em um único grupo de sincronização. | 5                      | Criar vários grupos de sincronização |
| Nomes de coluna, tabela, esquema e banco de dados                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas em uma tabela em um grupo de sincronização                              | 1000                   |                             |
| Tamanho da linha de dados em uma tabela                                        | 24 Mb                  |                             |
| Intervalo de sincronização mínima                                           | 5 Minutos              |                             |

## <a name="common-questions"></a>Perguntas comuns

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Com que frequência a Sincronização de Dados pode sincronizar meus dados? 
A frequência mínima é a cada cinco minutos.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Posso usar a Sincronização de Dados para sincronizar somente entre bancos de dados locais do SQL Server? 
Não diretamente. Contudo, é possível sincronizar entre bancos de dados locais do SQL Server indiretamente criando um banco de dados Hub no Azure e adicionando os bancos de dados locais ao grupo de sincronização.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Posso usar a Sincronização de Dados para propagar dados do meu banco de dados de produção para um banco de dados vazio e mantê-los sincronizados? 
Sim. Crie o esquema manualmente no novo banco de dados desenvolvendo o script com base no original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-los sincronizados.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Por que vejo tabelas que eu não criei?  
A Sincronização de Dados cria tabelas secundárias no banco de dados para controle de alterações. Não as exclua, pois a Sincronização de Dados deixará de funcionar.
   
### <a name="i-got-an-error-message-that-said-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-the-error"></a>Recebi uma mensagem de erro que diz “Não é possível inserir o valor NULL na coluna \<coluna\>. A coluna não permite valores nulos”. O que isso significa e como posso corrigir o erro? 
Essa mensagem de erro indica que um dos dois problemas a seguir está ocorrendo:
1.  Uma tabela pode estar sem uma chave primária. Para corrigir esse problema, adicione uma chave primária a todas as tabelas que você está sincronizando.
2.  Pode haver uma cláusula WHERE na instrução CREATE INDEX. A sincronização não trata essa condição. Para corrigir esse problema, remova a cláusula WHERE ou realize as alterações manualmente para todos os bancos de dados. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a>Como a Sincronização de Dados trata referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização, fazendo com que sejam continuamente alterados?
A Sincronização de Dados não trata referências circulares. Evite usá-las. 

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Como exportar e importar um banco de dados com a Sincronização de Dados?
Depois de exportar um banco de dados como um arquivo .bacpac e importá-lo para criar um novo banco de dados, você precisa realizar as duas ações a seguir para usar a Sincronização de Dados no novo banco de dados:
1.  Limpe os objetos de Sincronização de Dados e tabelas laterais no **novo banco de dados** usando [esse script](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Esse script exclui todos os objetos de Sincronização de Dados necessários do banco de dados.
2.  Recrie o grupo de sincronização com o novo banco de dados. Se você não precisar mais do grupo de sincronização antigo, exclua-o.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Sincronização de Dados SQL, veja:

-   [Introdução à Sincronização de Dados SQL](sql-database-get-started-sql-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Baixe a documentação técnica completa da Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](sql-database-technical-overview.md)

-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
