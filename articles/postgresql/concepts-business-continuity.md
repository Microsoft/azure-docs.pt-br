---
title: Continuidade dos negócios-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve a continuidade dos negócios (restauração pontual, data center interrupção, restauração geográfica, réplicas) ao usar o banco de dados do Azure para PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: f8d8547c2d4900e6258f7360c50d8dfc17157832
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517237"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Visão geral da continuidade de negócios com o banco de dados do Azure para PostgreSQL-servidor único

Esta visão geral descreve os recursos que o Banco de Dados do Azure para PostgreSQL fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções para recuperação dos eventos interruptivos que podem causar perda de dados ou tornar o banco de dados e o aplicativo indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que podem ser utilizados para fornecer continuidade dos negócios

Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário entender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento interruptivo - esse é o RTO (Objetivo do Tempo de Recuperação). Além disso, será necessário entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo poderá tolerar perder durante a recuperação após um evento interruptivo - esse é o RPO (Objetivo de Ponto de Recuperação).

O Banco de Dados do Azure para PostgreSQL fornece recursos para a continuidade dos negócios que incluem: backups com redundância geográfica com a capacidade de iniciar uma restauração geográfica, além da implantação de réplicas de leitura em uma região diferente. Cada uma tem características diferentes para o tempo de recuperação e a potencial perda de dados. Com o recurso de [restauração geográfica](concepts-backup.md) , um novo servidor é criado usando os dados de backup replicados de outra região. O tempo geral necessário para restaurar e recuperar depende do tamanho do banco de dados e da quantidade de logs a serem recuperados. O tempo geral para estabelecer o servidor varia de alguns minutos a algumas horas. Com [réplicas de leitura](concepts-read-replicas.md), os logs de transações do primário são transmitidos de forma assíncrona para a réplica. No caso de uma interrupção do banco de dados primário devido a uma falha no nível da zona ou no nível da região, o failover para a réplica fornece um RTO mais curto e menor perda de dados.

> [!NOTE]
> O atraso entre o primário e a réplica depende da latência entre os sites, da quantidade de dados a serem transmitidos e mais importante na carga de trabalho de gravação do servidor primário. Cargas de trabalho de gravação pesadas podem gerar um atraso significativo. 
>
> Devido à natureza assíncrona da replicação usada para réplicas de leitura, elas **não devem** ser consideradas como uma solução de ha (alta disponibilidade), uma vez que o retardo mais alto pode significar RTO e RPO maiores. Somente para cargas de trabalho em que o retardo permanece menor por meio do horário de pico e horário fora da carga de trabalho, as réplicas de leitura podem atuar como uma alternativa de alta disponibilidade. Caso contrário, réplicas de leitura destinam-se a uma escala de leitura real para cargas de trabalho pesadas e para cenários de DR de (recuperação de desastre).

A tabela a seguir compara RTO e RPO em um cenário **típico de carga de trabalho** :

| **Recurso** | **Basic** | **Uso Geral** | **Otimizado para memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Recuperação Pontual do backup | Qualquer ponto de restauração dentro do período de retenção <br/> RTO – varia <br/>RPO < 15 min| Qualquer ponto de restauração dentro do período de retenção <br/> RTO – varia <br/>RPO < 15 min | Qualquer ponto de restauração dentro do período de retenção <br/> RTO – varia <br/>RPO < 15 min |
| Restauração geográfica de backups replicados geograficamente | Sem suporte | RTO – varia <br/>RPO < 1 h | RTO – varia <br/>RPO < 1 h |
| Réplicas de leitura | RTO-minutos * <br/>RPO < 5 min * | RTO-minutos * <br/>RPO < 5 min *| RTO-minutos * <br/>RPO < 5 min *|

 \* O RTO e o RPO **podem ser muito mais altos** em alguns casos, dependendo de vários fatores, incluindo a latência entre os sites, a quantidade de dados a serem transmitidos e a maior parte da carga de trabalho de gravação do banco de dado principal. 

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de aplicativo ou usuário

Você pode usar os backups do serviço para recuperar um servidor de vários eventos interruptivos. Um usuário pode excluir alguns dados acidentalmente, remover uma tabela importante inadvertidamente ou até mesmo um banco de dados inteiro. Um aplicativo pode substituir acidentalmente dados corretos por dados incorretos, devido a uma falha de aplicativo, e assim por diante.

Você pode executar uma **restauração pontual** para criar uma cópia do seu servidor para um momento bom conhecido no tempo. Esse ponto no tempo deve estar dentro do período de retenção de backup que você configurou para o servidor. Depois que os dados forem restaurados para o novo servidor, você poderá substituir o servidor de origem pelo servidor restaurado recentemente, ou copiar os dados necessários do servidor restaurado para o servidor de origem.

> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Use o [bloqueio de recursos do Azure](../azure-resource-manager/management/lock-resources.md) para ajudar a evitar a exclusão acidental do seu servidor.

## <a name="recover-from-an-azure-data-center-outage"></a>Recuperar de uma interrupção de data center do Azure

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, isso causa uma interrupção dos negócios, que pode durar alguns minutos ou horas.

Uma opção é aguardar até que o servidor retorne online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem ter o servidor offline por algum período de tempo, por exemplo, um ambiente de desenvolvimento. Quando um data center tem uma interrupção, você não sabe por quanto tempo a interrupção pode durar, portanto, essa opção só funcionará se você não precisar do seu servidor por algum tempo.

## <a name="geo-restore"></a>Restauração geográfica

O recurso de restauração geográfica restaura o servidor usando backups com redundância geográfica. Os backups são hospedados na [região emparelhada](../best-practices-availability-paired-regions.md)do servidor. Você pode restaurar esses backups para qualquer outra região. A restauração geográfica cria um novo servidor com os dados dos backups. Saiba mais sobre a restauração geográfica no [artigo conceitos de backup e restauração](concepts-backup.md).

> [!IMPORTANT]
> A restauração geográfica somente será possível se o servidor foi provisionado com armazenamento de backup com redundância geográfica. Se você deseja alternar de backups redundantes localmente para redundantes geo-ativos para um servidor existente, você deve fazer um dump usando o pg_dump do seu servidor existente e restaurá-lo em um servidor recém-criado configurado com backups geo-redundantes.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura entre regiões
Você pode usar réplicas de leitura entre regiões para aprimorar sua continuidade de negócios e planejamento de recuperação de desastre. As réplicas de leitura são atualizadas assincronamente usando a tecnologia de replicação física do PostgreSQL e podem atrasar a primária. Saiba mais sobre réplicas de leitura, regiões disponíveis e como fazer failover do [artigo conceitos de leitura de réplicas](concepts-read-replicas.md). 

## <a name="faq"></a>Perguntas frequentes
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Onde o Azure Database para PostgreSQL armazena dados do cliente?
Por padrão, o banco de dados do Azure para PostgreSQL não moverá nem armazenará o cliente de fora da região em que está implantado. No entanto, os clientes podem optar por habilitar [backups com redundância geográfica](concepts-backup.md#backup-redundancy-options) ou criar [réplica de leitura entre regiões](concepts-read-replicas.md#cross-region-replication) para armazenar dados em outra região.


## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre backups automáticos, confira [Backups automáticos no Banco de Dados do Azure para PostgreSQL](concepts-backup.md). 
- Saiba como restaurar usando o [portal do Azure](howto-restore-server-portal.md) ou a [CLI do Azure](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).
