---
title: Perguntas frequentes do Banco de dados SQL do Azure | Microsoft Docs
description: "Respostas a perguntas comuns feitas por clientes sobre bancos de dados de nuvem e o Banco de Dados SQL do Azure, o sistema de gerenciamento de bancos de dados relacionais da Microsoft (RDBMS) e o banco de dados como um serviço na nuvem."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: 6ed02ead07c50b9a49e8868756b6f957d7b49b99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sql-database-faq"></a>Perguntas frequentes sobre o Banco de Dados SQL

## <a name="what-is-the-current-version-of-sql-database"></a>O que é a versão atual do banco de dados SQL?
A versão atual do banco de dados SQL é V12. Versão V11 foi desativado.

## <a name="what-is-the-sla-for-sql-database"></a>O que é o SLA para o banco de dados SQL?
Garantimos que, por pelo menos 99,99% do tempo, os clientes terão conectividade entre seu Banco de Dados SQL Basic, Standard ou Premium do Microsoft Azure único ou elástico e o nosso gateway de Internet. Para saber mais, veja [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Como redefinir a senha para o administrador do servidor?
No [portal do Azure](https://portal.azure.com), clique em **Servidores SQL**, escolha o servidor na lista e clique em **Redefinir Senha**.

## <a name="how-do-i-manage-databases-and-logins"></a>Como gerenciar logons e bancos de dados?
Consulte [gerenciamento de bancos de dados e logons](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Como eu asseguro que somente endereços IP autorizados tenham permissão de acesso a um servidor?
Confira [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Como o uso do Banco de Dados SQL aparece na minha fatura?
O Banco de Dados SQL é cobrado de acordo com uma taxa por hora previsível baseada na camada de serviço mais o nível de desempenho para bancos de dados individuais ou em eDTUs por pool elástico. O uso real é calculado e rateado por hora, de modo que sua fatura poderá mostrar frações de hora. Por exemplo, se um banco de dados existir por 12 horas em um mês, sua fatura mostrará a utilização de 0,5 dia. Além disso, as camadas de serviço mais o nível de desempenho e os eDTUs por pool são divididos na fatura para facilitar a leitura do número de dias de banco de dados você usou para cada em um único mês.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>E se um banco de dados único ficar ativo por menos de uma hora ou usar uma camada de serviço mais alta por menos de uma hora?
Você será cobrado por cada hora em que um banco de dados existir usando a camada de serviço mais alta mais o nível de desempenho aplicado durante aquela hora, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados. 

Exemplos

* Se você criar um banco de dados Básico e atualizá-lo imediatamente para Standard S1, será cobrado pela tarifa Standard S1 na primeira hora.
* Se você atualizar um banco de dados do Básico para Premium às 22h e a atualização for concluída à 1h35, no dia seguinte, você será cobrado na taxa Premium começando à 1h. 
* Se você fizer o downgrade de um banco de dados Premium para Básico às 11h e se ele for concluído às 14h15, o banco de dados será cobrado na taxa Premium até 15h e depois serão cobradas as taxas Básico.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Como o uso do pool elástico é mostrado em minha fatura e o que acontece se eu alterar as eDTUs por pool?
As cobranças do pool elástico são mostradas em sua fatura como DTUs Elásticas (eDTUs) nos incrementos mostrados sob eDTUs por pool na [página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Não há cobrança por banco de dados para pools elásticos. Você será cobrado por hora de existência de um pool no eDTU mais alto, independentemente do uso ou se o pool estive ativo por menos de uma hora. 

Exemplos

* Se você criar um pool elástico Standard com 200 eDTUs às 11h18, adicionando cinco bancos de dados ao pool, será cobrado por 200 eDTUs pela hora cheia, começando às 11h e se estendendo pelo resto do dia.
* No Dia 2, às 5:05h, o Banco de Dados 1 começa a consumir 50 eDTUs e se mantém estável ao longo do dia. Os Bancos de Dados 2 a 5 flutuam entre 0 e 80 eDTUs. Durante o dia, você adiciona cinco outros bancos de dados que consomem eDTUs variados durante o dia. O Dia 2 é um dia inteiro cobrado a 200 eDTUs. 
* No Dia 3, às 5h,  você adiciona mais 15 bancos de dados. O uso do banco de dados aumenta durante o tia até o ponto em que você decide aumentar o número de eDTUs para o pool de 200 para 400, às 20:05h. As cobranças no nível de 200 eDTUs estavam em vigor até às 20h e aumentam para 400 eDTUs pelas quatro horas restantes. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Informações sobre preços e cobrança do pool elástico
Pools elásticos são cobrados de acordo com as seguintes características:

* Um pool elástico é cobrado após sua criação, mesmo quando ele não contém bancos de dados.
* Um pool elástico é cobrado por hora. Essa frequência de medição é a mesma dos níveis de desempenho de bancos de dados individuais.
* Se um pool elástico for redimensionado para um novo n´mero de eDTUs, ele não será cobrado de acordo com esse novo valor até que a operação de redimensionamento seja concluída. Isso segue o mesmo padrão que a alteração do nível de desempenho de bancos de dados individuais.
* O preço de um pool elástico baseia-se no número de eDTUs do pool. O preço de um pool elástico é independente do número e da utilização dos bancos de dados elásticos dentro dele.
* O preço é calculado por (número de eDTUs do pool) x (preço unitário por eDTU).

O preço unitário por eDTU de um pool elástico é maior que o preço unitário por DTU de um banco de dados individual na mesma camada de serviço. Para obter detalhes, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). 

Para entender as camadas de serviço e eDTUs, consulte [Opções e desempenho de Banco de Dados SQL](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Como o uso da replicação geográfica ativa em um pool elástico aparece na minha fatura?
Ao contrário dos bancos de dados únicos, o uso da [replicação geográfica ativa](sql-database-geo-replication-overview.md) com bancos de dados elásticos não tem um impacto direto no faturamento.  Você só será cobrado pelos eDTUs provisionados para cada um dos pools (o pool primário e o pool secundário)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Como o uso do recurso de auditoria afeta minha fatura?
A auditoria é interna ao serviço de Banco de Dados SQL sem custo extra e está disponível para os bancos de dados Básico, Standard, Premium e Premium RS. No entanto, para armazenar os logs de auditoria, o recurso de auditoria usa uma conta do Armazenamento do Azure e as taxas para tabelas e consultas no Armazenamento do Azure são aplicadas com base no tamanho do seu log de auditoria.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Como encontro a camada de serviço e o nível de desempenho corretos para bancos de dados únicos e pools elásticos?
Existem algumas ferramentas disponíveis para você. 

* Para os bancos de dados locais, use o [Supervisor de dimensionamento de DTU](http://dtucalculator.azurewebsites.net/), para recomendar os bancos de dados e os DTUs necessários, além de avaliar vários bancos de dados para os pools elásticos.
* Se um banco de dados individual puder se beneficiar de estar em um pool, o mecanismo inteligente do Azure recomendará um pool elástico se perceber um padrão de uso histórico que garanta isso. Consulte [Monitorar e gerenciar um pool elástico com o Portal do Azure](sql-database-elastic-pool-manage-portal.md). Para obter detalhes sobre como fazer os cálculos você mesmo, consulte [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool.md)
* Para ver se você precisa aumentar ou reduzir um banco de dados único, consulte [diretrizes sobre desempenho para bancos de dados únicos](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Com que frequência posso alterar a camada de serviço ou o nível de desempenho de um banco de dados único?
Você pode alterar a camada de serviço (entre Básico, Standard, Premium e Premium RS) ou o nível de desempenho dentro de uma camada de serviço (por exemplo, S1 para S2) sempre que desejar. Para bancos de dados de versão anteriores, você pode alterar o nível de desempenho ou de camada de serviço no máximo quatro vezes em um período de 24 horas.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Com que frequência posso ajustar eDTUs por pool?
Quantas vezes desejar.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Quanto tempo leva para alterar a camada de serviço ou o nível de desempenho de um banco de dados individual ou incluir ou retirar um banco de dados de um pool elástico?
A alteração da camada de serviço de um banco de dados e a inclusão e a retirada de um pool exige que o banco de dados seja copiado para a plataforma como uma operação em segundo plano. Alterar a camada de serviço pode levar alguns minutos a várias horas, dependendo do tamanho dos bancos de dados. Em ambos os casos, os bancos de dados permanecem online e disponíveis durante a movimentação. Para obter detalhes sobre como alterar os bancos de dados únicos, consulte [Alterar a camada de serviço de um banco de dados](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando eu devo usar um banco de dados únicos em vez de bancos de dados elásticos?
Em geral, os pools elásticos são projetados para um típico [padrão de aplicativo de software como serviço (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), em que há um banco de dados por cliente ou locatário. Comprar bancos de dados individuais e provisionar em excesso para atender as demandas variáveis e de pico de cada banco de dados frequentemente não é uma opção com bom custo-benefício. Com os pools, você gerencia o desempenho coletivo do pool e os bancos de dados são escalados e reduzidos verticalmente. O mecanismo inteligente do Azure recomendará um pool para bancos de dados quando perceber um padrão de uso que o garanta. Para obter detalhes, consulte [Diretrizes de pool elástico](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>O que significa ter até 200% de seu armazenamento máximo de banco de dados provisionado para o armazenamento de backup?
O armazenamento para backup é aquele associado aos seus backups de banco de dados automatizados utilizados para [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore) e [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore). O Banco de Dados SQL do Microsoft Azure fornece até 200% de seu armazenamento máximo de banco de dados provisionado em armazenamento de backup, sem custo adicional. Por exemplo, se você tiver uma instância de banco de dados Standard com tamanho provisionado de 250 GB, serão oferecidos a você 500 GB de espaço de armazenamento para backup sem custo adicional. Se seu banco de dados exceder o armazenamento de backup fornecido, é possível optar por reduzir o período de retenção ao entrar em contato com o Suporte do Azure ou pagar pelo armazenamento de backup adicional com a taxa padrão do Armazenamento com Redundância Geográfica com Acesso de Leitura (RA-GRS). Para saber mais sobre a cobrança de RA-GRS, consulte Detalhes de preços de armazenamento.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Estou mudando de Web/Negócios para novas camadas de serviço, o que eu preciso saber?
Os bancos de dados SQL Business e Web do Azure foram desativados. As camadas Básico, Standard, Premium, Premium RS e Elástica substituem os bancos de dados Web e Business obsoletos. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Qual é o retardo de replicação esperado durante a replicação geográfica de um banco de dados entre duas regiões na mesma região geográfica do Azure?
Atualmente, damos suporte a um RPO de cinco segundos e o retardo de replicação tem sido menor que isso, desde que o secundário geográfico esteja hospedado na região emparelhada recomendada pelo Azure e esteja na mesma camada de serviço.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Qual é o retardo de replicação esperado quando o secundário geográfico é criado na mesma região do banco de dados primário?
Com base em dados empíricos, não há muita diferença entre o retardo de replicação intra e entre regiões quando a região emparelhada recomendada pelo Azure é usada. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se houver uma falha de rede entre duas regiões, como a lógica de repetição funcionará quando a replicação geográfica estiver configurada?
Se houver uma desconexão, haverá uma repetição a cada 10 segundos para restabelecer as conexões.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>O que posso fazer para garantir que uma alteração crítica no banco de dados primário seja replicada?
O secundário geográfico é uma réplica assíncrona e não tentamos mantê-la em sincronização completa com o primário. Mas nós fornecemos um método para forçar a sincronização de modo a assegurar a replicação das alterações críticas (por exemplo, atualizações de senha). A sincronização forçada afeta o desempenho, já que bloqueia o thread de chamada até que todas as transações confirmadas sejam replicadas. Para obter detalhes, veja [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quais ferramentas estão disponíveis para monitorar o retardo de replicação entre o banco de dados primário e o secundário geográfico?
Expomos o retardo de replicação em tempo real entre o banco de dados primário e o secundário geográfico por meio de um DMV. Para obter detalhes, veja [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover um banco de dados para um servidor diferente na mesma assinatura
* No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, selecione um banco de dados na lista e, em seguida, clique em **Copiar**. Para obter mais detalhes, consulte [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover um banco de dados entre assinaturas
* No [Portal do Azure](https://portal.azure.com), clique em **servidores SQL** e, em seguida, selecione o servidor que hospeda seu banco de dados da lista. Clique em **Mover**, então selecione os recursos a mover e a assinatura para a qual movê-los.
