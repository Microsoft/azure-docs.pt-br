---
title: "Análises de recuperação de desastre do Banco de Dados SQL | Microsoft Docs"
description: "Obtenha orientação e as práticas recomendadas para usar o Banco de dados SQL do Azure para executar as análises de recuperação de desastres para ajudar a manter seus aplicativos comerciais importantes resilientes a falhas e interrupções."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/31/2016
ms.author: sashan
ms.openlocfilehash: 1b1d65a41a794a566287dcffe3581ac58e2a965f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performing-disaster-recovery-drill"></a>Executar análise de recuperação de desastres
Recomenda-se a validação periódica da preparação do aplicativo para o fluxo de trabalho de recuperação. Consideramos uma boa prática de engenharia a verificação do comportamento do aplicativo e as implicações de perda de dados e/ou interrupção que envolvem o failover. Isso também é uma exigência da maioria dos padrões do setor, como parte da certificação de continuidade dos negócios.

A execução de uma análise de recuperação de desastres é composta por:

* Simulação da interrupção da camada de dados
* Recuperando
* Validação da integridade do aplicativo após a recuperação

Dependendo de como você [criou seu aplicativo para continuidade de negócios](sql-database-business-continuity.md), o fluxo de trabalho para executar a análise pode variar. A seguir, descrevemos as práticas recomendadas para condução de uma análise de recuperação de desastres no contexto do Banco de Dados SQL do Azure.

## <a name="geo-restore"></a>Restauração geográfica
Para evitar a possível perda de dados durante a realização de uma análise de recuperação de desastres, recomendamos a execução da análise usando um ambiente de teste criando uma cópia do ambiente de produção e usando-a para verificar o fluxo de trabalho de failover do aplicativo.

#### <a name="outage-simulation"></a>Simulação de interrupção
Para simular a interrupção, você pode excluir ou renomear o banco de dados de origem. Isso causará falha de conectividade do aplicativo.

#### <a name="recovery"></a>Recuperação
* Execute a restauração geográfica do banco de dados em um servidor diferente, como descrito [aqui](sql-database-disaster-recovery.md).
* Altere a configuração de aplicativo para se conectar aos bancos de dados recuperados e siga o guia [Configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

#### <a name="validation"></a>Validação
* Conclua a análise verificando a integridade do aplicativo após a recuperação (incluindo cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

## <a name="geo-replication"></a>Replicação geográfica
Para um banco de dados protegido usando a replicação geográfica, o exercício de análise envolverá failover planejado para o banco de dados secundário. O failover planejado garante que os bancos de dados primário e secundário permaneçam em sincronia quando as funções são alternadas. Ao contrário de failover não planejado, essa operação não resultará na perda de dados, assim, a análise pode ser executada no ambiente de produção.

#### <a name="outage-simulation"></a>Simulação de interrupção
Para simular a interrupção, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados. Isso resulta em falhas de conectividade para os clientes da web.

#### <a name="recovery"></a>Recuperação
* Certifique-se de que a configuração do aplicativo na região de DR aponta para o secundário anterior que se torna o novo primário totalmente acessível.
* Execute [failover planejado](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) para tornar o banco de dados secundário um novo primário
* Siga o guia [Configurar um banco de dados após a recuperação](sql-database-disaster-recovery.md) para concluir a recuperação.

#### <a name="validation"></a>Validação
* Conclua a análise verificando a integridade do aplicativo após a recuperação (incluindo cadeias de conexão, logons, teste de funcionalidade básica ou outras validações que fazem parte dos procedimentos de aprovações padrão do aplicativo).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre cenários de continuidade dos negócios, consulte [Cenários de continuidade](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, consulte [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [replicação geográfica ativa](sql-database-geo-replication-overview.md)  
