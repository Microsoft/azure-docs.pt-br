---
title: Recuperação Acelerada do Banco de Dados - Banco de Dados SQL do Azure | Microsoft Docs
description: O Banco de Dados SQL do Azure tem um novo recurso que fornece recuperação de banco de dados rápida e consistente, reversão de transação instantânea e truncamento de log agressivo para bancos de dados individuais e em pool em Banco de Dados SQL do Azure, bem como bancos de dados no SQL Data Warehouse do Azure.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1d556c82f47868f4ee06694e23092f10029d619d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839845"
---
# <a name="accelerated-database-recovery"></a>Recuperação acelerada de banco de dados

**ADR (Accelerated Database Recovery - Recuperação Acelerada de Banco de Dados)** é um novo recurso de mecanismo de banco de dados SQL que melhora bastante a disponibilidade do banco de dados, especialmente na presença de transações de longa duração, reprojetando o processo de recuperação do mecanismo de banco de dados SQL. ADR está atualmente disponível para bancos de dados individuais e bancos de dados em pool no banco de dados SQL e bancos de dados no Azure SQL Data Warehouse (atualmente em visualização pública). Principais benefícios da ADR são:

- **Recuperação de banco de dados rápida e consistente**

  Com o ADR, transações de longa execução não afetam o tempo de recuperação geral, permitindo a recuperação de banco de dados rápida e consistente independentemente do número de transações ativas no sistema ou seus tamanhos.

- **Reversão de transação de instantâneo**

  Com o ADR, a reversão de transação é instantânea, independentemente da hora em que a transação foi Active Directory ou o número de atualizações que executou.

- **Truncamento de log agressivo**

  Com o ADR, o log de transações é truncado de forma agressiva, mesmo na presença de transações ativas de longa duração, o que impede que ele cresça fora de controle.

## <a name="the-current-database-recovery-process"></a>O processo de recuperação do banco de dados atual

A recuperação do banco de dados no SQL Server segue o modelo de recuperação [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) e consiste em três fases, que são ilustradas no diagrama a seguir e explicadas com mais detalhes após o diagrama.

![processo de recuperação atual](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Fase de análise**

  Encaminhar a verificação do log de transações desde o início do último ponto de verificação bem-sucedida (ou a página suja mais antiga LSN) até o final de para determinar o estado de cada transação no momento que do SQL Server é interrompido.

- **Fase refazer**

  Encaminhar verificação do log de transação da transação mais antiga não confirmada até o fim, para trazer o banco de dados para o estado em que estava no momento da falha ao refazer todas as operações confirmadas.

- **Fase Desfazer**

  Para cada transação que estava ativa no momento da falha, o registro é revertido, desfazendo as operações executadas por essa transação.

Com base nesse design, o tempo que o mecanismo do banco de dados SQL leva para recuperar-se de uma reinicialização inesperada é (aproximadamente) proporcional ao tamanho da transação ativa mais longa no sistema no momento da falha. A recuperação requer uma reversão de todas as transações incompletas. O período de tempo necessário é proporcional para o trabalho que a transação foi executado e o tempo ele esteve ativo. Portanto, o processo de recuperação do SQL Server pode levar muito tempo na presença de transações de longa duração (como grandes operações de inserção em massa ou operações de criação de índice em uma tabela grande).

Além disso, o cancelamento / reversão de uma transação grande com base nesse design também pode levar um longo tempo, já que está usando a mesma fase de recuperação Desfazer, conforme descrito acima.

Além disso, o mecanismo de banco de dados SQL não pode truncar o log de transações quando há transações de execução longa, porque seus registros de log correspondentes são necessários para os processos de recuperação e reversão. Como resultado desse design do mecanismo de banco de dados SQL, alguns clientes enfrentam o mesmo problema que o tamanho do log de transação ficar muito grande e consome enormes quantidades de espaço em disco.

## <a name="the-accelerated-database-recovery-process"></a>O processo de recuperação de banco de dados acelerada

O ADR soluciona os problemas acima, redesenhando completamente o processo de recuperação do mecanismo de banco de dados SQL para:

- Torne constante o tempo / instantâneo, evitando ter que escanear o log de / para o início da transação ativa mais antiga. Com o ADR, só é processado no log de transações do último ponto de verificação bem-sucedida (ou número de sequência de Log (LSN) da página suja mais antiga). Como resultado, o tempo de recuperação não é afetado por longa execução de transações.
- Minimize o espaço de log de transações necessário, pois não há mais necessidade de processar o log para toda a transação. Como resultado, o log de transações pode ser truncado de forma agressiva à medida que os pontos de verificação e backups ocorrem.

Em um alto nível, o ADR atinge recuperação rápida de banco de dados pelo controle de versão de todas as modificações de banco de dados físico e somente desfazer operações lógicas, que são limitadas e podem ser desfeitas quase instantaneamente. Qualquer transação que estava ativa no momento de uma falha é marcada como abortada e, portanto, qualquer versão gerada por essas transações pode ser ignorada por consultas de usuários simultâneas.

O processo de recuperação do ADR tem as mesmas três fases que o processo de recuperação atual. Como essas fases operam com ADR é ilustrado no diagrama a seguir e explicado com mais detalhes seguindo o diagrama.

![Processo de recuperação ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Fase de análise**

  O processo permanece o mesmo que hoje com a adição de reconstrução sLog copiando os registros de log de operações sem controle de versão.
  
- fase **Refazer**

  Dividido em duas fases (P)
  - Fase 1

      Refazer de sLog (transação não confirmada mais antiga até o último ponto de verificação). O Redo é uma operação rápida, pois precisa apenas processar alguns registros do sLog.
      
  - Fase 2

     Refazer a partir do Log de transações inicia a partir do último ponto de verificação (em vez da transação não confirmada mais antiga)
     
- **Fase Desfazer**

   A fase Desfazer com ADR é concluída quase instantaneamente usando sLog para desfazer operações sem versão e Armazenamento de Versão Persistente (PVS) com Revolução Lógica para executar a Desfazer baseada em versão no nível da linha.

## <a name="adr-recovery-components"></a>Componentes da recuperação ADR

Os quatro componentes principais da ADR são:

- **Store de versão persistentes (PVS)**

  O armazenamento de versão persistente é um novo mecanismo de mecanismo de banco de dados SQL para persistir as versões de linha geradas no próprio banco de dados, em vez do tradicional `tempdb` armazenamento de versão. PVS habilita o isolamento de recursos, bem como melhora a disponibilidade de secundários legíveis.

- **Lógica reverter**

  Reversão lógica é o processo assíncrono responsável por executar desfazer baseada em versão de nível de linha – fornecendo a reversão de transação de instantâneo e desfazer para todas as operações de controle de versão.

  - Mantém o controle de todas as transações anuladas
  - Executa a reversão usando o PVS para todas as transações do usuário
  - Anulação de todos os bloqueios imediatamente após a transação de versões

- **um tremendo esforço**

  sLog é um fluxo de log secundário de na memória que armazena os registros para operações sem controle de versão (como invalidação de cache de metadados, aquisições de bloqueio e assim por diante) de log. O sLog é:

  - Baixo volume e memória
  - Persistiu no disco por ser serializado durante o processo do ponto de verificação
  - Periodicamente truncado conforme as transações são confirmadas
  - Acelera refazer e desfazer processando apenas as operações sem versão  
  - Habilita o truncamento de log de transação agressiva preservando apenas os registros de log exigido

- **Limpador**

  O limpador é o processo assíncrono que acorda periodicamente e limpa as versões da página que não são necessárias.

## <a name="who-should-consider-accelerated-database-recovery"></a>Quem deve considerar a recuperação acelerada de banco de dados

Os seguintes tipos de clientes devem considerar a ativação do ADR:

- Os clientes que têm cargas de trabalho com longos transações em execução.
- Clientes que viram casos em que transações ativas estão fazendo com que o log de transações aumentará significativamente.  
- Clientes que passaram por longos períodos de indisponibilidade do banco de dados devido à recuperação longa do SQL Server (como reinicialização inesperada do SQL Server ou reversão de transação manual).

