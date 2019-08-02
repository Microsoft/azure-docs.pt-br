---
title: Grupos de failover – Banco de Dados SQL do Azure | Microsoft Docs
description: Os grupos de failover automático é um recurso do Banco de Dados SQL que permite que você gerencie a replicação e failover automático/coordenado de um grupo de bancos de dados em um servidor do Banco de Dados SQL ou todos os bancos de dados na instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 07/18/2019
ms.openlocfilehash: bd68909f51ff6cead8484ae4ab9f2557e9d6554e
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443313"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Use grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados

Os grupos de failover automático são um recurso de banco de dados SQL que permite que você gerencie a replicação e o failover de um grupo de bancos de dados em um servidor de banco de dados SQL ou de todos os bancos em uma instância gerenciada para outra região. É uma abstração declarativa sobre o recurso de [replicação geográfica ativa](sql-database-active-geo-replication.md) existente, projetado para simplificar a implantação e o gerenciamento de bancos de dados replicados geograficamente em escala. Você pode iniciar o failover manualmente ou pode delegá-lo para o serviço de Banco de Dados SQL com base em uma política definida pelo usuário. A última opção permite que você recupere automaticamente vários bancos de dados relacionados em uma região secundária após uma falha catastrófica ou outro evento não planejado que resulte em perda total ou parcial de disponibilidade do serviço de Banco de Dados SQL na região primária. Um grupo de failover pode incluir um ou vários bancos de dados, normalmente usados pelo mesmo aplicativo. Além disso, eles podem usar os bancos de dados secundários legíveis para descarregar cargas de trabalho de consulta somente leitura. Como os grupos de failover automático incluem vários bancos de dados, esses bancos de dados devem ser configurados no servidor primário. Servidores primários e secundários para bancos de dados no grupo de failover devem estar na mesma assinatura. Os grupos de failover automático oferecem suporte à replicação de todos os bancos de dados no grupo para apenas um servidor secundário em uma região diferente.

> [!NOTE]
> Ao trabalhar com bancos de dados individuais ou em pool em um servidor do Banco de Dados SQL, se quiser vários secundários nas mesmas regiões ou em regiões diferentes, use a [replicação geográfica ativa](sql-database-active-geo-replication.md).

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete um ou vários bancos de dados no grupo resultar em failover automático. Além disso, os grupos de failover automático fornecem pontos de extremidade de ouvinte de leitura/gravação e somente leitura que permanecem inalterados durante failovers. Não importa se você usa a ativação de failover manual ou automática, o failover alterna todos os bancos de dados secundários no grupo para primário. Após o failover de banco de dados ser concluído, o registro DNS é atualizado automaticamente para redirecionar os pontos de extremidade para a nova região. Para os dados específicos de RPO e RTO, confira [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete bancos de dados no servidor do Banco de Dados SQL ou na instância gerenciada resulta em um failover automático. Você pode gerenciar o grupo de failover automático usando:

- O [Portal do Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell: Grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: Grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como criar soluções para a recuperação de desastre, veja [Criando soluções de nuvem para a recuperação de desastre usando a replicação geográfica ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funcionalidades e terminologia de grupo de failover automático

- **Grupo de failover (neblina)**

  Um grupo de failover é um grupo nomeado de bancos de dados gerenciados por um único servidor de banco de dados SQL ou em uma única instância gerenciada que pode fazer failover como uma unidade para outra região caso todos ou alguns bancos de dados primários fiquem indisponíveis devido a uma interrupção na região primária. Quando criadas para instâncias gerenciadas, um grupo de failover contém todos os bancos de dados de usuário na instância e, portanto, apenas um grupo de failover pode ser configurado em uma instância do.
  
  > [!IMPORTANT]
  > O nome do grupo de failover deve ser globalmente exclusivo no `.database.windows.net` domínio.

- **Servidores do Banco de Dados SQL**

     Com servidores do Banco de Dados SQL, alguns ou todos os bancos de dados do usuário em um único servidor podem ser colocados em um grupo de failover. Além disso, um servidor do Banco de Dados SQL dá suporte a vários grupos de failover em um único servidor do Banco de Dados SQL.

- **Primário**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos dos dados primários no grupo de failover.

- **Secundário**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos dos dados secundários no grupo de failover. O secundário não pode estar na mesma região do primário.

- **Adicionar bancos de dados individuais ao grupo de failover**

  É possível colocar vários bancos de dados individuais no mesmo servidor do Banco de Dados SQL no mesmo grupo de failover. Se você adicionar um banco de dados individual ao grupo de failover, ele criará automaticamente um banco de dados secundário usando a mesma edição e tamanho da computação no servidor secundário.  Você especificou esse servidor ao criar o grupo de failover. Se você adicionar um banco de dados que já possui um banco de dados secundário no servidor secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no servidor secundário.
  
  > [!IMPORTANT]
  > Em uma instância gerenciada, todos os bancos de dados de usuário são replicados. Você não pode escolher um subconjunto de bancos de dados de usuário para replicação no grupo de failover.

- **Adicionar bancos de dados no pool elástico para o grupo de failover**

  É possível colocar todos ou vários bancos de dados dentro de um pool elástico no mesmo grupo de failover. Se o banco de dados primário estiver em um pool elástico, o banco de dados secundário é criado automaticamente no pool elástico com o mesmo nome (pool secundário). Você deve garantir que o servidor secundário contém um pool elástico com exatamente o mesmo nome e capacidade livre suficiente para hospedar os bancos de dados secundários que serão criados pelo grupo de failover. Se você adicionar um banco de dados no pool que já possui um banco de dados secundário no pool secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no pool secundário.
  
- **Zona DNS**

  Uma ID exclusiva que é gerada automaticamente quando uma nova instância é criada. Um certificado de vários domínios (SAN) para essa instância é provisionado para autenticar as conexões de cliente com qualquer instância na mesma zona DNS. As duas instâncias gerenciadas no mesmo grupo de failover devem compartilhar a zona DNS. 
  
  > [!NOTE]
  > Uma ID de zona DNS não é necessária para grupos de failover criados para servidores de banco de dados SQL.

- **Ouvinte de leitura/gravação do grupo de failover**

  Um registro DNS CNAME que aponta para a URL primária atual. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho de leitura/gravação do SQL Reconecte-se de forma transparente ao banco de dados primário quando o primário é alterado após o failover. Quando o grupo de failover é criado em um servidor de banco de dados SQL, o registro DNS CNAME para a URL `<fog-name>.database.windows.net`do ouvinte é formado como. Quando o grupo de failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.database.windows.net`.

- **Ouvinte de somente leitura do grupo de failover**

  Foi formado um registro CNAME de DNS que aponta ao ouvinte somente leitura que aponta à URL do secundário. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho SQL somente leitura se conecte de forma transparente ao secundário usando as regras de balanceamento de carga especificadas. Quando o grupo de failover é criado em um servidor de banco de dados SQL, o registro DNS CNAME para a URL `<fog-name>.secondary.database.windows.net`do ouvinte é formado como. Quando o grupo de failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.secondary.database.windows.net`.

- **Política de failover automático**

  Por padrão, um grupo de failover é configurado com uma política de failover automático. O serviço de Banco de Dados SQL dispara o failover após a falha ser detectada e o período de cortesia expirar. O sistema deve verificar se a interrupção não pode ser mitigada pela [infraestrutura de alta disponibilidade interna do serviço do Banco de Dados SQL](sql-database-high-availability.md) devido à escala do impacto. Se você deseja controlar o fluxo de trabalho de failover do aplicativo, pode desligar o failover automático.

- **Política de failover somente leitura**

  Por padrão, o failover do ouvinte somente leitura é desabilitado. Isso garante que o desempenho do primário não seja afetado quando o secundário estiver offline. No entanto, isso também significa que as sessões somente leitura não poderão conectar-se até que o secundário seja recuperado. Se não for possível tolerar o tempo de inatividade para sessões somente leitura e tiver condições para usar temporariamente o primário tanto para tráfego somente leitura como leitura/gravação às custas da possível degradação do desempenho do primário, você poderá habilitar o failover para o ouvinte somente leitura. Nesse caso, o tráfego somente leitura será redirecionado automaticamente para o primário se o secundário não estiver disponível.

- **Failover planejado**

   O failover planejado executa uma sincronização completa entre o banco de dados primário e o secundário antes de o secundário mudar para a função de primário. Isso assegura que não ocorra nenhuma perda de dados. O failover planejado é usado nos seguintes cenários:

  - Executar a recuperação de desastre em produção quando a perda de dados não é aceitável
  - Relocar os bancos de dados para uma região diferente
  - Retorne os bancos de dados para a região primária após a interrupção ter sido atenuada (failback).

- **Failover não planejado**

   Um failover forçado ou não planejado mudará imediatamente o secundário para a função primária, sem nenhuma sincronização com o primário. Esta operação pode resultar em perda de dados. Um failover não planejado é usado como um método de recuperação durante as interrupções quando o primário não está acessível. Quando o primário original estiver online novamente, ele será reconectado automaticamente sem sincronização e se tornará um novo secundário.

- **Failover manual**

  Você pode iniciar o failover manualmente a qualquer momento, independentemente da configuração de failover automático. Se a política de failover automático não for configurada, será necessário fazer o failover manual para recuperar os bancos de dados no grupo de failover para o secundário. Você pode iniciar um failover forçado ou amigável (com sincronização total de dados). O failover manual pode ser usado para relocar o primário para a região secundária. Quando o failover estiver concluído os registros DNS são atualizados automaticamente para garantir a conectividade com o novo primário

- **Período de carência com perda de dados**

  Como os bancos de dados primário e secundário são sincronizados usando replicação assíncrona, o failover pode resultar em perda de dados. Você pode personalizar a política de failover automático para refletir a tolerância do seu aplicativo à perda de dados. Ao configurar **GracePeriodWithDataLossHours**, você poderá controlar quanto tempo o sistema aguarda antes de iniciar o failover que provavelmente resultará em perda de dados.

- **Vários grupos de failover**

  Você pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de failovers. Cada grupo sofre failover de forma independente. Se seu aplicativo multilocatário usa pools elásticos, você pode usar esse recurso para misturar os bancos de dados primários e secundários em cada pool. Dessa forma, você pode reduzir o impacto de uma interrupção a somente metade dos locatários.

  > [!NOTE]
  > A Instância Gerenciada não dá suporte a vários grupos de failover.
  
## <a name="permissions"></a>Permissões
As permissões para um grupo de failover são gerenciadas por [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md). A função [colaborador de SQL Server](../role-based-access-control/built-in-roles.md#sql-server-contributor) tem todas as permissões necessárias para gerenciar grupos de failover. 

### <a name="create-failover-group"></a>Criar grupo de failover
Para criar um grupo de failover, você precisa de acesso de gravação de RBAC para os servidores primários e secundários e para todos os bancos de dados no grupo de failover. Para uma instância gerenciada, você precisa de acesso de gravação de RBAC para a instância gerenciada primária e secundária, mas as permissões em bancos de dados individuais não são relevantes, pois bancos de dados individuais de instância gerenciada não podem ser adicionados ou removidos de um grupo de failover. 

### <a name="update-a-failover-group"></a>Atualizar um grupo de failover
Para atualizar um grupo de failover, você precisa de acesso de gravação do RBAC para o grupo de failover e de todos os bancos de dados no servidor primário atual ou instância gerenciada.  

### <a name="failover-a-failover-group"></a>Failover de um grupo de failover
Para fazer failover de um grupo de failover, você precisa de acesso de gravação de RBAC ao grupo de failover no novo servidor primário ou instância gerenciada. 

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Práticas recomendadas de como usar grupos de failover com bancos de dados individuais e pools elásticos

O grupo de failover automático precisa ser configurado no servidor do Banco de Dados SQL primário e o conectará ao servidor do Banco de Dados SQL secundário em outra região do Azure.  Os grupos podem incluir alguns ou todos os bancos de dados nesses servidores. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando vários bancos de dados e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group.png)

Ao projetar um serviço pensando em continuidade de negócios, siga estas diretrizes gerais:

- **Use um ou vários grupos de failover para gerenciar failover de vários bancos de dados**

  Um ou mais grupos de failover podem ser criados entre dois servidores em diferentes regiões (servidores primário e secundário). Cada grupo pode conter um ou vários bancos de dados que são recuperados como uma unidade no caso de alguns ou todos os bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária. O grupo de failover cria um banco de dados geograficamente secundário com o mesmo objetivo de serviço do primário. Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, certifique-se de que o geograficamente secundário esteja configurado com o mesmo nível de serviço e tamanho da computação do primário.

- **Use ouvinte de leitura/gravação para carga de trabalho OLTP**

  Ao executar operações de OLTP, use `<fog-name>.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. Observe que o failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS.

- **Use o ouvinte somente leitura para a carga de trabalho somente leitura**

  Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para sessões somente leitura, use `<fog-name>.secondary.database.windows.net` como a URL do servidor e a conexão é direcionada automaticamente para o secundário. Também recomendamos que você indique na cadeia de conexão a intenção de leitura usando **ApplicationIntent=ReadOnly**.

- **Prepare-se para degradação de desempenho**

  A decisão de failover do SQL é independente do restante do aplicativo ou de outros serviços usados. O aplicativo pode estar "misturado", com alguns componentes em uma região e outros em outra. Para evitar a degradação, garanta a implantação do aplicativo redundante na região de recuperação de desastre e siga essas [diretrizes de segurança de rede](#failover-groups-and-network-security).

  > [!NOTE]
  > O aplicativo na área de recuperação de desastres não precisa usar uma cadeia de conexão diferente.  

- **Prepare-se para a perda de dados**

  Se uma interrupção é detectada, o SQL aguarda o período especificado por **GracePeriodWithDataLossHours**. O valor padrão é de 1 hora. Se você não puder perder dados, defina **GracePeriodWithDataLossHours** com um número grande o suficiente, como 24 horas. Use o failover manual do grupo para fazer failback do secundário para primário.

  > [!IMPORTANT]
  > Os pools elásticos com 800 ou menos DTUs e mais de 250 bancos de dados usando a replicação geográfica podem encontrar problemas, incluindo failovers planejados mais longos e diminuição do desempenho.  A ocorrência desses problemas é mais provável para cargas de trabalho com uso intensivo de gravação, quando os pontos de extremidade de replicação geográfica são separados por uma grande extensão geográfica ou quando vários pontos de extremidade secundários são usados para cada banco de dados.  Os sintomas desses problemas são indicados quando o retardo da replicação geográfica aumenta ao longo do tempo.  Esse retardo pode ser monitorado usando [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se esses problemas ocorrerem, considere mitigações como aumentar o número de DTUs do pool ou reduzir o número de bancos de dados replicados geograficamente no mesmo pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Práticas recomendadas de uso de grupos de failover com instâncias gerenciadas

O grupo de failover automático precisa ser configurado na instância primária e a conectará à instância secundária em uma região do Azure diferente.  Todos os bancos de dados na instância serão replicados para a instância secundária. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando uma instância gerenciada e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!IMPORTANT]
> Os grupos de failover automático para a Instância Gerenciada estão em versão prévia pública.

Se seu aplicativo usar a instância gerenciada como a camada de dados, siga estas diretrizes gerais ao projetar para continuidade dos negócios:

- **Criar a instância secundária na mesma zona DNS que a instância primária**

  Para garantir conectividade ininterrupta à instância primária após o failover, ambas as instâncias primária e secundária precisam estar na mesma zona DNS. Ele garantirá que o mesmo certificado de vários domínios (SAN) possa ser usado para autenticar as conexões do cliente com uma das duas instâncias no grupo de failover. Quando seu aplicativo está pronto para implantação em produção, crie uma instância do secundário em uma região diferente e assegure que ela compartilhe a zona DNS com a instância do primário. Você pode fazer isso especificando um `DNS Zone Partner` parâmetro opcional usando o portal do Azure, o PowerShell ou a API REST. 

  Para obter mais informações sobre como criar a instância secundária na mesma zona DNS que a instância primária, consulte [Gerenciando grupos de failover com instâncias gerenciadas (versão prévia)](#powershell-managing-failover-groups-with-managed-instances-preview).

- **Permitir o tráfego de replicação entre duas instâncias**

  Já que cada instância é isolada em sua própria rede virtual, o tráfego em duas vias entre essas redes virtuais deve ser permitido. Confira [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Configurar um grupo de failover para gerenciar o failover da instância inteira**

  O grupo de failover gerenciará o failover de todos os bancos de dados na instância. Quando um grupo é criado, cada banco de dados na instância será replicado geograficamente de modo automático para a instância do secundário. Não é possível usar grupos de failover para iniciar um failover parcial de um subconjunto dos bancos de dados.

  > [!IMPORTANT]
  > Se um banco de dados for removido da instância do primário, ele será também removido automaticamente na instância geográfica do secundário.

- **Use ouvinte de leitura/gravação para carga de trabalho OLTP**

  Ao executar operações de OLTP, use `<fog-name>.zone_id.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. O failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS. Como a instância secundária compartilha a zona DNS com o primário, o aplicativo cliente poderá se reconectar a ela usando o mesmo certificado de SAN.

- **Conectar-se diretamente a um secundário replicado geograficamente para consultas somente leitura**

  Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para se conectar diretamente ao secundário com replicação geográfica, use `server.secondary.zone_id.database.windows.net` como a URL do servidor.

  > [!NOTE]
  > Em determinadas camadas de serviço, o Banco de Dados SQL do Azure é compatível com o uso de [réplicas somente leitura](sql-database-read-scale-out.md) para realizar o balanceamento de cargas de trabalho de consulta somente leitura usando a capacidade de uma réplica somente leitura e usando o parâmetro `ApplicationIntent=ReadOnly` na cadeia de conexão. Quando você tiver configurado um secundário replicado geograficamente, você pode usar essa funcionalidade para se conectar a uma réplica somente leitura na localização do primário ou na localização com replicação geográfica.
  > - Para se conectar a uma réplica somente leitura na localização do primário, use `<fog-name>.zone_id.database.windows.net`.
  > - Para se conectar a uma réplica somente leitura no local secundário, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **Prepare-se para degradação de desempenho**

  A decisão de failover do SQL é independente do restante do aplicativo ou de outros serviços usados. O aplicativo pode estar "misturado", com alguns componentes em uma região e outros em outra. Para evitar a degradação, garanta a implantação do aplicativo redundante na região de recuperação de desastre e siga essas [diretrizes de segurança de rede](#failover-groups-and-network-security).

- **Prepare-se para a perda de dados**

  Quando uma falha for detectada, o SQL disparará o failover de leitura-gravação se não houver perda de dados, até onde nós sabemos. Caso contrário, ele aguardará o período especificado por você em `GracePeriodWithDataLossHours`. Se você especificou `GracePeriodWithDataLossHours`, esteja preparado para perda de dados. Em geral, durante interrupções, o Azure favorece a disponibilidade. Se você não puder perder dados, defina GracePeriodWithDataLossHours com um número grande o suficiente, como 24 horas.

  A atualização do DNS do ouvinte de leitura-gravação ocorrerá imediatamente após o início do failover. Esta operação não resultará em perda de dados. No entanto, o processo de mudar as funções de bancos de dados pode levar até 5 minutos em condições normais. Até que ele seja concluído, alguns bancos de dados na nova instância do primário ainda serão somente leitura. Se o failover for iniciado usando o PowerShell, toda a operação será síncrona. Se ele for iniciado usando o portal do Azure, a interface do usuário indicará o status de conclusão. Se ele é iniciado usando a API REST, use o mecanismo de sondagem padrão do Azure Resource Manager para monitorar quanto à conclusão.

  > [!IMPORTANT]
  > Use o failover manual de grupo para mover os primários de volta para a localização original. Quando a interrupção que causou o failover for atenuada, você poderá mover seus bancos de dados primários para a localização original. Para fazer isso, você deve iniciar o failover manual do grupo.

## <a name="failover-groups-and-network-security"></a>Grupos de failover e a segurança de rede

Para alguns aplicativos, as regras de segurança exigem que o acesso à rede para a camada de dados seja restrito a um ou mais componentes específicos, como uma VM, um serviço Web etc. Essa exigência impõe alguns desafios para o design de continuidade de negócios e o uso dos grupos de failover. Considere as seguintes opções ao implementar esse acesso restrito.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Como usar grupos de failover e regras da rede virtual

Se você está usando [Pontos de extremidade e regras de serviço de Rede Virtual](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso ao seu banco de dados SQL, lembre-se de que cada ponto de extremidade de serviço de Rede Virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede. Portanto, apenas os aplicativos implantados na mesma região do cliente podem se conectar ao banco de dados primário. Uma vez que o failover resulta em sessões de cliente SQL serem redirecionadas para um servidor em uma região diferente (secundária), essas sessões falham se originadas de um cliente fora dessa região. Por esse motivo, a política de failover automático não poderá ser habilitada se os servidores participantes estiverem incluídos nas regras de Rede Virtual. Para dar suporte a failover manual, siga estas etapas:

1. Provisione as cópias redundantes dos componentes front-end do seu aplicativo (serviço Web, máquinas virtuais etc.) na região secundária
2. Configurar as [regras da rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) individualmente para os servidores primário e secundário
3. Habilitar o [failover front-end usando uma configuração do Gerenciador de tráfego](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Iniciar o failover manual quando a interrupção for detectada. Essa opção é otimizada para os aplicativos que precisam de latência consistente entre o front-end e a camada de dados e oferece suporte à recuperação quando o front-end, a camada de dados ou ambos são afetados pela interrupção.

> [!NOTE]
> Se você estiver usando o **ouvinte somente leitura** para balanceamento de uma carga de trabalho somente leitura, essa carga de trabalho deverá ser executada em uma VM ou outro recurso na região secundária para que possa se conectar ao banco de dados secundário.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Usando grupos de failover e regras de firewall de banco de dados SQL

Se o seu plano de continuidade de negócios exigir failover usando grupos com failover automático, você poderá restringir o acesso ao banco de dados SQL usando as regras de firewall tradicional.  Para dar suporte a failover automático, siga estas etapas:

1. [Criar um IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Crie um balanceador de carga público](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e atribua o IP público a ele.
3. [Crie uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) para os componentes de front-end
4. [Crie um grupo de segurança de rede](../virtual-network/security-overview.md) e configure conexões de entrada.
5. Verifique se as conexões de saída estão abertas para o banco de dados SQL do Azure usando a [marca de serviço](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Crie uma [regra de firewall de banco de dados SQL](sql-database-firewall-configure.md) para permitir o tráfego de entrada do endereço IP público criado na etapa 1.

Para obter mais informações sobre como configurar o acesso de saída e qual IP usar nas regras de firewall, veja [conexões de saída do balanceador de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima garantirá que o failover automático não bloqueie conexões dos componentes front-end e pressupõe que o aplicativo pode tolerar a latência mais longa entre o front-end e a camada de dados.

> [!IMPORTANT]
> Para garantir a continuidade dos negócios para interrupções regionais, garanta redundância geográfica para bancos de dados e componentes de front-end.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Habilitando a replicação geográfica entre instâncias gerenciadas e suas VNets

Quando você configura um grupo de failover entre instâncias gerenciadas primárias e secundárias em duas regiões diferentes, cada instância é isolada usando uma VNet independente. Para permitir o tráfego de replicação entre esses VNets, verifique se esses pré-requisitos foram atendidos:

1. As duas instâncias gerenciadas precisam estar em regiões diferentes do Azure.
2. Seu secundário deve ser vazio (nenhum banco de dados do usuário).
3. As instâncias gerenciadas primárias e secundárias precisam estar no mesmo grupo de recursos.
4. O VNets que as instâncias gerenciadas fazem parte do precisam ser conectados por meio de um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). O Emparelhamento VNET Global não é compatível.
5. Os dois VNets de instância gerenciada não podem ter endereços IP sobrepostos.
6. Você precisa configurar seus NSG (grupos de segurança de rede) de modo que as portas 5022 e o intervalo 11000 ~ 12000 sejam abertos de entrada e saída para conexões de outra sub-rede em instância gerenciada. Isso é para permitir o tráfego de replicação entre as instâncias

   > [!IMPORTANT]
   > Regras de segurança de NSG mal configuradas resultam em operações de cópia de banco de dados paralisadas.

7. A instância secundária está configurada com a ID de zona DNS correta. A zona DNS é uma propriedade de uma instância gerenciada e sua ID é incluída no endereço de nome do host. A ID da zona é gerada como uma cadeia de caracteres aleatória quando a primeira instância gerenciada é criada em cada VNet e a mesma ID é atribuída a todas as outras instâncias na mesma sub-rede. Uma vez atribuída, a zona DNS não pode ser modificada. As instâncias gerenciadas incluídas no mesmo grupo de failover devem compartilhar a zona DNS. Isso é feito passando a ID da zona da instância primária como o valor do parâmetro DnsZonePartner ao criar a instância secundária. 

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade de um banco de dados primário

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho da computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize todos os bancos de dados secundários primeiro e, em seguida, atualize o primário. Ao fazer downgrade, inverta o pedido: faça o downgrade do primário primeiro e, em seguida, downgrade todos os bancos de dados secundários. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente essa recomendação é imposta.

Essa sequência é recomendada especificamente para evitar o problema em que o secundário em um SKU inferior é sobrecarregado e deve ser propagado durante um processo de atualização ou de downgrade. Você também pode evitar o problema tornando o primário somente leitura, às custas de afetar todas as cargas de trabalho de leitura/gravação no primário. 

> [!NOTE]
> Se você tiver criado um banco de dados secundário como parte da configuração do grupo de failover não é recomendável fazer o downgrade do banco de dados secundário. Isso é para garantir que sua camada de dados tenha capacidade suficiente para processar sua carga de trabalho normal após o failover ser ativado.

> [!IMPORTANT]
> Atualmente, não há suporte para atualizar ou fazer downgrade de um Instância Gerenciada que é um membro de um grupo de failover.

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a confirmação da transação. Chamar **sp_wait_for_database_copy_sync** bloqueia o thread de chamada até que a última transação confirmada seja transmitida para o banco de dados secundário. Contudo, a chamada não aguarda as transações transmitidas serem reproduzidas e confirmadas no banco de dados secundário. **sp_wait_for_database_copy_sync** é atribuído a um vínculo de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede a perda de dados depois de um failover, mas não garante a sincronização completa para acesso de leitura. A demora causada por uma chamada de procedimento **sp_wait_for_database_copy_sync** pode ser significativa e depende do tamanho do log de transações no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauração pontual

Para obter informações sobre como usar a restauração pontual com grupos de failover, confira a [PITR (recuperação pontual)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Gerenciando os grupos de failover programaticamente

Conforme discutido anteriormente, os grupos de failover automático e a replicação geográfica ativa podem ser gerenciados programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs do Azure Resource Manager para gerenciamento, incluindo a [API REST do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, confira [Controle de Acesso Baseado em Funções do Azure](../role-based-access-control/overview.md).

### <a name="powershell-manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>PowerShell: Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [Remove-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove o grupo de failover do servidor e exclui todos os bancos de dados secundários incluídos no grupo |
| [Get-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração do grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração do grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Dispara o failover do grupo de failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](https://docs.microsoft.com/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona um ou mais bancos de dados a um grupo de failover do Banco de Dados SQL do Azure|
|  | |

> [!IMPORTANT]
> Para um script de exemplo, confira [Configurar e realizar o failover de um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md).
>

### <a name="powershell-managing-failover-groups-with-managed-instances-preview"></a>PowerShell: Gerenciar grupos de failover com instâncias gerenciadas (versão prévia)

#### <a name="install-the-newest-pre-release-version-of-powershell"></a>Instalar a versão de pré-lançamento mais recente do PowerShell

1. Atualize o módulo PowerShellGet para 1.6.5 (ou a versão prévia mais recente). Confira o [site da versão prévia do PowerShell](https://www.powershellgallery.com/packages/AzureRM.Sql/4.11.6-preview).

   ```powershell
      install-module PowerShellGet -MinimumVersion 1.6.5 -force
   ```

2. Em uma nova janela do PowerShell, execute os comandos a seguir:

   ```powershell
      import-module PowerShellGet
      get-module PowerShellGet #verify version is 1.6.5 (or newer)
      install-module azurerm.sql -RequiredVersion 4.5.0-preview -AllowPrerelease –Force
      import-module azurerm.sql
   ```

#### <a name="powershell-commandlets-to-create-an-instance-failover-group"></a>Commandlets do PowerShell para criar um grupo de failover de instância

| API | Descrição |
| --- | --- |
| New-AzureRmSqlDatabaseInstanceFailoverGroup |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| Set-AzureRmSqlDatabaseInstanceFailoverGroup |Modifica a configuração do grupo de failover|
| Get-AzureRmSqlDatabaseInstanceFailoverGroup |Recupera a configuração do grupo de failover|
| Switch-AzureRmSqlDatabaseInstanceFailoverGroup |Dispara o failover do grupo de failover para o servidor secundário|
| Remove-AzureRmSqlDatabaseInstanceFailoverGroup | Remove um grupo de failover|

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST: Gerenciar grupos de failover de Banco de Dados SQL com bancos de dados individuais e em pool

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Failover do servidor principal atual para este servidor. |
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Failover do servidor principal atual para este servidor. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover pelo servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista grupos de failover em um servidor. |
| [Atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza um grupo de failover. |
|  | |

### <a name="rest-api-manage-failover-groups-with-managed-instances-preview"></a>API REST: Gerenciar grupos de failover com instâncias gerenciadas (versão prévia)

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Failover do servidor principal atual para este servidor. |
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Failover do servidor principal atual para este servidor. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover – listar por localização](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover em uma localização. |

## <a name="next-steps"></a>Próximas etapas

- Para exemplos de scripts, consulte:
  - [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Configurar e fazer failover de um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre como usar backups automatizados de recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre requisitos de autenticação para um novo servidor primário e banco de dados, consulte [Segurança do Banco de Dados SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md).
