---
title: Recuperação de banco de dados acelerada
titleSuffix: Azure SQL
description: A recuperação de banco de dados acelerada fornece recuperação rápida e consistente de banco de dados, reversão instantânea de transação e truncamento de log agressivo para bancos de dados no portfólio SQL do Azure.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/19/2020
ms.openlocfilehash: bd1b33b2f6b1b0e0bf94639b3991b6507a89f5a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97400787"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Recuperação de banco de dados acelerada no SQL do Azure 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A **ADR (recuperação de banco de dados acelerada)** é um recurso de mecanismo de banco de dados SQL Server que melhora muito a disponibilidade do banco de dados, especialmente na presença de transações de longa execução, remodelando o processo de recuperação do mecanismo de banco de dados SQL Server. 

O ADR está disponível no momento para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada, bancos de dados no Azure Synapse Analytics e SQL Server em VMs do Azure a partir do SQL Server 2019. 

> [!NOTE] 
> ADR está habilitado por padrão no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada e não há suporte para a desabilitação de ADR para qualquer produto. 

## <a name="overview"></a>Visão geral

Os principais benefícios do ADR são:

- **Recuperação de banco de dados rápida e consistente**

  Com a ADR, as transações de execução prolongada não afetam o tempo de recuperação geral, permitindo uma recuperação de banco de dados rápida e consistente, independentemente do número de transações ativas no sistema ou do tamanho delas.

- **Reversão de transação instantânea**

  Com ADR, a reversão de transação é instantânea, independentemente do tempo em que a transação esteve ativa ou do número de atualizações que ela realizou.

- **Truncamento agressivo do log**

  Com ADR, o log de transações é truncado agressivamente, mesmo na presença de transações de execução longa ativas, o que impede o crescimento fora do controle.

## <a name="standard-database-recovery-process"></a>Processo de recuperação de banco de dados padrão

A recuperação de banco de dados segue o modelo de recuperação [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama a seguir e explicadas em mais detalhes após o diagrama.

![processo de recuperação atual](./media/accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Encaminhe a varredura do log de transações desde o início do último ponto de verificação bem-sucedido (ou o LSN de página sujo mais antigo) até o final, para determinar o estado de cada transação no momento em que o banco de dados foi interrompido.

- **Fase refazer**

  Encaminhe a varredura do log de transações da transação não confirmada mais antiga até o fim, para colocar o banco de dados no estado em que estava no momento da falha, refazendo todas as operações confirmadas.

- **Fase desfazer**

  Para cada transação que estava ativa no momento da falha, o registro é revertido, desfazendo as operações executadas por essa transação.

Com base nesse design, o tempo necessário para que o mecanismo de banco de dados do SQL Server se recupere de uma reinicialização inesperada é (aproximadamente) proporcional ao tamanho da transação ativa mais longa no sistema no momento da falha. A recuperação requer uma reversão de todas as transações incompletas. O período de tempo necessário é proporcional para o trabalho que a transação foi executado e o tempo ele esteve ativo. Portanto, o processo de recuperação pode levar muito tempo na presença de transações de longa execução (como operações de inserção em massa grandes ou operações de compilação de índice em uma tabela grande).

Além disso, o cancelamento / reversão de uma transação grande com base nesse design também pode levar um longo tempo, já que está usando a mesma fase de recuperação Desfazer, conforme descrito acima.

Além disso, o mecanismo de banco de dados SQL Server não pode truncar o log de transações quando há transações de longa execução, pois seus registros de log correspondentes são necessários para os processos de recuperação e reversão. Como resultado desse design do mecanismo de banco de dados SQL Server, alguns clientes usaram o problema de que o tamanho do log de transações cresce muito grande e consome enormes quantidades de espaço em disco.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação de banco de dados acelerada

ADR aborda os problemas acima remodelando completamente o processo de recuperação do mecanismo de banco de dados SQL Server para:

- Torne constante o tempo / instantâneo, evitando ter que escanear o log de / para o início da transação ativa mais antiga. Com ADR, o log de transações só é processado a partir do último ponto de verificação bem-sucedido (ou LSN (número de sequência de log) de página sujo mais antigo). Como resultado, o tempo de recuperação não é afetado por longa execução de transações.
- Minimize o espaço de log de transações necessário, pois não há mais necessidade de processar o log para toda a transação. Como resultado, o log de transações pode ser truncado de forma agressiva à medida que os pontos de verificação e backups ocorrem.

Em um nível alto, o ADR obtém uma rápida recuperação do banco de dados, modificando todas as modificações físicas do banco de dados e apenas desfazendo as operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estava ativa no momento de uma falha é marcada como abortada e, portanto, qualquer versão gerada por essas transações pode ser ignorada por consultas de usuários simultâneas.

O processo de recuperação ADR tem as mesmas três fases que o processo de recuperação atual. Como essas fases operam com ADR é ilustrado no diagrama a seguir e explicado com mais detalhes seguindo o diagrama.

![Processo de recuperação ADR](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo que antes com a adição da recriação de sLog e a cópia de registros de log para operações sem controle de versão.
  
- Fase **refazer**

  Dividido em duas fases (P)
  - Fase 1

      Refazer de sLog (transação não confirmada mais antiga até o último ponto de verificação). O Redo é uma operação rápida, pois precisa apenas processar alguns registros do sLog.

  - Fase 2

     Refazer a partir do Log de transações inicia a partir do último ponto de verificação (em vez da transação não confirmada mais antiga)

- **Fase desfazer**

   A fase Desfazer com ADR é concluída quase instantaneamente usando sLog para desfazer operações sem versão e Armazenamento de Versão Persistente (PVS) com Revolução Lógica para executar a Desfazer baseada em versão no nível da linha.

## <a name="adr-recovery-components"></a>Componentes de recuperação ADR

Os quatro componentes principais da ADR são:

- **PVS (repositório de versão persistente)**

  O repositório de versão persistente é um novo mecanismo de mecanismo de banco de dados SQL Server para persistir as versões de linha geradas no próprio banco de dados, em vez do `tempdb` armazenamento de versão tradicional. PVS habilita o isolamento de recursos, bem como melhora a disponibilidade de secundários legíveis.

- **Reverter lógica**

  A reversão lógica é o processo assíncrono responsável por executar desfazer baseado em versão de linha, fornecendo reversão de transação instantânea e desfazer para todas as operações com controle de versão. A reversão lógica é realizada por:

  - Manter o controle de todas as transações anuladas e marcá-las invisíveis para outras transações. 
  - Executar Rollback usando PVS para todas as transações de usuário, em vez de verificar fisicamente o log de transações e desfazer as alterações uma de cada vez.
  - Liberando todos os bloqueios imediatamente após a anulação da transação. Como Abort envolve simplesmente marcar alterações na memória, o processo é muito eficiente e, portanto, os bloqueios não precisam ser mantidos por muito tempo.

- **sLog**

  O sLog é um fluxo de log na memória secundário que armazena registros de log para operações sem controle de versão (tais como invalidação de cache de metadados, aquisições de bloqueio e assim por diante). O sLog é:

  - de baixo volume e na memória;
  - persistido no disco ao ser serializado durante o processo de ponto de verificação;
  - truncado periodicamente conforme as transações são confirmadas;
  - acelera as operações refazer e desfazer ao processar apenas as operações sem controle de versão;  
  - habilita o truncamento agressivo de log de transações ao preservar apenas os registros de log necessários.

- **Limpador**

  O limpador é o processo assíncrono que é ativado periodicamente e limpa as versões de página que não são necessárias.

## <a name="accelerated-database-recovery-patterns"></a>Padrões de recuperação de banco de dados acelerados

Os seguintes tipos de cargas de trabalho se beneficiam da ADR:

- Cargas de trabalho com transações de longa execução.
- Cargas de trabalho que viram casos em que as transações ativas estão fazendo com que o log de transações cresça significativamente.  
- Cargas de trabalho que tiveram longos períodos de indisponibilidade de banco de dados devido a uma recuperação de longa execução (como reinicialização de serviço inesperada ou reversão de transação manual).
