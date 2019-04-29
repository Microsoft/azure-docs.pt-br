---
title: Visão geral da continuidade dos negócios com o Banco de Dados do Azure para PostgreSQL
description: Visão geral da continuidade dos negócios com o Banco de Dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: b1d566ac571ddd2b2be3aff160f669e277887209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456267"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Visão geral da continuidade dos negócios com o Banco de Dados do Azure para PostgreSQL

Esta visão geral descreve os recursos que o Banco de Dados do Azure para PostgreSQL fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções para recuperação dos eventos interruptivos que podem causar perda de dados ou tornar o banco de dados e o aplicativo indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Recursos que podem ser utilizados para fornecer continuidade dos negócios

O Banco de Dados do Azure para PostgreSQL fornece recursos de continuidade dos negócios que incluem backups automatizados e a capacidade dos usuários iniciarem a restauração geográfica. Cada um possui características diferentes para ERT (Tempo de Recuperação Estimado) e potencial perda de dados. Após compreender essas opções, você poderá escolher entre elas e utilizá-las para diferentes cenários. Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário entender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento interruptivo - esse é o RTO (Objetivo do Tempo de Recuperação). Além disso, será necessário entender a quantidade máxima de atualizações de dados recentes (intervalo de tempo) que o aplicativo poderá tolerar perder durante a recuperação após um evento interruptivo - esse é o RPO (Objetivo de Ponto de Recuperação).

A tabela a seguir compara o ERT e o RPO para os recursos disponíveis:

| **Recurso** | **Básico** | **Uso geral** | **Memória otimizada** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Recuperação Pontual do backup | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção | Qualquer ponto de restauração dentro do período de retenção |
| Restauração geográfica de backups replicados geograficamente | Sem suporte | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Excluir servidores **não é possível** ser restaurado. Se você excluir o servidor, todos os bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Recuperar um servidor após um erro de aplicativo ou usuário

Você pode usar os backups do serviço para recuperar um servidor de vários eventos interruptivos. Um usuário pode excluir alguns dados acidentalmente, remover uma tabela importante inadvertidamente ou até mesmo um banco de dados inteiro. Um aplicativo pode substituir acidentalmente dados corretos por dados incorretos, devido a uma falha de aplicativo, e assim por diante.

Você pode executar uma restauração pontual para criar uma cópia do servidor em um ponto no tempo conhecido e ideal. Esse ponto no tempo deve estar dentro do período de retenção de backup que você configurou para o servidor. Depois que os dados forem restaurados para o novo servidor, você poderá substituir o servidor de origem pelo servidor restaurado recentemente, ou copiar os dados necessários do servidor restaurado para o servidor de origem.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Recuperar de uma interrupção no data center regional do Azure

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, isso causa uma interrupção dos negócios, que pode durar alguns minutos ou horas.

Uma opção é aguardar até que o servidor retorne online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem ter o servidor offline por algum período de tempo, por exemplo, um ambiente de desenvolvimento. Quando o data center tem uma interrupção, não é possível saber por quanto tempo a interrupção poderá durar, então, essa opção somente funcionará se você não precisar usar o servidor por algum tempo.

A outra opção é usar a restauração geográfica do Banco de Dados do Azure para PostgreSQL que restaura o servidor usando backups com redundância geográfica. Esses backups serão acessíveis mesmo quando a região em que seu servidor está hospedado estiver offline. É possível restaurar a partir desses backups para qualquer outra região e retornar o servidor para online.

> [!IMPORTANT]
> A restauração geográfica somente será possível se o servidor foi provisionado com armazenamento de backup com redundância geográfica. Se você deseja alternar entre backups redundantes localmente e redundantes para um servidor existente, você deve fazer um dump usando o pg_dump do seu servidor existente e restaurá-lo para um novo criado configurado com backups geo-redundantes.

## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre backups automáticos, confira [Backups automáticos no Banco de Dados do Azure para PostgreSQL](concepts-backup.md). 
- Saiba como restaurar usando o [portal do Azure](howto-restore-server-portal.md) ou a [CLI do Azure](howto-restore-server-cli.md).
- Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).