---
title: Análises de recuperação de desastre
description: Aprenda diretrizes e melhores práticas para usar o Banco de Dados SQL do Azure para executar simulações de recuperação de desastre.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91321655"
---
# <a name="performing-disaster-recovery-drills"></a>Executando análises de recuperação de desastre
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Recomenda-se a validação periódica da preparação do aplicativo para o fluxo de trabalho de recuperação. Consideramos uma boa prática de engenharia a verificação do comportamento do aplicativo e as implicações de perda de dados e/ou interrupção que envolvem o failover. Isso também é uma exigência da maioria dos padrões do setor, como parte da certificação de continuidade dos negócios.

A execução de uma análise de recuperação de desastres é composta por:

* Simulação da interrupção da camada de dados
* Recuperando
* Validação da integridade do aplicativo após a recuperação

Dependendo de como você [criou seu aplicativo para continuidade de negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md), o fluxo de trabalho para executar a análise pode variar. Este artigo descreve as práticas recomendadas para conduzir uma análise de recuperação de desastres no contexto do Banco de Dados SQL do Azure.

## <a name="geo-restore"></a>Restauração geográfica

Para evitar a perda potencial de dados ao realizar uma simulação de recuperação de desastres, execute a análise usando um ambiente de teste, criando uma cópia do ambiente de produção e usando-a para verificar o fluxo de trabalho de failover do aplicativo.

### <a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, você pode excluir ou renomear o banco de dados de origem. Essa alteração no nome causará falha de conectividade do aplicativo.

### <a name="recovery"></a>Recuperação

* Execute a restauração geográfica do banco de dados em um servidor diferente, como descrito [aqui](disaster-recovery-guidance.md).
* Altere a configuração de aplicativo para se conectar aos bancos de dados recuperados e siga o guia [Configurar um banco de dados após a recuperação](disaster-recovery-guidance.md) para concluir a recuperação.

### <a name="validation"></a>Validação

Conclua a análise verificando a integridade do aplicativo após a recuperação (incluindo cadeias de conexão, logons, teste básico de funcionalidade ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

## <a name="failover-groups"></a>Grupos de failover

Para um banco de dados que é protegido usando grupos de failover, o exercício de análise envolve o failover planejado para o servidor secundário. O failover planejado garante que os bancos de dados primário e secundário permaneçam em sincronia quando as funções são alternadas. Ao contrário de failover não planejado, essa operação não resultará na perda de dados, assim, a análise pode ser executada no ambiente de produção.

### <a name="outage-simulation"></a>Simulação de interrupção

Para simular a interrupção, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados. Essa simulação de interrupção resulta em falhas de conectividade para os clientes da web.

### <a name="recovery"></a>Recuperação

* Certifique-se de que a configuração do aplicativo na região de DR aponta para o secundário anterior, que se torna o novo primário totalmente acessível.
* Inicie o [failover planejado](scripts/setup-geodr-and-failover-database-powershell.md) do grupo de failover do servidor secundário.
* Siga o guia [Configurar um banco de dados após a recuperação](disaster-recovery-guidance.md) para concluir a recuperação.

### <a name="validation"></a>Validação

Conclua a análise verificando a integridade do aplicativo após a recuperação (incluindo cadeias de conexão, logons, teste básico de funcionalidade ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre cenários de continuidade dos negócios, consulte [Cenários de continuidade](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Para saber mais sobre os backups automatizados do banco de dados SQL do Azure, confira [backups automatizados](automated-backups-overview.md)
* Para saber mais sobre como usar backups automatizados para recuperação, consulte [restaurar um banco de dados dos backups iniciados pelo serviço](recovery-using-backups.md).
* Para saber mais sobre opções de recuperação mais rápidas, consulte [Replicação geográfica ativa](active-geo-replication-overview.md) e [Grupos de failover automático](auto-failover-group-overview.md).
