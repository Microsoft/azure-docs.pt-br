---
title: Continuidade dos negócios-banco de dados do Azure para MariaDB
description: Saiba mais sobre continuidade de negócios (restauração pontual, data center interrupção, restauração geográfica) ao usar o banco de dados do Azure para serviço MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e164930736f7b93e56d9bd4187e72a9c8eb76202
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659845"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Entender a continuidade dos negócios no banco de dados do Azure para MariaDB

Este artigo descreve os recursos que o banco de dados do Azure para MariaDB fornece para a continuidade dos negócios e a recuperação de desastres. Saiba mais sobre as opções para recuperação dos eventos interruptivos que podem causar perda de dados ou tornar o banco de dados e o aplicativo indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que podem ser utilizados para fornecer continuidade dos negócios

O Banco de Dados do Azure para MariaDB fornece recursos de continuidade dos negócios que incluem backups automatizados e capacidade para usuários iniciarem a restauração geográfica. Cada um possui características diferentes para ERT (Tempo de Recuperação Estimado) e potencial perda de dados. Após compreender essas opções, você poderá escolher entre elas e utilizá-las para diferentes cenários. Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário entender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento interruptivo - esse é o RTO (Objetivo do Tempo de Recuperação). Além disso, será necessário entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo poderá tolerar perder durante a recuperação após um evento interruptivo - esse é o RPO (Objetivo de Ponto de Recuperação).

A tabela a seguir compara o ERT e o RPO para os recursos disponíveis:

| **Recurso** | **Basic** | **Uso Geral** | **Otimizado para memória** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Recuperação Pontual do backup | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Restauração geográfica de backups replicados geograficamente | Sem suporte | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Se você excluir o servidor, todos os bancos de dados contidos no servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de aplicativo ou usuário

Você pode usar os backups do serviço para recuperar um servidor de vários eventos de interrupção. Um usuário pode excluir alguns dados acidentalmente, remover uma tabela importante inadvertidamente ou até mesmo um banco de dados inteiro. Um aplicativo pode substituir acidentalmente dados corretos por dados incorretos, devido a uma falha de aplicativo, e assim por diante.

Você pode executar uma restauração pontual para criar uma cópia do servidor em um ponto no tempo conhecido e ideal. Esse ponto no tempo deve estar dentro do período de retenção de backup que você configurou para o servidor. Depois que os dados forem restaurados para o novo servidor, você poderá substituir o servidor de origem pelo servidor restaurado recentemente, ou copiar os dados necessários do servidor restaurado para o servidor de origem.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma interrupção no data center regional do Azure

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, isso causa uma interrupção dos negócios, que pode durar alguns minutos ou horas.

Uma opção é aguardar até que o servidor retorne online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem ter o servidor offline por algum período de tempo, por exemplo, um ambiente de desenvolvimento. Quando o data center tem uma interrupção, não é possível saber por quanto tempo a interrupção poderá durar, então, essa opção somente funcionará se você não precisar usar o servidor por algum tempo.

A outra opção é usar a restauração geográfica do Banco de Dados do Azure para MariaDB que restaura o servidor usando backups com redundância geográfica. Esses backups serão acessíveis mesmo quando a região em que seu servidor está hospedado estiver offline. É possível restaurar a partir desses backups para qualquer outra região e retornar o servidor para online.

> [!IMPORTANT]
> A restauração geográfica somente será possível se o servidor foi provisionado com armazenamento de backup com redundância geográfica.

## <a name="cross-region-read-replicas"></a>Réplicas de leitura entre regiões

Você pode usar réplicas de leitura entre regiões para aprimorar sua continuidade de negócios e planejamento de recuperação de desastre. As réplicas de leitura são atualizadas de forma assíncrona usando a tecnologia de replicação de log binário do MariaDB. Saiba mais sobre réplicas de leitura, regiões disponíveis e como fazer failover do [artigo conceitos de leitura de réplicas](concepts-read-replicas.md). 

## <a name="faq"></a>Perguntas frequentes
### <a name="where-does-azure-database-for-mariadb-store-customer-data"></a>Onde o Azure database for MariaDB armazena dados do cliente?
Por padrão, o banco de dados do Azure para MariaDB não moverá nem armazenará o cliente de fora da região em que está implantado. No entanto, os clientes podem optar por habilitar [backups com redundância geográfica](concepts-backup.md#backup-redundancy-options) ou criar [réplica de leitura entre regiões](concepts-read-replicas.md#cross-region-replication) para armazenar dados em outra região.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [backups automatizados no banco de dados do Azure para MariaDB](concepts-backup.md).
- Saiba como restaurar usando o [portal do Azure](howto-restore-server-portal.md) ou a [CLI do Azure](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura no banco de dados do Azure para MariaDB](concepts-read-replicas.md).