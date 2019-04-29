---
title: Leia réplicas no Banco de Dados do Azure para MySQL.
description: Este artigo descreve as réplicas de leitura do Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093209"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leia réplicas no Banco de Dados do Azure para MySQL

O recurso de réplica de leitura permite replicar dados de um servidor do Banco de Dados do Azure para MySQL (mestre) para até cinco servidores (réplicas) de somente leitura dentro da mesma região do Azure. As réplicas somente leitura são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição do arquivo binário nativo (log binário) do mecanismo MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas criadas no banco de dados do Azure para o serviço MySQL são novos servidores que podem ser gerenciados da mesma maneira que os servidores MySQL normais / autônomos. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento provisionado em GB/mês.

Para saber mais sobre os recursos e problemas de replicação do MySQL, consulte a [documentação de replicação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Quando usar réplicas de leitura

Aplicativos e cargas de trabalho que são intensivos em leitura podem ser atendidos pelas réplicas somente leitura. As réplicas de leitura ajudam a aumentar a quantidade de capacidade de leitura disponível em comparação com se você usasse apenas um servidor para leitura e gravação. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

### <a name="master-server-restart"></a>Reinicialização do servidor mestre

Quando você cria uma réplica para um mestre que não possui nenhuma réplica existente, o mestre primeiro será reiniciada para se prepare para replicação. Leve isso em consideração e execute estas operações durante um período de pouca atividade.

### <a name="stopping-replication"></a>A interrupção da replicação

Você pode optar por interromper a replicação entre um servidor mestre e um servidor de réplica. Parar a replicação remove o relacionamento de replicação entre o servidor mestre e o servidor de réplica.

Depois que a replicação é interrompida, o servidor de réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando "stop replication" foi iniciado. O servidor autônomo não alcança o servidor mestre. Este servidor não pode ser transformado em uma réplica novamente.

### <a name="replicas-are-new-servers"></a>Réplicas são novos servidores

As réplicas são criadas como novos bancos de dados do Azure para servidores MySQL. Servidores existentes não podem ser transformados em réplicas.

### <a name="replica-server-configuration"></a>Configuração do servidor de réplica

Os servidores de réplica são criados usando as mesmas configurações de servidor que o mestre, o que inclui as seguintes configurações:

- Tipo de preço
- Geração de computação
- vCores
- Armazenamento
- Período de retenção do backup
- Opção de redundância de backup
- Versão do mecanismo de MySQL
- Regras de firewall

Depois que uma réplica tiver sido criada, você poderá alterar a camada de preços (exceto para e de Basic), geração de computação, vCores, armazenamento e retenção de backup, independentemente do servidor mestre.

### <a name="master-server-configuration"></a>Configuração do servidor mestre

Se a configuração do servidor mestre (ex. vCores ou storage) é atualizado, a configuração das réplicas também deve ser atualizada para valores iguais ou maiores. Sem isso, o servidor de réplica pode não conseguir acompanhar as alterações feitas no mestre e pode falhar como resultado.

Novas regras de firewall adicionadas ao servidor mestre após a criação de um servidor de réplica não serão replicadas para a réplica. A réplica também deverá ser atualizada com essa nova regra de firewall.

### <a name="deleting-the-master-server"></a>Excluindo o servidor principal

Quando um servidor mestre é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas tornam-se a servidores autônomos. O próprio servidor mestre é excluído.

### <a name="user-accounts"></a>Contas de usuário

Os usuários no servidor mestre são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

### <a name="other"></a>Outros

- O GTID (identificadores de transação globais) não são compatíveis.
- A criação de uma réplica de uma réplica não é suportada.
- Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Esta é uma limitação da tecnologia de replicação do MySQL. Leia mais na [documentação de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para mais informações.
- Ajustar o parâmetro [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) em um servidor mestre depois de criar um servidor de réplica pode fazer com que a réplica fique fora de sincronia. O servidor de réplica não está ciente dos diferentes espaços de tabela.
- Verifique se as tabelas de servidor mestre contêm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.
- Analise a lista completa das limitações de replicação do MySQL no [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure](howto-read-replicas-cli.md)
