---
title: O que é o Sincronização de Dados SQL para o Azure?
description: Esta visão geral apresenta Sincronização de Dados SQL para o Azure, que permite que você sincronize dados em vários bancos de dado de nuvem e locais.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: c38e4681c76fb0dd52d77c7dc1438b87a9571a80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562052"
---
# <a name="what-is-sql-data-sync-for-azure"></a>O que é o Sincronização de Dados SQL para o Azure?

O Sincronização de Dados SQL é um serviço criado no banco de dados SQL do Azure que permite sincronizar o bi-Directional em vários bancos de dado, tanto localmente quanto na nuvem. 

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL não dá suporte ao Azure SQL Instância Gerenciada no momento.


## <a name="overview"></a>Visão geral 

A sincronização de dados é baseada em relação ao conceito de um grupo de sincronização. Um grupo de sincronização é um grupo de bancos de dados que você deseja sincronizar.

A Sincronização de Dados usa uma topologia hub-spoke para sincronizar os dados. Você define um dos bancos de dados no grupo de sincronização como o banco de dados de Hub. O restante dos bancos de dados são bancos de dados de membros. A sincronização ocorre somente entre o Hub e os membros individuais.

- O **Banco de Dados Hub** deve ser um Banco de Dados SQL do Azure.
- Os **bancos** de dados do membro podem ser de ambos os bancos de dados do Azure SQL ou em instâncias do SQL Server.
- O **banco** de dados de metadados de sincronização contém os metadados e o log para sincronização de data. O banco de dados de metadados de sincronização deve ser um banco de dados SQL do Azure localizado na mesma região que o banco de dados de Hub. O banco de dados de metadados de sincronização é criado pelo cliente e pertence ao cliente. Você só pode ter um banco de dados de metadados de sincronização por região e assinatura. O banco de dados de metadados de sincronização não pode ser excluído ou renomeado enquanto grupos de sincronização ou agentes de sincronização existirem. A Microsoft recomenda que você crie um novo banco de dados vazio para usar como o banco de dados de metadados de sincronização. A Sincronização de Dados cria tabelas nesse banco de dados e executa uma carga de trabalho frequente.

> [!NOTE]
> Se você usando um banco de dados local como um banco de dados de membro, você terá que [instalar e configurar um agente de sincronização local](sql-data-sync-sql-server-configure.md#add-on-prem).

![Sincronizar dados entre bancos de dados](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Um grupo de sincronização tem as seguintes propriedades:

- O **Esquema de Sincronização** descreve quais dados estão sendo sincronizados.
- A **Direção da Sincronização** pode ser bidirecional ou pode fluir em uma única direção. Ou seja, a direção de sincronização pode ser *Hub para membro* ou *membro para Hub*, ou ambos.
- O **Intervalo de Sincronização** descreve a frequência com a qual ocorre a sincronização.
- A **Política de Resolução de Conflito** é uma política em nível de grupo, que pode ser *Hub ganha* ou *Membro ganha*.

## <a name="when-to-use"></a>Quando usar

A sincronização de dados é útil nos casos em que os dados precisam ser mantidos atualizados em vários bancos de dados no banco de dados SQL do Azure ou SQL Server. Estes são os casos de uso principais para Sincronização de Dados:

- **Sincronização de dados híbridos:** Com a sincronização de dados, você pode manter os dados sincronizados entre seus bancos de dados no SQL Server e no banco de dados SQL do Azure para habilitar aplicativos híbridos. Esse recurso pode ser atraente para clientes que estejam avaliando a mudança para a nuvem e gostariam de colocar alguns dos seus aplicativos no Azure.
- **Aplicativos Distribuídos:** Em muitos casos, é útil separar diferentes cargas de trabalho em bancos de dados diferentes. Por exemplo, se você tiver um banco de dados de produção grande, mas você também precisa executar uma carga de trabalho de relatório ou análise de dados, é útil ter um segundo banco de dados para essa carga de trabalho adicional. Essa abordagem minimiza o impacto no desempenho da sua carga de trabalho de produção. Você pode usar a Sincronização de Dados para manter esses dois bancos de dados sincronizados.
- **Aplicativos distribuídos globalmente:** Muitas empresas abrangem várias regiões e até mesmo vários países/regiões. Para minimizar a latência de rede, é melhor ter seus dados em uma região perto de você. Com a sincronização de dados, você pode facilmente manter os bancos de dados em regiões de todo o mundo sincronizados.

A sincronização de dados não é a solução preferida para os seguintes cenários:

| Cenário | Algumas soluções recomendadas |
|----------|----------------------------|
| Recuperação de desastre | [Backups com redundância geográfica do Azure](automated-backups-overview.md) |
| Escala de Leitura | [Usar réplicas somente leitura para balancear a carga de cargas de trabalho de consulta somente leitura (visualização)](read-scale-out.md) |
| ETL (OLTP para OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ou [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migração de SQL Server para o banco de dados SQL do Azure. No entanto, Sincronização de Dados SQL pode ser usado após a conclusão da migração, para garantir que a origem e o destino sejam mantidos em sincronia.  | [Serviço de Migração de Banco de Dados do Azure](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Como ele funciona

- **Controle de alterações de dados:** A Sincronização de Dados controla alterações usando os gatilhos inserir, atualizar e excluir. As alterações são registradas em uma tabela secundária do banco de dados do usuário. Observe que BULK INSERT não dispara gatilhos por padrão. Se FIRE_TRIGGERS não for especificado, nenhum gatilho de inserção será executado. Adicionar a opção de FIRE_TRIGGERS para a Sincronização de dados rastrear essas inserções. 
- **Sincronizando dados:** A sincronização de dados é projetada em um modelo de Hub e spoke. O Hub é sincronizado com cada membro individualmente. As alterações do hub são baixadas para o membro e, em seguida, as alterações do membro são carregadas no Hub.
- **Resolução de conflitos:** A Sincronização de Dados fornece duas opções para a resolução de conflito, *Hub ganha* ou *Membro ganha*.
  - Se você selecionar *Hub ganha*, as alterações no hub sempre substituem as alterações no membro.
  - Se você selecionar *Membro ganha*, as alterações no membro sempre substituem as alterações no hub. Se houver mais de um membro, o valor final depende de qual membro será sincronizado pela primeira vez.

## <a name="compare-with-transactional-replication"></a>Comparar com a replicação transacional

| | Sincronização de Dados | Replicação transacional |
|---|---|---|
| **Vantagens** | – Suporte ativo-ativo<br/>– Bidirecional entre o Banco de Dados SQL do Azure e o local | – Menor latência<br/>– Consistência transacional<br/>– Reutilização da topologia existente após a migração <br/>-Suporte do Azure SQL Instância Gerenciada |
| **Desvantagens** | – Não há consistência transacional<br/>– Maior impacto do desempenho | -Não é possível publicar do banco de dados SQL do Azure <br/>– Alto custo de manutenção |

## <a name="private-link-for-data-sync-preview"></a>Link privado para sincronização de dados (versão prévia)
O novo recurso de link privado (versão prévia) permite que você escolha um ponto de extremidade privado gerenciado pelo serviço para estabelecer uma conexão segura entre o serviço de sincronização e seus bancos de dados de membro/Hub durante o processo de sincronização de dados. Um ponto de extremidade privado gerenciado pelo serviço é um endereço IP privado em uma rede virtual e sub-rede específica. Na sincronização de dados, o ponto de extremidade particular gerenciado pelo serviço é criado pela Microsoft e é usado exclusivamente pelo serviço de sincronização de dados para uma determinada operação de sincronização. Antes de configurar o link privado, leia os [requisitos gerais](sql-data-sync-data-sql-server-sql-database.md#general-requirements) para o recurso. 

![Link privado para sincronização de dados](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Você deve aprovar manualmente o ponto de extremidade privado gerenciado pelo serviço na página **conexões do ponto de extremidade privado** do portal do Azure durante a implantação do grupo de sincronização ou usando o PowerShell.

## <a name="get-started"></a>Introdução 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Configurar a Sincronização de Dados no Portal do Azure

- [Configurar a Sincronização de Dados SQL do Azure](sql-data-sync-sql-server-configure.md)
- Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Configurar a Sincronização de Dados com o PowerShell

- [Usar o PowerShell para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Usar o PowerShell para sincronizar entre um banco de dados no banco de dados SQL do Azure e em uma instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Configurar a sincronização de dados com a API REST
- [Usar a API REST para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Revisar as práticas recomendadas para a Sincronização de Dados

- [Melhores práticas para a Sincronização de Dados SQL do Azure](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Algo deu errado?

- [Solucionar problemas com a Sincronização de Dados SQL do Azure](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Consistência e desempenho

### <a name="eventual-consistency"></a>Coerência eventual

Como a sincronização de dados é baseada em gatilho, a consistência transacional não é garantida. A Microsoft garante que todas as alterações sejam feitas eventualmente e que a sincronização de dados não cause perda de dados.

### <a name="performance-impact"></a>Impacto sobre o desempenho

A Sincronização de Dados usa os gatilhos inserir, atualizar e excluir para controlar as alterações. Ela cria tabelas secundárias no banco de dados do usuário para controle de alterações. Essas atividades de controle de alterações têm um impacto sobre sua carga de trabalho do banco de dados. Avalie sua camada de serviço e faça a atualização necessário.

Provisionamento e desprovisionamento durante a criação do grupo de sincronização, atualização e exclusão também podem afetar o desempenho do banco de dados.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Requisitos e limitações

### <a name="general-requirements"></a>Requisitos gerais

- Cada tabela deve ter uma chave primária. Não altere o valor da chave primária em nenhuma linha. Se você tiver de alterar o valor de uma chave primária, exclua a linha e recrie-a com o novo valor de chave primária.

> [!IMPORTANT]
> Alterar o valor de uma chave primária existente resultará no seguinte comportamento de falha:
> - Os dados entre o Hub e o membro podem ser perdidos, embora a sincronização não relate nenhum problema.
> - A sincronização pode falhar porque a tabela de rastreamento tem uma linha não existente da origem devido à alteração da chave primária.

- O isolamento de instantâneo deve ser habilitado tanto para membros de sincronização quanto para o Hub. Para obter mais informações, consulte [Isolamento de instantâneo no SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Para usar o link privado com a sincronização de dados, os bancos de dados de membro e de Hub devem ser hospedados no Azure (as mesmas ou em regiões diferentes) no mesmo tipo de nuvem (por exemplo, na nuvem pública ou na nuvem do governo). Além disso, para usar o link privado, os provedores de recursos Microsoft. Network devem ser registrados para as assinaturas que hospedam os servidores de Hub e membro. Por fim, você deve aprovar manualmente o link privado para sincronização de dados durante a configuração de sincronização, na seção "conexões de ponto de extremidade privado" no portal do Azure ou por meio do PowerShell. Para obter mais detalhes sobre como aprovar o link privado, consulte [configurar sincronização de dados SQL](./sql-data-sync-sql-server-configure.md). Depois de aprovar o ponto de extremidade privado gerenciado pelo serviço, toda a comunicação entre o serviço de sincronização e os bancos de dados de membro/Hub ocorrerá sobre o link privado. Os grupos de sincronização existentes podem ser atualizados para que esse recurso seja habilitado.

### <a name="general-limitations"></a>Limitações gerais

- Uma tabela não pode ter uma coluna de identidade que não seja a chave primária.
- Uma tabela deve ter um índice clusterizado para usar a sincronização de dados.
- Uma chave primária não pode ter os seguintes tipos de dados: sql_variant, binary, varbinary, Image, XML.
- Tenha cuidado ao usar os seguintes tipos de dados como uma chave primária, porque a precisão com suporte é apenas para o segundo: time, datatime, datetime2 e datetimeoffset.
- Os nomes de objetos (bancos de dados, tabelas e colunas) não podem conter o ponto de caracteres imprimíveis (.), colchete esquerdo ([) ou colchete direito (]).
- Um nome de tabela não pode conter caracteres imprimíveis:! "# $% ' () * + espaço
- Não há suporte para a autenticação Azure Active Directory.
- Se houver tabelas com o mesmo nome, mas um esquema diferente (por exemplo, dbo. Customers e Sales. Customers), somente uma das tabelas poderá ser adicionada à sincronização.
- Não há suporte para colunas com tipos de dados User-Defined
- Não há suporte para a movimentação de servidores entre assinaturas diferentes. 

#### <a name="unsupported-data-types"></a>Tipos de dados sem suporte

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (suporte para XML)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Não há suporte para os tipos de coluna

A Sincronização de Dados não pode sincronizar colunas somente leitura ou geradas pelo sistema. Por exemplo:

- Colunas computadas.
- Colunas geradas pelo sistema para tabelas temporais.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitações nas dimensões de serviço e do banco de dados

| **Dimensões**                                                  | **Limite**              | **Solução alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronização aos quais qualquer banco de dados pode pertencer.       | 5                      |                             |
| Número máximo de pontos de extremidade em um único grupo de sincronização              | 30                     |                             |
| Número máximo de pontos de extremidade locais em um único grupo de sincronização. | 5                      | Criar vários grupos de sincronização |
| Nomes de coluna, tabela, esquema e banco de dados                       | 50 caracteres por nome |                             |
| Tabelas em um grupo de sincronização                                          | 500                    | Criar vários grupos de sincronização |
| Colunas em uma tabela em um grupo de sincronização                              | 1000                   |                             |
| Tamanho da linha de dados em uma tabela                                        | 24 Mb                  |                             |

> [!NOTE]
> Pode haver até 30 pontos de extremidade em um único grupo de sincronização, se houver apenas um grupo de sincronização. Se houver mais de um grupo de sincronização, o número total de pontos de extremidade em todos os grupos de sincronização não pode exceder 30. Se um banco de dados pertencer a vários grupos de sincronização, ele será contado como vários pontos de extremidade, não um.

### <a name="network-requirements"></a>Requisitos de rede

> [!NOTE]
> Se você usar o link privado, esses requisitos de rede não se aplicarão. 

Quando o grupo de sincronização é estabelecido, o serviço de sincronização de dados precisa se conectar ao banco de dado Hub. No momento em que você estabelece o grupo de sincronização, o SQL Server do Azure deve ter a seguinte configuração em suas `Firewalls and virtual networks` configurações:

 * *Negar acesso à rede pública* deve ser definido como *desativado*.
 * *Permitir que os serviços e recursos do Azure acessem este servidor* deve ser definido como *Sim* ou você deve criar regras de IP para os [endereços IP usados pelo serviço de sincronização de dados](network-access-controls-overview.md#data-sync).

Depois que o grupo de sincronização for criado e provisionado, você poderá desabilitar essas configurações. O agente de sincronização se conectará diretamente ao banco de dados de Hub e você poderá usar as [regras de IP de firewall](firewall-configure.md) do servidor ou os pontos de [extremidade privados](private-endpoint-overview.md) para permitir que o agente acesse o servidor de Hub.

> [!NOTE]
> Se você alterar as configurações de esquema do grupo de sincronização, será necessário permitir que o serviço de sincronização de dados acesse o servidor novamente para que o banco de dados de Hub possa ser reprovisionado.

## <a name="faq-about-sql-data-sync"></a>Perguntas Frequentes sobre a Sincronização de Dados SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Quanto custa o serviço de Sincronização de Dados SQL?

Não há encargos pelo próprio serviço de Sincronização de Dados SQL. No entanto, você ainda coleta cobranças de transferência de dados para movimentação de dados para dentro e para fora de sua instância do SQL Database. Para saber mais, veja [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quais regiões dão suporte à Sincronização de Dados?

A Sincronização de Dados SQL está disponível em todas as regiões.

### <a name="is-a-sql-database-account-required"></a>É necessária uma conta do Banco de Dados SQL?

Sim. Você deve ter uma conta do banco de dados SQL para hospedar o banco de dados Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Posso usar a sincronização de dados para sincronização entre os bancos de SQL Server apenas

Não diretamente. No entanto, você pode sincronizar entre os bancos de dados do SQL Server indiretamente, criando um banco de dados de Hub no Azure e, em seguida, adicionando os dados locais ao grupo de sincronização.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Posso usar a sincronização de dados para sincronizar entre bancos de dado no banco de dados SQL que pertencem a assinaturas diferentes

Sim. Você pode sincronizar entre bancos de dados que pertencem a grupos de recursos de propriedade de assinaturas diferentes.

- Se as assinaturas pertencerem ao mesmo locatário e você tiver permissão para todas as assinaturas, será possível configurar o grupo de sincronização no Portal do Azure.
- Caso contrário, será necessário usar o PowerShell para adicionar os membros de sincronização que pertencem a assinaturas diferentes.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Posso usar a sincronização de dados para sincronizar entre bancos de dado no banco de dados SQL que pertencem a nuvens diferentes (como nuvem pública do Azure e Azure China 21Vianet)

Sim. Você pode sincronizar entre bancos de dados que pertencem a nuvens diferentes. Você precisa usar o PowerShell para adicionar os membros de sincronização que pertencem às diferentes assinaturas.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Posso usar a Sincronização de Dados para propagar dados do meu banco de dados de produção para um banco de dados vazio e sincronizá-los?

Sim. Crie o esquema manualmente no novo banco de dados desenvolvendo o script com base no original. Depois de criar o esquema, adicione as tabelas a um grupo de sincronização para copiar os dados e mantê-los sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Devo usar a Sincronização de Dados SQL para fazer backup e restaurar meus bancos de dados?

Não é recomendável usar Sincronização de Dados SQL para criar um backup de seus dados. Não é possível fazer backup e restaurar para um ponto específico no tempo porque Sincronização de Dados SQL sincronizações não têm controle de versão. Além disso, Sincronização de Dados SQL não faz backup de outros objetos SQL, como procedimentos armazenados, e não faz o equivalente de uma operação de restauração rapidamente.

Para obter uma técnica de backup recomendada, consulte [copiar um banco de dados no banco de dados SQL do Azure](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>A Sincronização de Dados pode sincronizar tabelas e colunas criptografadas?

- Se um banco de dados usar Always Encrypted, será possível sincronizar apenas as tabelas e colunas *não* criptografadas. Não é possível sincronizar as colunas criptografadas porque a sincronização de dados não pode descriptografar os dados.
- Se uma coluna usar a CLE (Criptografia em Nível de Coluna), será possível sincronizar a coluna, desde que o tamanho da linha seja menor que o tamanho máximo de 24 MB. A Sincronização de Dados trata a coluna criptografada pela chave (CLE) como dados binários normais. Para descriptografar os dados em outros membros de sincronização, é necessário ter o mesmo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>Ordenações são compatíveis com a Sincronização de Dados SQL?

Sim. A Sincronização de Dados SQL dá suporte a ordenações nos seguintes cenários:

- Se as tabelas de esquema de sincronização selecionadas ainda não estiverem em seus bancos de dados de Hub ou membro, quando você implantar o grupo de sincronização, o serviço criará automaticamente as tabelas e colunas correspondentes com as configurações de agrupamento selecionadas nos bancos de dados de destino vazios.
- Se as tabelas a serem sincronizadas já existirem nos bancos de dados hub e membro, a Sincronização de Dados SQL exigirá que as colunas de chave primária tenham a mesma ordenação entre bancos de dados hub e membro para implantar com êxito o grupo de sincronização. Não há nenhuma restrição de ordenação em colunas que não sejam colunas de chave primária.

### <a name="is-federation-supported-in-sql-data-sync"></a>A federação é compatível com a Sincronização de Dados SQL?

O Banco de Dados de Raiz da Federação pode ser usado no Serviço da Sincronização de Dados SQL sem qualquer limitação. Você não pode adicionar o ponto de extremidade do banco de dados federado à versão atual do Sincronização de Dados SQL.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Posso usar a sincronização de dados para sincronizar dados exportados do Dynamics 365 usando o recurso traga seu próprio banco de dados (BYOD)?

O recurso traga seu próprio banco de dados do Dynamics 365 permite que os administradores exportem entidades do aplicativo para suas próprias Microsoft Azure banco de dado SQL. A sincronização de dados pode ser usada para sincronizar esses dados em outros bancos de dado se os dados forem exportados usando o **Push incremental** (não há suporte para Push completo) e **habilitar gatilhos no banco de dados de destino** estiver definido como **Sim**.

## <a name="next-steps"></a>Próximas etapas

### <a name="update-the-schema-of-a-synced-database"></a>Atualizar o esquema de um banco de dados sincronizado

Você precisa atualizar o esquema de um banco de dados em um grupo de sincronização? As alterações de esquema não são replicadas automaticamente. Para algumas soluções, consulte os seguintes artigos:

- [Automatizar a replicação de alterações de esquema com o Sincronização de Dados SQL no Azure](./sql-data-sync-update-sync-schema.md)
- [Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorar e solucionar problemas

O Sincronização de Dados SQL está sendo feito conforme o esperado? Para monitorar a atividade e solucionar problemas, consulte os seguintes artigos:

- [Monitorar a Sincronização de Dados SQL com logs do Azure Monitor](./monitor-tune-overview.md)
- [Solucionar problemas com a Sincronização de Dados SQL do Azure](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre o Banco de Dados SQL do Azure

Para obter mais informações sobre o banco de dados SQL do Azure, consulte os seguintes artigos:

- [Visão geral do Banco de Dados SQL](sql-database-paas-overview.md)
- [Gerenciamento de ciclo de vida do banco de dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
