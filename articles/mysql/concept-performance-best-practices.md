---
title: Práticas recomendadas de desempenho-banco de dados do Azure para MySQL
description: Este artigo descreve algumas recomendações para monitorar e ajustar o desempenho do banco de dados do Azure para MySQL.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 46c7952247babd528b230dfa0e70b0eb47878912
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217747"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Práticas recomendadas para o desempenho ideal do banco de dados do Azure para MySQL-servidor único

Saiba como obter o melhor desempenho enquanto trabalha com seu banco de dados do Azure para MySQL-servidor único. À medida que adicionarmos novos recursos à plataforma, continuaremos refinando nossas recomendações nesta seção.

## <a name="physical-proximity"></a>Proximidade física

 Certifique-se de implantar um aplicativo e o banco de dados na mesma região. Uma verificação rápida antes de iniciar qualquer execução de parâmetro de comparação de desempenho é determinar a latência de rede entre o cliente e o banco de dados usando uma consulta SELECT 1 simples. 

## <a name="accelerated-networking"></a>Rede Acelerada

Use a rede acelerada para o servidor de aplicativos se você estiver usando a máquina virtual do Azure, o Azure kubernetes ou os serviços de aplicativo. A Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM compatíveis.

## <a name="connection-efficiency"></a>Eficiência da conexão

Estabelecer uma nova conexão é sempre uma tarefa cara e demorada. Quando um aplicativo solicita uma conexão de banco de dados, ele prioriza a alocação de conexões de banco de dados ociosas existentes em vez de criar uma nova.  Aqui estão algumas opções para boas práticas de conexão:

- **ProxySQL**: use [ProxySQL](https://proxysql.com/) , que fornece pooling de conexão interna e [balancear a carga de sua carga de trabalho](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) para várias réplicas de leitura, conforme necessário sob demanda com qualquer alteração no código do aplicativo.

- **Proxy de dados Heimdall**: como alternativa, você também pode aproveitar o proxy de dados Heimdall, uma solução de proxy de proprietário neutra de fornecedor. Ele dá suporte ao cache de consultas e à divisão de leitura/gravação com detecção de retardo de replicação. Você também pode consultar como [acelerar o desempenho do MySQL com o proxy Heimdall](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349).  

- **Conexão persistente ou Long-Lived**: se seu aplicativo tiver transações curtas ou consultas normalmente com tempo de execução < 5-10 ms, substitua conexões curtas por conexões persistentes. Substituir conexões curtas com conexões persistentes exige apenas pequenas alterações no código, mas tem um grande efeito em termos de melhorar o desempenho em muitos cenários de aplicativos típicos. Certifique-se de definir o tempo limite ou fechar a conexão quando a transação for concluída.

- **Réplica**: se você estiver usando a réplica, use [ProxySQL](https://proxysql.com/) para balancear a carga entre o servidor primário e o servidor de réplica secundária legível. Saiba [como configurar o ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Configurações de importação de dados

- Você pode dimensionar temporariamente sua instância para um tamanho de SKU mais alto antes de iniciar uma operação de importação de dados e dimensioná-la quando a importação for bem-sucedida.
- Você pode importar seus dados com tempo de inatividade mínimo usando o serviço de migração de banco de dado [do Azure (DMS)](https://datamigration.microsoft.com/) para migrações online ou offline. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Recomendações de memória do banco de dados do Azure para MySQL

Uma prática recomendada de desempenho do banco de dados do Azure para MySQL é alocar RAM suficiente para que o conjunto de trabalho resida quase que completamente na memória. 

- Verifique se a porcentagem de memória está sendo usada para atingir os [limites](./concepts-pricing-tiers.md) usando as [métricas para o servidor MySQL](./concepts-monitoring.md). 
- Configure alertas para esses números para garantir que, à medida que os servidores atingem limites, você pode tomar ações de prompt para corrigi-lo. Com base nos limites definidos, verifique se escalar verticalmente o SKU do banco de dados — para o tamanho de computação mais alto ou para um tipo de preço melhor, o que resulta em um aumento considerável no desempenho. 
- Escale verticalmente até que os números de desempenho não sejam mais eliminados drasticamente após uma operação de dimensionamento. Para obter informações sobre como monitorar as métricas de uma instância de banco de dados, consulte [métricas do banco](./concepts-monitoring.md#metrics)de dados MySQL.
 
## <a name="use-innodb-buffer-pool-warmup"></a>Usar o pool de buffers InnoDB aquecimento

Depois de reiniciar o banco de dados do Azure para o servidor MySQL, as páginas que residem no armazenamento são carregadas conforme as tabelas são consultadas, o que leva a uma latência maior e um desempenho mais lento para a primeira execução das consultas. Isso pode não ser aceitável para cargas de trabalho sensíveis à latência. 

Usar o pool de buffers InnoDB aquecimento reduz o período de aquecimento recarregando páginas de disco que estavam no pool de buffers antes da reinicialização em vez de esperar que as operações DML ou SELECT acessem as linhas correspondentes.

Você pode reduzir o período de aquecimento após reiniciar o banco de dados do Azure para o servidor MySQL, que representa uma vantagem de desempenho Configurando os [parâmetros do servidor do pool de buffers do InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html). O InnoDB salva uma porcentagem das páginas usadas mais recentemente para cada pool de buffers no desligamento do servidor e restaura essas páginas na inicialização do servidor.

Também é importante observar que o desempenho aprimorado é a despesa de tempo de inicialização mais longo para o servidor. Quando esse parâmetro é habilitado, espera-se que a inicialização do servidor e o tempo de reinicialização aumentem dependendo do IOPS provisionado no servidor. 

É recomendável testar e monitorar o tempo de reinicialização para garantir que o desempenho de inicialização/reinicialização seja aceitável, pois o servidor não está disponível durante esse tempo. Não é recomendável usar esse parâmetro com menos de 1000 IOPS provisionados (ou em outras palavras, quando o armazenamento provisionado é menor que 335 GB).

Para salvar o estado do pool de buffers no desligamento do servidor, defina o parâmetro `innodb_buffer_pool_dump_at_shutdown` de servidor como `ON` . Da mesma forma, defina o parâmetro de servidor `innodb_buffer_pool_load_at_startup` como `ON` para restaurar o estado do pool de buffers na inicialização do servidor. Você pode controlar o impacto no tempo de inicialização/reinicialização, reduzindo e ajustando o valor do parâmetro de servidor `innodb_buffer_pool_dump_pct` . Por padrão, esse parâmetro é definido como `25`.

> [!Note]
> Os parâmetros aquecimento do pool de buffers InnoDB têm suporte apenas em servidores de armazenamento de uso geral com até 16 TB de armazenamento. Saiba mais sobre [as opções de armazenamento do banco de dados do Azure para MySQL aqui](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage).

## <a name="next-steps"></a>Próximas etapas

- [Prática recomendada para operações de servidor usando o banco de dados do Azure para MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Prática recomendada para monitorar o banco de dados do Azure para MySQL](concept-monitoring-best-practices.md)<br/>
- [Introdução ao banco de dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>