---
title: "Criar um serviço altamente disponível usando o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre a criação de um aplicativo para serviços altamente disponíveis usando o Banco de Dados SQL do Azure."
keywords: "recuperação de desastre em nuvem, soluções de recuperação de desastre, backup de dados de aplicativo, replicação geográfica, planejamento de continuidade de negócios"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 09/08/2017
ms.author: sashan
ms.openlocfilehash: 5a8b7711d6576edcc470886f27aa61ac04944002
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Criar serviços altamente disponíveis usando o Banco de Dados SQL do Azure

Ao criar e implantar serviços altamente disponíveis no Banco de Dados SQL do Azure, use [grupos de failover e a replicação geográfica ativa](sql-database-geo-replication-overview.md) para fornecer resiliência a interrupções regionais e falhas catastróficas. Ela também permite a recuperação rápida em bancos de dados secundários. Este artigo tem como foco os padrões comuns de aplicativos e aborda as vantagens e desvantagens de cada opção. Para obter informações sobre o uso da replicação geográfica ativa com Pools elásticos, confira [Estratégias de recuperação de desastres do pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Cenário 1: Usando duas regiões do Azure para continuidade dos negócios com tempo de inatividade mínimo
Neste cenário, os aplicativos têm as seguintes características: 
*   O aplicativo está ativo em uma região do Azure
*   Todas as sessões do banco de dados exigem acesso de leitura e gravação (RW) nos dados
*   A camada da Web e a camada de dados devem estar colocalizadas para reduzir a latência e o custo de tráfego 
*   Essencialmente, o tempo de inatividade é um risco maior de negócios para esses aplicativos do que a perda de dados

Nesse caso, a topologia de implantação do aplicativo é otimizada para lidar com desastres regionais quando todos os componentes do aplicativo precisam fazer failover juntos. O diagrama abaixo mostra essa topologia. Para redundância geográfica, os recursos do aplicativo são implantados nas Regiões A e B. No entanto, os recursos da Região B não são utilizados até que a Região A falhe. Um grupo de failover é configurado entre as duas regiões para gerenciar o failover, a replicação e a conectividade do banco de dados. O serviço Web em ambas as regiões é configurado para acessar o banco de dados por meio do ouvinte de leitura/gravação **&lt;failover-group-name&gt;.database.windows.net** (1). O gerenciador de tráfego é configurado para usar o [método de roteamento de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> O [gerenciador de tráfego do ](../traffic-manager/traffic-manager-overview.md) é usado em todo este artigo apenas para fins ilustrativos. Você pode usar qualquer solução de balanceamento de carga que dê suporte ao método de roteamento de prioridade.    
>

O seguinte diagrama mostra essa configuração antes de uma interrupção:

![Cenário 1. Configuração antes da interrupção.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Após uma interrupção na região primária, o serviço do Banco de Dados SQL detecta que o banco de dados primário não está acessível e dispara um failover para a região secundária, com base nos parâmetros da política de failover automático (1). Dependendo do SLA do aplicativo, você pode configurar um período de carência que controla o tempo entre a detecção de interrupção e o failover em si. É possível que o gerenciador de tráfego inicie o failover do ponto de extremidade antes que o grupo de failover dispare o failover do banco de dados. Nesse caso, o aplicativo Web não pode se reconectar ao banco de dados imediatamente. Porém, as reconexões terão êxito automaticamente assim que o failover do banco de dados for concluído. Quando a região com falha é restaurada e colocada online novamente, o primário antigo se reconecta automaticamente como um novo secundário. O diagrama abaixo ilustra a configuração após o failover.
 
> [!NOTE]
> Todas as transações confirmadas após o failover são perdidas durante a reconexão. Após a conclusão do failover, o aplicativo da região B consegue se reconectar e reiniciar o processamento das solicitações do usuário. O aplicativo Web e o banco de dados primário agora estão na região B e permanecem colocalizados. 
n>

![Cenário 1. Configuração após o failover](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Se ocorrer uma interrupção na região B, o processo de replicação entre o banco de dados primário e secundário será suspenso, mas o vínculo entre os dois permanecerá intacto (1). O gerenciador de tráfego detecta que a conectividade com a Região B foi interrompida e marca o aplicativo Web do ponto de extremidade 2 como Degradado (2). O desempenho do aplicativo não é afetado nesse caso, mas o banco de dados fica exposto e, portanto, corre um risco maior de perda de dados, no caso de falha da região A em sucessão.

> [!NOTE]
> Para a recuperação de desastre, recomendamos a configuração com implantação de aplicativo limitada a duas regiões. Isso ocorre porque a maioria das regiões geográficas do Azure tem somente duas regiões. Essa configuração não protege o aplicativo contra uma falha catastrófica simultânea nas duas regiões. Caso ocorra essa falha improvável, você poderá recuperar seus bancos de dados em uma terceira região usando a [operação de restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Depois que a interrupção é atenuada, o banco de dados secundário é sincronizado novamente de forma automática com o primário. Durante a sincronização, o desempenho do primário pode ser afetado. O impacto específico depende da quantidade de dados que o novo primário adquiriu desde o failover. O seguinte diagrama ilustra uma interrupção na região secundária:

![Cenário 1. Configuração após uma interrupção na região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

As principais **vantagens** desse padrão de design são:

* O mesmo aplicativo Web é implantado em ambas as regiões sem nenhuma configuração específica à região e não exige nenhuma lógica adicional para gerenciar o failover. 
* O desempenho do aplicativo não é afetado pelo failover, pois o aplicativo Web e o banco de dados sempre estão co-localizados.

A principal **desvantagem** é que os recursos do aplicativo na Região B são subutilizados na maioria das vezes.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Cenário 2: regiões do Azure para continuidade dos negócios com preservação máxima de dados
Essa opção é mais adequada para aplicativos com as seguintes características:

* Qualquer perda de dados é um risco comercial elevado. O failover de banco de dados só poderá ser usado como último recurso, se a interrupção for causada por uma falha catastrófica.
* O aplicativo oferece suporte aos modos somente leitura e leitura/gravação de operações e pode operar em “modo somente leitura” por um período de tempo específico.

Nesse padrão, o aplicativo alterna para o modo somente leitura quando as conexões de leitura/gravação começam a receber erros de tempo limite. O aplicativo Web é implantado em ambas as regiões e inclui uma conexão com o ponto de extremidade do ouvinte de leitura/gravação e uma conexão diferente com o ponto de extremidade do ouvinte somente leitura (1). O perfil do gerenciador de tráfego deve usar o [roteamento de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md). O [monitoramento de ponto de extremidade](../traffic-manager/traffic-manager-monitoring.md) deve ser habilitado para o ponto de extremidade do aplicativo em cada região (2).

O seguinte diagrama ilustra essa configuração antes de uma interrupção:

![Cenário 2: Configuração antes da interrupção.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando o gerenciador de tráfego detecta uma falha de conectividade na região A, ele alterna automaticamente o tráfego de usuário para a instância do aplicativo na região B. Com esse padrão, é importante definir o período de carência com perda de dados com um valor suficientemente alto, por exemplo, 24 horas. Ele garante que a perda de dados é impedida se a interrupção é atenuada dentro desse período. Quando o aplicativo Web na região B é ativado, as operações de leitura/gravação começam a falhar. Nesse ponto, ele deve alternar para o modo somente leitura (1). Nesse modo, as solicitações são encaminhadas automaticamente para o banco de dados secundário. Se a interrupção for causada por uma falha catastrófica, muito provavelmente, ela não poderá ser atenuada dentro do período de carência. Quando ele expirar, o grupo de failover disparará o failover. Depois disso, o ouvinte de leitura/gravação fica disponível e as conexões com ele param de falhar (2). O diagrama a seguir ilustra os dois estágios do processo de recuperação.

> [!NOTE]
> Se a interrupção na região primária é atenuada dentro do período de carência, o gerenciador de tráfego detecta a restauração da conectividade na região primária e alterna o tráfego do usuário novamente para a instância do aplicativo na região A. Essa instância do aplicativo é retomada e opera no modo leitura/gravação usando o banco de dados primário na região A, conforme ilustrado no diagrama anterior.
>

![Cenário 2: Estágios da recuperação de desastre.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se ocorrer uma interrupção na região B, o gerenciador de tráfego detectará a falha do aplicativo Web do ponto de extremidade 2 na região B e o marcará como degradado (1). Enquanto isso, o grupo de failover alterna o ouvinte somente leitura para a região A (2). Essa interrupção não afeta a experiência do usuário final, mas o banco de dados primário fica exposto durante a interrupção. O seguinte diagrama ilustra uma falha na região secundária:

![Cenário 2: Interrupção da região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Após a interrupção ser atenuada, o banco de dados secundário é sincronizado imediatamente com o primário e o ouvinte de somente leitura é revertido para o banco de dados secundário na região B. Durante a sincronização, o desempenho do banco de dados primário pode ser afetado ligeiramente dependendo da quantidade de dados que precisam ser sincronizados.

Esse padrão de design apresenta várias **vantagens**:

* Ele evita a perda de dados durante as interrupções temporárias.
* O tempo de inatividade depende somente da velocidade com a qual o gerenciador de tráfego detecta a falha de conectividade, o que pode ser configurado.

A **desvantagem** é que o aplicativo deve poder operar no modo somente leitura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Cenário 3: relocação de aplicativo para outra região geográfica sem perda de dados e tempo de inatividade quase zero 
Neste cenário, o aplicativo tem as seguintes características: 
* Os usuários finais acessam o aplicativo em geografias diferentes
* O aplicativo inclui cargas de trabalho somente leitura que não dependem de uma sincronização completa com as últimas atualizações
* O acesso de gravação aos dados deve ter suporte na mesma geografia para a maioria dos usuários 
* A latência de leitura é crítica para a experiência do usuário final 


Para atender a esses requisitos, você precisa garantir que o dispositivo do usuário **sempre** se conecta ao aplicativo implantado na mesma geografia para as operações somente leitura, como procura de dados, análise, etc. Por outro lado, as operações OLTP são processadas na mesma geografia na **maior parte do tempo**. Por exemplo, durante as horas do dia, as operações OLTP são processadas na mesma geografia, mas fora do horário comercial, elas podem ser processadas em uma geografia diferente. Se a atividade do usuário final geralmente ocorrer durante o horário de trabalho, você poderá garantir o desempenho ideal para a maioria dos usuários na maior parte do tempo. O diagrama a seguir mostra essa topologia. 
 
Os recursos do aplicativo devem ser implantados em cada geografia em que há demanda de uso substancial. Por exemplo, se o aplicativo for usado ativamente nos Estados Unidos, na União Europeia e no Sudeste Asiático, ele deverá ser implantado em todas essas geografias. O banco de dados primário deve ser dinamicamente alternado de uma geografia para a próxima ao final do horário de trabalho. Esse método é chamado de “seguir o sol”. A carga de trabalho OLTP sempre se conecta ao banco de dados por meio do ouvinte de leitura/gravação **&lt;failover-group-name&gt;.database.windows.net** (1). A carga de trabalho somente leitura se conecta ao banco de dados local diretamente usando o ponto de extremidade do servidor de bancos de dados **&lt;server-name&gt;.database.windows.net** (2). O gerenciador de tráfego é configurado com o [método de roteamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Isso garante que o dispositivo do usuário final seja conectado ao serviço Web na região mais próxima. O gerenciador de tráfego deve ser configurado com o monitoramento de ponto de extremidade habilitado para cada ponto de extremidade do serviço Web (3).

> [!NOTE]
> A configuração do grupo de failover define qual região é usada para failover. Como o novo primário está em uma geografia diferente, o failover resulta em uma latência mais longa para cargas de trabalho OLTP e somente leitura até que a região afetada fique online novamente.
>

![Cenário 3: Configuração com o primário no Leste dos EUA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

No final do dia (por exemplo, 23h na hora local), os bancos de dados ativos devem ser alternados para a próxima região (Europa Setentrional). Essa tarefa pode ser totalmente automatizada usando o [serviço de agendamento do Azure](../scheduler/scheduler-intro.md).  A tarefa envolve as seguintes etapas:
* Alternar o servidor primário no grupo de failover para a Europa Setentrional usando o failover amigável (1)
* Remover o grupo de failover entre o Leste dos EUA e a Europa Setentrional
* Criar um novo grupo de failover com o mesmo nome mas entre a Europa Setentrional e a Ásia Oriental (2). 
* Adicionar o primário na Europa Setentrional e o secundário na Ásia Oriental a esse grupo de failover (3).


O seguinte diagrama ilustra a nova configuração após o failover planejado:

![Cenário 3: Fazendo a transição do primário para a Europa Setentrional.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Se uma interrupção ocorrer na Europa Setentrional, por exemplo, o failover automático de banco de dados será iniciado pelo grupo de failover, o que resulta efetivamente na movimentação do aplicativo para a próxima região antes do prazo (1).  Nesse caso, o Leste dos EUA é a única região secundária restante até que a Europa Setentrional fique online novamente. As duas regiões restantes atendem aos clientes em todas as três geografias alternando funções. O agendador do Azure precisa ser ajustado de acordo. Como as demais regiões recebem tráfego de usuário adicional da Europa, o desempenho do aplicativo é afetado não apenas pela latência adicional, mas também por um aumento do número de conexões do usuário final. Depois que a interrupção for atenuada na Europa Setentrional, o banco de dados secundário será imediatamente sincronizado com o primário atual. O seguinte diagrama ilustra uma interrupção na Europa Setentrional:

![Cenário 3: Interrupção na Europa Setentrional.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Reduza o tempo em que a experiência do usuário final na Europa é degradada pela latência longa. Para fazer isso, você deve implantar de forma proativa uma cópia do aplicativo e criar os bancos de dados secundários em outra região local (Europa Ocidental) como uma substituição da instância do aplicativo offline na Europa Setentrional. Quando o último ficar online novamente, você poderá decidir se deseja continuar usando a Europa Ocidental ou remover a cópia do aplicativo nela e voltar a usar a Europa Setentrional.
>

Os principais **benefícios** desse design são:
* A carga de trabalho do aplicativo somente leitura acessa os dados na região mais próxima em todos os momentos. 
* A carga de trabalho do aplicativo de leitura/gravação acessa os dados na região mais próxima durante o período de atividade mais alto em cada geografia
* Como o aplicativo é implantado em várias regiões, ele pode sobreviver a uma perda de uma das regiões, sem nenhum tempo de inatividade significativo. 

Mas há algumas **desvantagens**:
* Uma interrupção regional resulta no impacto da geografia pela latência mais longa. As cargas de trabalho de leitura/gravação e somente leitura são atendidas pelo aplicativo em outra região. 
* As cargas de trabalho somente leitura devem se conectar a um ponto de extremidade diferente em cada região. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planejamento de continuidade de negócios: escolher um design de aplicativo para recuperação de desastre em nuvem
A estratégia específica de recuperação de desastre em nuvem pode combinar ou estender esses padrões de design para atender da melhor forma às necessidades do aplicativo.  Como mencionado anteriormente, a estratégia escolhida é baseada no SLA que você deseja oferecer a seus clientes e na topologia de implantação do aplicativo. Para ajudar a orientar sua decisão, a tabela a seguir compara as opções com base no RPO (objetivo de ponto de recuperação) e no ERT (tempo de recuperação estimado).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implantação ativa-passiva para recuperação de desastres com acesso de banco de dados colocalizado |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + TTL do DNS |
| Implantação ativa-ativa para balanceamento de carga de aplicativo |Acesso de leitura/gravação < 5 s |Tempo de detecção de falha + TTL do DNS |
| Implantação ativa-passiva para preservação de dados |Acesso somente leitura < 5 seg | Acesso somente leitura = 0 |
||Acesso de leitura/gravação = zero | Acesso de leitura/gravação = Tempo de detecção de falha + período de carência de perda de dados |
|||

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre a replicação geográfica e grupos de failover, consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para obter informações sobre o uso da replicação geográfica ativa com Pools elásticos, confira [Estratégias de recuperação de desastres do pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).