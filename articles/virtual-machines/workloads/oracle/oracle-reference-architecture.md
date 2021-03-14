---
title: Arquiteturas de referência para bancos de dados Oracle no Azure | Microsoft Docs
description: Faz referência a arquiteturas para executar Oracle Database Enterprise Edition bancos de dados no Máquinas Virtuais do Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.openlocfilehash: 6bce6f011086d9855c4da2739addbb34e661e2d6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507476"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Arquiteturas de referência para Oracle Database Enterprise Edition no Azure

Este guia fornece informações detalhadas sobre como implantar um banco de dados Oracle altamente disponível no Azure. Além disso, este guia se aprofunda em considerações sobre recuperação de desastres. Essas arquiteturas foram criadas com base nas implantações do cliente. Este guia só se aplica a Oracle Database Enterprise Edition.

Se você estiver interessado em aprender mais sobre a maximização do desempenho do seu banco de dados Oracle, consulte [arquitetar um Oracle DB](oracle-design.md).

## <a name="assumptions"></a>Suposições

- Você tem uma compreensão dos diferentes conceitos do Azure, como [zonas de disponibilidade](../../../availability-zones/az-overview.md)
- Você está executando o Oracle Database Enterprise Edition 12c ou posterior
- Você está ciente e reconhece as implicações de licenciamento ao usar as soluções neste artigo

## <a name="high-availability-for-oracle-databases"></a>Alta disponibilidade para bancos de dados Oracle

A obtenção de alta disponibilidade na nuvem é uma parte importante do planejamento e do design de cada organização. O Microsoft Azure oferece [zonas de disponibilidade](../../../availability-zones/az-overview.md) e conjuntos de disponibilidade (para serem usados em regiões em que as zonas de disponibilidade estão indisponíveis). Leia mais sobre como [gerenciar a disponibilidade de suas máquinas virtuais](../../availability.md) para projetar para a nuvem.

Além das ferramentas e ofertas nativas de nuvem, a Oracle fornece soluções para alta disponibilidade, como [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [Data Guard com FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [fragmentação](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)e [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) que podem ser configurados no Azure. Este guia aborda as arquiteturas de referência para cada uma dessas soluções.

Por fim, ao migrar ou criar aplicativos para a nuvem, é importante ajustar o código do aplicativo para adicionar padrões nativos de nuvem, como [padrão de repetição](/azure/architecture/patterns/retry) e padrão de [disjuntor](/azure/architecture/patterns/circuit-breaker). Padrões adicionais definidos no [Guia de padrões de design de nuvem](/azure/architecture/patterns/) podem ajudar seu aplicativo a ser mais resiliente.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC na nuvem

O Oracle Real Application Cluster (RAC) é uma solução da Oracle para ajudar os clientes a alcançarem altas taxas de transferência, tendo muitas instâncias acessando um armazenamento de banco de dados (padrão de arquitetura compartilhada-todos). Embora o Oracle RAC também possa ser usado para alta disponibilidade local, o Oracle RAC sozinho não pode ser usado para alta disponibilidade na nuvem, pois protege apenas contra falhas em nível de instância e não contra falhas em nível de rack ou de data center. Por esse motivo, a Oracle recomenda o uso do Oracle Data Guard com seu banco de dados (seja única instância ou RAC) para alta disponibilidade. Os clientes geralmente exigem um alto SLA para executar seus aplicativos críticos. No momento, o Oracle RAC não é certificado ou tem suporte do Oracle no Azure. No entanto, o Azure oferece recursos como o Azure oferece Zonas de Disponibilidade e janelas de manutenção planejada para ajudar a proteger contra falhas em nível de instância. Além disso, os clientes podem usar tecnologias como Oracle Data Guard, Oracle GoldenGate e fragmentação Oracle para alto desempenho e resiliência, protegendo seus bancos de dados de nível de rack, bem como falhas de nível de datacenter e de políticas geográficas.

Ao executar bancos de dados Oracle em várias [zonas de disponibilidade](../../../availability-zones/az-overview.md) em conjunto com o Oracle Data Guard ou o GoldenGate, os clientes são capazes de obter um SLA de tempo de atividade de 99,99%. Em regiões do Azure em que as zonas de disponibilidade ainda não estão presentes, os clientes podem usar [conjuntos de disponibilidade](../../availability-set-overview.md) e obter um SLA de tempo de atividade de 99,95%.

>Observação: você pode ter um destino de tempo de atividade muito maior do que o SLA de tempo de atividade fornecido pela Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Recuperação de desastre para bancos de dados Oracle

Ao hospedar seus aplicativos de missão crítica na nuvem, é importante projetar para alta disponibilidade e recuperação de desastres.

Por Oracle Database Enterprise Edition, o Oracle Data Guard é um recurso útil para a recuperação de desastres. Você pode configurar uma instância de banco de dados em espera em uma [região emparelhada do Azure](../../../best-practices-availability-paired-regions.md) e configurar o failover do Data Guard para recuperação de desastre. Para perda de dados zero, é recomendável que você implante uma instância de sincronização distante do Oracle Data Guard além do Active Data Guard. 

Considere configurar a instância de sincronização distante do Data Guard em uma zona de disponibilidade diferente de seu banco de dados primário do Oracle se seu aplicativo permitir a latência (é necessário um teste completo). Use um modo de **disponibilidade máximo** para configurar o transporte síncrono de seus arquivos redo para a instância de sincronização distante. Esses arquivos são transferidos assincronamente para o banco de dados em espera. 

Se seu aplicativo não permitir a perda de desempenho ao configurar a instância de sincronização distante em outra zona de disponibilidade no modo de **disponibilidade máximo** (síncrono), você poderá configurar uma instância de sincronização distante na mesma zona de disponibilidade que o banco de dados primário. Para obter disponibilidade adicional, considere configurar várias instâncias de sincronização distantes próximas ao seu banco de dados primário e pelo menos uma instância próxima ao seu banco de dados em espera (se a função estiver em transição). Leia mais sobre a sincronização distante do Oracle Data Guard neste [White paper sobre a sincronização do Oracle active Data Guard no momento](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Ao usar bancos de dados Oracle Standard Edition, há soluções de ISV, como DBVisit standby, que permitem que você configure alta disponibilidade e recuperação de desastres.

## <a name="reference-architectures"></a>Arquiteturas de referência

### <a name="oracle-data-guard"></a>Oracle Data Guard

O Oracle Data Guard garante alta disponibilidade, proteção de dados e recuperação de desastre para dados corporativos. O Data Guard mantém os bancos de dados em espera como cópias transacionalmente consistentes do banco de dados primário. Dependendo da distância entre os bancos de dados primário e secundário e a tolerância do aplicativo para latência, você pode configurar a replicação síncrona ou assíncrona. Em seguida, se o banco de dados primário não estiver disponível devido a uma interrupção planejada ou não planejada, o Data Guard poderá alternar qualquer banco de dados em espera para a função primária, minimizando o tempo de inatividade. 

Ao usar o Oracle Data Guard, você também pode abrir seu banco de dados secundário para fins somente leitura. Essa configuração é chamada active Data Guard. Oracle Database 12c introduziu um recurso chamado instância de sincronização distante do Data Guard. Essa instância permite que você defina uma configuração de perda de dados sem zero do seu Oracle Database sem a necessidade de comprometer o desempenho.

> [!NOTE]
> O Active Data Guard requer licenciamento adicional. Essa licença também é necessária para usar o recurso de sincronização distante. Conecte-se com seu representante da Oracle para discutir as implicações de licenciamento.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard com FSFO
O Oracle Data Guard com failover de Fast-Start (FSFO) pode fornecer resiliência adicional Configurando o agente em um computador separado. O agente do Data Guard e o banco de dados secundário executam o observador e observam o banco de dados primário quanto ao tempo de inatividade. Isso também permite a redundância na configuração do observador do Data Guard. 

Com Oracle Database versão 12,2 e superior, também é possível configurar vários observadores com uma única configuração do agente do Oracle Data Guard. Essa configuração fornece disponibilidade adicional, no caso de um observador e o tempo de inatividade secundário da experiência do banco de dados. O agente do Data Guard é leve e pode ser hospedado em uma máquina virtual relativamente pequena. Para saber mais sobre o agente do Data Guard e suas vantagens, visite a [documentação do Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) sobre este tópico.

O diagrama a seguir é uma arquitetura recomendada para usar o Oracle Data Guard no Azure com zonas de disponibilidade. Essa arquitetura permite que você obtenha um SLA de tempo de atividade de VM de 99,99%.

![Diagrama que mostra uma arquitetura recomendada para usar o Oracle Data Guard no Azure com zonas de disponibilidade.](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

No diagrama anterior, o sistema cliente acessa um aplicativo personalizado com o back-end da Oracle por meio da Web. O front-end da Web é configurado em um balanceador de carga. O front-end da Web faz uma chamada para o servidor de aplicativos apropriado para lidar com o trabalho. O servidor de aplicativos consulta o banco de dados Oracle primário. O banco de dados Oracle foi configurado usando uma [máquina virtual com memória](../../sizes-memory.md) de hiperthread otimizada com [vCPUs básica restritas](../../../virtual-machines/constrained-vcpu.md) para economizar nos custos de licenciamento e maximizar o desempenho. Vários discos Premium ou ultra (Managed Disks) são usados para desempenho e alta disponibilidade.

Os bancos de dados Oracle são colocados em várias zonas de disponibilidade para alta disponibilidade. Cada zona é composta por um ou mais data centers equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, no mínimo três zonas separadas são configuradas em todas as regiões habilitadas. A separação física das zonas de disponibilidade em uma região protege os dados contra falhas data centers. Além disso, dois observadores FSFO são configurados em duas zonas de disponibilidade para iniciar e fazer failover do banco de dados para o secundário quando ocorre uma interrupção. 

Você pode configurar observadores adicionais e/ou bancos de dados em espera em uma zona de disponibilidade diferente (AZ 1, nesse caso) do que a zona mostrada na arquitetura anterior. Por fim, os bancos de dados Oracle são monitorados quanto ao tempo de atividade e ao desempenho do Oracle Enterprise Manager (OEM). O OEM também permite que você gere vários relatórios de desempenho e uso.

Em regiões em que não há suporte para zonas de disponibilidade, você pode usar conjuntos de disponibilidade para implantar seu Oracle Database de maneira altamente disponível. Os conjuntos de disponibilidade permitem que você alcance um tempo de atividade de VM de 99,95%. O diagrama a seguir é uma arquitetura de referência desse uso:

![Oracle Database usando conjuntos de disponibilidade com o agente do Data Guard-FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * A VM do Oracle Enterprise Manager não precisa ser colocada em um conjunto de disponibilidade, pois há apenas uma instância do OEM que está sendo implantada.
> * No momento, não há suporte para ultra discos em uma configuração de conjunto de disponibilidade.

#### <a name="oracle-data-guard-far-sync"></a>Sincronização distante do Oracle Data Guard

A sincronização distante do Oracle Data Guard fornece zero recurso de proteção contra perda de dados para bancos de dados Oracle. Essa funcionalidade permite que você se proteja contra a perda de dados no se o computador do banco de dados falhar. A sincronização distante do Oracle Data Guard precisa ser instalada em uma VM separada. A sincronização distante é uma instância do Oracle leve que tem apenas um arquivo de controle, arquivo de senha, SPFile e logs em espera. Não há arquivos de dados ou arquivos de log rego. 

Para uma proteção sem perda de dados, deve haver comunicação síncrona entre o banco de dado primário e a instância de sincronização distante. A instância de sincronização distante recebe a refazer do primário de maneira síncrona e a encaminha imediatamente para todos os bancos de dados em espera de maneira assíncrona. Essa configuração também reduz a sobrecarga no banco de dados primário, pois ele só precisa enviar a refazer para a instância de sincronização distante, em vez de todos os bancos de dados em espera. Se uma instância de sincronização distante falhar, o Data Guard usará automaticamente o transporte assíncrono para o banco de dados secundário do banco de dados primário para manter a proteção de perda de dados quase zero. Para resiliência adicional, os clientes podem implantar várias instâncias de sincronização distantes por cada instância de banco de dados (primária e secundária).

O diagrama a seguir é uma arquitetura de alta disponibilidade que usa a sincronização distante do Oracle Data Guard:

![Banco de dados Oracle usando zonas de disponibilidade com Data Guard de sincronização distante & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

Na arquitetura anterior, há uma instância de sincronização distante implantada na mesma zona de disponibilidade que a instância do banco de dados para reduzir a latência entre as duas. Nos casos em que o aplicativo é sensível à latência, considere implantar o banco de dados e a instância ou instâncias de sincronização distante em um [grupo de posicionamento de proximidade](../../../virtual-machines/linux/proximity-placement-groups.md).

O diagrama a seguir é uma arquitetura que utiliza o Oracle Data Guard FSFO e a sincronização distante para alcançar alta disponibilidade e recuperação de desastres:

![Oracle Database usando zonas de disponibilidade para recuperação de desastre com o Data Guard & de sincronização distante do agente-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

O GoldenGate permite a troca e a manipulação de dados no nível de transação entre várias plataformas heterogêneas em toda a empresa. Ele move transações confirmadas com integridade de transação e sobrecarga mínima em sua infraestrutura existente. Sua arquitetura modular oferece a flexibilidade para extrair e replicar registros de dados selecionados, alterações transacionais e alterações em DDL (linguagem de definição de dados) em uma variedade de topologias.

O Oracle GoldenGate permite que você configure seu banco de dados para alta disponibilidade fornecendo replicação bidirecional. Isso permite que você configure uma configuração de **vários mestres** ou **ativos-ativos**. O diagrama a seguir é uma arquitetura recomendada para a instalação do Oracle GoldenGate Active-Active no Azure. Na arquitetura a seguir, o banco de dados Oracle foi configurado usando uma [máquina virtual com memória](../../sizes-memory.md) de hiperthread otimizada com [vCPUs básica restrita](../../../virtual-machines/constrained-vcpu.md) para economizar nos custos de licenciamento e maximizar o desempenho. Vários discos Premium ou ultra (discos gerenciados) são usados para desempenho e disponibilidade.

![Oracle Database usando zonas de disponibilidade com o agente do Data Guard-FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Uma arquitetura semelhante pode ser configurada usando conjuntos de disponibilidade em regiões em que as zonas de disponibilidade não estão disponíveis no momento.

O Oracle GoldenGate tem processos como extrair, bombear e replicar que ajudam a replicar de forma assíncrona seus dados de um servidor de banco de dados Oracle para outro. Esses processos permitem que você configure uma replicação bidirecional para garantir a alta disponibilidade do seu banco de dados se houver tempo de inatividade no nível de zona de disponibilidade. No diagrama anterior, o processo de extração é executado no mesmo servidor que o banco de dados Oracle, enquanto que os processos de bombeamento e replicação são executados em um servidor separado na mesma zona de disponibilidade. O processo de replicação é usado para receber dados do banco de dado em outra zona de disponibilidade e para confirmar os dados para o Oracle Database em sua zona de disponibilidade. Da mesma forma, o processo de bombeamento de dados envia dados que foram extraídos pelo processo de extração para o processo de replicação na outra zona de disponibilidade. 

Embora o diagrama de arquitetura anterior mostre o processo de bombeamento e replicação de dados configurado em um servidor separado, você pode configurar todos os processos do Oracle GoldenGate no mesmo servidor, com base na capacidade e no uso do servidor. Sempre consulte seu relatório AWR e as métricas no Azure para entender o padrão de uso do seu servidor.

Ao configurar a replicação bidirecional do Oracle GoldenGate em diferentes zonas de disponibilidade ou regiões diferentes, é importante garantir que a latência entre os diferentes componentes seja aceitável para seu aplicativo. A latência entre zonas de disponibilidade e regiões pode variar e depende de vários fatores. É recomendável que você configure testes de desempenho entre a camada de aplicativo e a camada de banco de dados em diferentes zonas de disponibilidade e/ou regiões para confirmar se atende aos requisitos de desempenho do aplicativo.

A camada de aplicativo pode ser configurada em sua própria sub-rede e a camada de banco de dados pode ser separada em sua própria sub-rede. Quando possível, considere o uso de [aplicativo Azure gateway](../../../application-gateway/overview.md) para balancear a carga do tráfego entre os servidores de aplicativos. Aplicativo Azure gateway é um balanceador de carga de tráfego da Web robusto. Ele fornece afinidade de sessão baseada em cookie que mantém uma sessão de usuário no mesmo servidor, minimizando assim os conflitos no banco de dados. As alternativas para o gateway de aplicativo são [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) e o [Gerenciador de tráfego do Azure](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Fragmentação do Oracle

A fragmentação é um padrão de camada de dados que foi introduzido no Oracle 12,2. Ele permite que você particione horizontalmente e dimensione seus dados em bancos de dado independentes. É uma arquitetura de nada de compartilhamento em que cada banco de dados é hospedado em uma máquina virtual dedicada, o que permite alta taxa de transferência de leitura e gravação, além de resiliência e maior disponibilidade. Esse padrão elimina pontos únicos de falha, fornece isolamento de falha e habilita as atualizações sem tempo de inatividade. O tempo de inatividade de um fragmento ou uma falha de nível de data center não afeta o desempenho ou a disponibilidade dos outros fragmentos em outros data centers. 

A fragmentação é adequada para aplicativos OLTP de alta produtividade que não podem arcar com nenhum tempo de inatividade. Todas as linhas com a mesma chave de fragmentação sempre têm a garantia de estar no mesmo fragmento, o que aumenta o desempenho, proporcionando alta consistência. Os aplicativos que usam a fragmentação devem ter uma estratégia de distribuição de dados e um modelo de dados bem definido (hash consistente, intervalo, lista ou composto) que acessam principalmente os dados usando uma chave de fragmentação (por exemplo, *CustomerID* ou *accountNum*). A fragmentação também permite que você armazene conjuntos específicos de dados mais próximos dos clientes finais, ajudando-o a atender aos seus requisitos de desempenho e conformidade.

É recomendável que você replique seus fragmentos para alta disponibilidade e recuperação de desastre. Essa configuração pode ser feita usando tecnologias Oracle, como Oracle Data Guard ou Oracle GoldenGate. Uma unidade de replicação pode ser um fragmento, uma parte de um fragmento ou um grupo de fragmentos. A disponibilidade de um banco de dados fragmentado não é afetada por uma interrupção ou lentidão de um ou mais fragmentos. Para alta disponibilidade, os fragmentos em espera podem ser colocados na mesma zona de disponibilidade em que os fragmentos primários são colocados. Para a recuperação de desastres, os fragmentos em espera podem estar localizados em outra região. Você também pode implantar fragmentos em várias regiões para atender ao tráfego nessas regiões. Leia mais sobre como configurar a alta disponibilidade e a replicação do banco de dados fragmentado na [documentação de fragmentação Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

A fragmentação do Oracle consiste principalmente nos seguintes componentes. Mais informações sobre esses componentes podem ser encontradas na [documentação de fragmentação do Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Catálogo de fragmento** -banco de dados Oracle de finalidade especial que é um repositório persistente para todos os data de configuração do banco de dado do fragmento. Todas as alterações de configuração, como adicionar ou remover fragmentos, o mapeamento dos dados e DDLs em um banco de dados de fragmento, são iniciadas no catálogo de fragmentos. O catálogo de fragmentos também contém a cópia mestra de todas as tabelas duplicadas em um SDB. 

  O catálogo de fragmentos usa exibições materializadas para replicar automaticamente as alterações em tabelas duplicadas em todos os fragmentos. O banco de dados do catálogo de fragmentos também atua como um coordenador de consultas usado para processar consultas de vários fragmentos e consultas que não especificam uma chave de fragmentação. 
  
  O uso do Oracle Data Guard em conjunto com zonas de disponibilidade ou conjuntos de disponibilidade para alta disponibilidade do catálogo de fragmentos é uma prática recomendada. A disponibilidade do catálogo de fragmentos não tem impacto sobre a disponibilidade do banco de dados fragmentado. Um tempo de inatividade no catálogo de fragmentos afeta apenas as operações de manutenção e as consultas de multifragmento durante o breve período em que o failover do Data Guard é concluído. As transações online continuam a ser roteadas e executadas pelo SDB e não são afetadas por uma interrupção do catálogo.

- **Diretores de fragmentos** -serviços leves que precisam ser implantados em cada região/zona de disponibilidade em que seus fragmentos residem. Os diretores de fragmentos são gerentes de serviço globais implantados no contexto da fragmentação Oracle. Para alta disponibilidade, é recomendável que você implante pelo menos um diretor de fragmentos em cada zona de disponibilidade em que seus fragmentos existem. 

  Ao se conectar ao banco de dados inicialmente, as informações de roteamento são configuradas por um diretor de fragmentos e armazenadas em cache para solicitações subsequentes, ignorando o diretor de fragmentos. Depois que a sessão é estabelecida com um fragmento, todas as consultas SQL e DMLs têm suporte e são executadas no escopo do fragmento fornecido. Esse roteamento é rápido e é usado para todas as cargas de trabalho OLTP que executam transações de fragmento interno. É recomendável usar o roteamento direto para todas as cargas de trabalho OLTP que exigem o melhor desempenho e disponibilidade. O cache de roteamento é atualizado automaticamente quando um fragmento se torna indisponível ou ocorrem alterações na topologia de fragmentação. 
  
  Para um roteamento dependente de dados de alto desempenho, a Oracle recomenda usar um pool de conexões ao acessar dados no banco de dado fragmentado. Pools de conexão Oracle, bibliotecas específicas de idioma e drivers dão suporte a fragmentação Oracle. Consulte a [documentação de fragmentação do Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) para obter mais detalhes.

- **Serviço global** -o serviço global é semelhante ao serviço de banco de dados regular. Além de todas as propriedades de um serviço de banco de dados, um serviço global tem propriedades para bancos de dados fragmentados, como afinidade de região entre clientes, fragmentação e tolerância de atraso de replicação. Somente um serviço global precisa ser criado para ler/gravar dados de/para um banco de dado fragmentado. Ao usar o Active Data Guard e configurar réplicas somente leitura dos fragmentos, você pode criar outro serviço gGobal para cargas de trabalho somente leitura. O cliente pode usar esses serviços globais para se conectar ao banco de dados.

- **Bancos de dados de fragmentos** -os bancos de dados de fragmentos são seus bancos de dados Oracle. Cada banco de dados é replicado usando o Oracle Data Guard em uma configuração de agente com failover de Fast-Start (FSFO) habilitado. Você não precisa configurar o failover do Data Guard e a replicação em cada fragmento. Isso é automaticamente configurado e implantado quando o banco de dados compartilhado é criado. Se um fragmento específico falhar, o compartilhamento Oracle fará failover automaticamente das conexões de banco de dados do primário para o em espera.

Você pode implantar e gerenciar bancos de dados fragmentados da Oracle com duas interfaces: GUI do controle de nuvem do Oracle Enterprise Manager e/ou o `GDSCTL` Utilitário de linha de comando. Você pode até monitorar os diferentes fragmentos para disponibilidade e desempenho usando o controle de nuvem. O `GDSCTL DEPLOY` comando cria automaticamente os fragmentos e seus respectivos ouvintes. Além disso, esse comando implanta automaticamente a configuração de replicação usada para alta disponibilidade no nível de fragmento especificada pelo administrador.

Há diferentes maneiras de fragmentar um banco de dados:

* Fragmentação gerenciada pelo sistema – distribui automaticamente entre fragmentos usando o particionamento
* Fragmentação definida pelo usuário – permite que você especifique o mapeamento dos dados para os fragmentos, o que funciona bem quando há requisitos regulatórios ou de localização de dados)
* Fragmentação de composição-uma combinação de fragmentação gerenciada pelo sistema e definida pelo usuário para diferentes _shardspaces_
* Subpartições de tabela – semelhante a uma tabela particionada regular.

Leia mais sobre os diferentes [métodos de fragmentação](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) na documentação da Oracle.

Embora um banco de dados fragmentado possa parecer um banco de dados individual para aplicativos e desenvolvedores, ao migrar de um banco de dados não fragmentado para um banco de dados fragmentado, é necessário um planejamento cuidadoso para determinar quais tabelas serão duplicadas versus fragmentadas. 

As tabelas duplicadas são armazenadas em todos os fragmentos, enquanto as tabelas fragmentadas são distribuídas entre diferentes fragmentos. A recomendação é duplicar tabelas pequenas e dimensionais e distribuir/fragmentar as tabelas de fatos. Os dados podem ser carregados em seu banco de dado fragmentado usando o catálogo de fragmentos como o coordenador central ou executando a bomba de dados em cada fragmento. Leia mais sobre [a migração de dados para um banco de dado fragmentado](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) na documentação da Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Fragmentação do Oracle com o Data Guard

O Oracle Data Guard pode ser usado para fragmentação com métodos gerenciados pelo sistema, definidos pelo usuário e pela fragmentação de composição.

O diagrama a seguir é uma arquitetura de referência para a fragmentação do Oracle com o Oracle Data Guard usado para alta disponibilidade de cada fragmento. O diagrama de arquitetura mostra um _método de fragmentação composto_. O diagrama de arquitetura provavelmente será diferente para aplicativos com requisitos diferentes para localidade de dados, balanceamento de carga, alta disponibilidade, recuperação de desastres etc. e pode usar um método diferente para fragmentação. A fragmentação Oracle permite que você atenda a esses requisitos e dimensione horizontalmente e com eficiência fornecendo essas opções. Uma arquitetura semelhante pode até mesmo ser implantada usando o Oracle GoldenGate.

![Fragmentação de Oracle Database usando zonas de disponibilidade com o agente do Data Guard-FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Embora a fragmentação gerenciada pelo sistema seja a mais fácil de configurar e gerenciar, a fragmentação definida pelo usuário ou a fragmentação composta é adequada para cenários em que seus dados e aplicativos são distribuídos geograficamente ou em cenários em que você precisa ter controle sobre a replicação de cada fragmento. 

Na arquitetura anterior, a fragmentação de composição é usada para distribuir geograficamente os dados e expandir horizontalmente suas camadas de aplicativo. A fragmentação de composição é uma combinação de fragmentação gerenciada pelo sistema e pelo usuário e, portanto, fornece o benefício de ambos os métodos. No cenário anterior, os dados são fragmentados primeiro em vários shardspaces separados por região. Em seguida, os dados são particionados mais detalhadamente pelo hash consistente em vários fragmentos no shardspace. Cada shardspace contém vários shardgroups. Cada um dos fragmentos tem vários fragmentos e é uma "unidade" de replicação, nesse caso. Cada um deles contém todos os dados no shardspace. Shardgroups a1 e B1 são as Shardgroups primárias, enquanto Shardgroups a2 e B2 são em espera. Você pode optar por ter fragmentos individuais como a unidade de replicação, em vez de um de fragmento.

Na arquitetura anterior, um diretor de GSM/fragmento é implantado em cada zona de disponibilidade para alta disponibilidade. A recomendação é implantar pelo menos um diretor de GSM/fragmento por data center/região. Além disso, uma instância do servidor de aplicativos é implantada em cada zona de disponibilidade que contém um availabilitygroup. Essa configuração permite que o aplicativo mantenha a latência entre o servidor de aplicativos e o banco de dados/o nível de fragmento baixo. Se um banco de dados falhar, o servidor de aplicativos na mesma zona que o banco de dados em espera poderá lidar com solicitações depois que a transição da função de banco de dados ocorrer. Aplicativo Azure gateway e o diretor de fragmentos controlam a latência da solicitação e da resposta e as solicitações de rota de acordo.

Do ponto de vista de um aplicativo, o sistema cliente faz uma solicitação para Aplicativo Azure gateway (ou outras tecnologias de balanceamento de carga no Azure) que redireciona a solicitação para a região mais próxima do cliente. Aplicativo Azure gateway também dá suporte a sessões adesivas, portanto, todas as solicitações provenientes do mesmo cliente são roteadas para o mesmo servidor de aplicativos. O servidor de aplicativos usa o pool de conexões em drivers de acesso a dados. Esse recurso está disponível em drivers como JDBC, ODP.NET, OCI, etc. Os drivers podem reconhecer chaves de fragmentação especificadas como parte da solicitação. O [UCP (pool de conexões universal) da Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicativos não Oracle, como o Apache Tomcat e o IIS, trabalhem com a fragmentação Oracle. 

Durante a solicitação inicial, o servidor de aplicativos se conecta ao diretor de fragmentos em sua região para obter informações de roteamento para o fragmento ao qual a solicitação precisa ser roteada. Com base na chave de fragmentação passada, o diretor roteia o servidor de aplicativos para o respectivo fragmento. O servidor de aplicativos armazena essas informações em cache criando um mapa e, para solicitações subsequentes, ignora o diretor de fragmentos e roteia as solicitações diretamente para o fragmento.

#### <a name="oracle-sharding-with-goldengate"></a>Fragmentação do Oracle com o GoldenGate

O diagrama a seguir é uma arquitetura de referência para a fragmentação do Oracle com o Oracle GoldenGate para alta disponibilidade em região de cada fragmento. Em oposição à arquitetura anterior, essa arquitetura apenas retrata a alta disponibilidade em uma única região do Azure (várias zonas de disponibilidade). Uma delas poderia implantar um banco de dados fragmentado de alta disponibilidade de várias regiões (semelhante ao exemplo anterior) usando o Oracle GoldenGate.

![Fragmentação de Oracle Database usando zonas de disponibilidade com GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

A arquitetura de referência anterior usa o método de fragmentação _gerenciado pelo sistema_ para fragmentar os dados. Como a replicação do Oracle GoldenGate é feita em um nível de parte, metade os dados replicados para um fragmento podem ser replicados para outro fragmento. A outra metade pode ser replicada para um fragmento diferente. 

A maneira como os dados são replicados depende do fator de replicação. Com um fator de replicação de 2, você terá duas cópias de cada parte dos dados em seus três fragmentos no agrupamento. Da mesma forma, com um fator de replicação de 3 e três fragmentos em seu The Fragment, todos os dados em cada fragmento serão replicados para todos os outros fragmentos no The Fragment. Cada fragmento no The fragmento pode ter um fator de replicação diferente. Essa configuração ajuda você a definir seu design de alta disponibilidade e recuperação de desastres com eficiência em um shardgroups e em vários locais.

Na arquitetura anterior, o availabilitygroup A e o availabilitygroup B contêm os mesmos dados, mas residem em diferentes zonas de disponibilidade. Se ambos os grupos de fragmentos A e de fragmentos B tiverem o mesmo fator de replicação de 3, cada linha/bloco da tabela fragmentada será replicada 6 vezes em todos os dois shardgroups. Se o shardgroups de fragmentos A tiver um fator de replicação de 3 e o fragmento B tiver um fator de replicação de 2, cada linha/bloco será replicada cinco vezes entre os dois.

Essa configuração impede a perda de dados se ocorrer uma falha em nível de instância ou de zona de disponibilidade. A camada de aplicativo é capaz de ler e gravar em cada fragmento. Para minimizar conflitos, a fragmentação Oracle designa uma "parte mestre" para cada intervalo de valores de hash. Esse recurso garante que as solicitações de gravação para uma determinada parte sejam direcionadas para a parte correspondente. Além disso, o Oracle GoldenGate fornece detecção e resolução de conflitos automáticas para lidar com quaisquer conflitos que possam surgir. Para obter mais informações e limitações de implementação do GoldenGate com a fragmentação do Oracle, consulte a documentação da Oracle sobre como usar [o Oracle GoldenGate com um banco de dados fragmentado](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

Na arquitetura anterior, um diretor de GSM/fragmento é implantado em cada zona de disponibilidade para alta disponibilidade. A recomendação é implantar pelo menos um diretor de GSM/fragmento por data center ou região. Além disso, uma instância do servidor de aplicativos é implantada em cada zona de disponibilidade que contém um availabilitygroup. Essa configuração permite que o aplicativo mantenha a latência entre o servidor de aplicativos e o banco de dados/o nível de fragmento baixo. Se um banco de dados falhar, o servidor de aplicativos na mesma zona que o banco de dados em espera poderá lidar com solicitações após a transição da função de banco de dados. Aplicativo Azure gateway e o diretor de fragmentos controlam a latência da solicitação e da resposta e as solicitações de rota de acordo.

Do ponto de vista de um aplicativo, o sistema cliente faz uma solicitação para Aplicativo Azure gateway (ou outras tecnologias de balanceamento de carga no Azure) que redireciona a solicitação para a região mais próxima do cliente. Aplicativo Azure gateway também dá suporte a sessões adesivas, portanto, todas as solicitações provenientes do mesmo cliente são roteadas para o mesmo servidor de aplicativos. O servidor de aplicativos usa o pool de conexões em drivers de acesso a dados. Esse recurso está disponível em drivers como JDBC, ODP.NET, OCI, etc. Os drivers podem reconhecer chaves de fragmentação especificadas como parte da solicitação. O [UCP (pool de conexões universal) da Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) para clientes JDBC pode permitir que clientes de aplicativos não Oracle, como o Apache Tomcat e o IIS, trabalhem com a fragmentação Oracle. 

Durante a solicitação inicial, o servidor de aplicativos se conecta ao diretor de fragmentos em sua região para obter informações de roteamento para o fragmento ao qual a solicitação precisa ser roteada. Com base na chave de fragmentação passada, o diretor roteia o servidor de aplicativos para o respectivo fragmento. O servidor de aplicativos armazena essas informações em cache criando um mapa e, para solicitações subsequentes, ignora o diretor de fragmentos e roteia as solicitações diretamente para o fragmento.

## <a name="patching-and-maintenance"></a>Aplicação de patch e manutenção

Ao implantar suas cargas de trabalho do Oracle no Azure, a Microsoft cuida de toda a aplicação de patch no nível do sistema operacional do host. Qualquer manutenção planejada no nível do sistema operacional é comunicada aos clientes com antecedência para permitir que o cliente tenha essa manutenção planejada. Dois servidores de duas Zonas de Disponibilidade diferentes nunca são corrigidos simultaneamente. Consulte [gerenciar a disponibilidade de máquinas virtuais](../../availability.md) para obter mais detalhes sobre a manutenção da VM e aplicação de patches. 

A aplicação de patch no sistema operacional da máquina virtual pode ser automatizada usando a [Gerenciamento de atualizações de automação do Azure](../../../automation/update-management/overview.md). Aplicar patches e manter seu banco de dados Oracle pode ser automatizado e agendado usando [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) ou a [automação do Azure gerenciamento de atualizações](../../../automation/update-management/overview.md) para minimizar o tempo de inatividade. Veja [entrega contínua e implantações azuis/verdes](/azure/devops/learn/what-is-continuous-delivery) para entender como elas podem ser usadas no contexto de seus bancos de dados Oracle.

## <a name="architecture-and-design-considerations"></a>Considerações sobre arquitetura e design

- Considere o uso da [máquina virtual com memória](../../sizes-memory.md) de hiperthread otimizada com [vCPUs básica restrita](../../../virtual-machines/constrained-vcpu.md) para sua VM Oracle Database para economizar nos custos de licenciamento e maximizar o desempenho. Use vários discos Premium ou ultra (discos gerenciados) para desempenho e disponibilidade.
- Ao usar discos gerenciados, o nome do disco/dispositivo pode ser alterado em reinicializações. É recomendável que você use o UUID do dispositivo em vez do nome para garantir que as montagens persistam entre as reinicializações. Encontre mais informações [aqui](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Use zonas de disponibilidade para obter alta disponibilidade na região.
- Considere o uso de discos ultra (quando disponíveis) ou Premium para seu banco de dados Oracle.
- Considere configurar um banco de dados Oracle em espera em outra região do Azure usando o Oracle Data Guard.
- Considere o uso de [grupos de posicionamento de proximidade](../../co-location.md#proximity-placement-groups) para reduzir a latência entre o aplicativo e a camada de banco de dados.
- Configure o [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) para gerenciamento, monitoramento e registro em log.
- Considere o uso do ASM (gerenciamento automático de armazenamento) da Oracle para o gerenciamento de armazenamento simplificado para seu banco de dados.
- Use [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) para gerenciar patches e atualizações para seu banco de dados sem nenhum tempo de inatividade.
- Ajuste o código do aplicativo para adicionar padrões nativos de nuvem, como [padrão de repetição](/azure/architecture/patterns/retry), padrão de [disjuntor](/azure/architecture/patterns/circuit-breaker)e outros padrões definidos no [Guia de padrões de design de nuvem](/azure/architecture/patterns/) que podem ajudar seu aplicativo a ser mais resiliente.

## <a name="next-steps"></a>Próximas etapas

Examine os seguintes artigos de referência do Oracle que se aplicam ao seu cenário.

- [Introdução ao Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Conceitos do agente do Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Configurando o Oracle GoldenGate para Active-Active alta disponibilidade](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Visão geral da fragmentação do Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [A Oracle active Data Guard está sincronizada com zero perda de dados a qualquer distância](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
