---
title: Práticas recomendadas operacionais do servidor MySQL-banco de dados do Azure para MySQL
description: Este artigo descreve as práticas recomendadas para operar seu banco de dados MySQL no Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96354937"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Práticas recomendadas para operações de servidor no banco de dados do Azure para MySQL-servidor único

Saiba mais sobre as práticas recomendadas para trabalhar com o banco de dados do Azure para MySQL. À medida que adicionarmos novos recursos à plataforma, continuaremos a se concentrar em refinar as práticas recomendadas detalhadas nesta seção.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Diretrizes operacionais do banco de dados do Azure para MySQL 

Veja a seguir as diretrizes operacionais que devem ser seguidas ao trabalhar com o banco de dados do Azure para MySQL para melhorar o desempenho do banco de dados: 

* **Colocalização**: para reduzir a latência de rede, coloque o cliente e o servidor de banco de dados estão na mesma região do Azure.

* **Monitore a memória, a CPU e o uso do armazenamento**: você pode [configurar alertas](howto-alert-on-metric.md) para notificá-lo quando os padrões de uso forem alterados ou quando você abordar a capacidade de sua implantação, para que você possa manter o desempenho e a disponibilidade do sistema. 

* **Escalar verticalmente sua instância de BD**: você pode [escalar verticalmente](howto-create-manage-server-portal.md) quando estiver se aproximando dos limites de capacidade de armazenamento. Você deve ter algum buffer no armazenamento e na memória para acomodar aumentos imprevistos na demanda de seus aplicativos. Você também pode [habilitar o recurso de crescimento automático do armazenamento](howto-auto-grow-storage-portal.md) ' on ' apenas para garantir que o serviço dimensione automaticamente o armazenamento conforme ele se aproximar dos limites de armazenamento. 

* **Configurar backups**: habilitar [backups locais ou com redundância geográfica](howto-restore-server-portal.md#set-backup-configuration) com base no requisito do negócio. Além disso, você modifica o período de retenção de quanto tempo os backups estão disponíveis para continuidade dos negócios. 

* **Aumentar a capacidade de e/s**: se a carga de trabalho do banco de dados exigir mais e/s do que você provisionou, a recuperação ou outras operações transacionais para seu banco de dados serão lentas. Para aumentar a capacidade de e/s de uma instância de servidor, faça o seguinte: 

    * O banco de dados do Azure para MySQL fornece dimensionamento de IOPS na taxa de três IOPS por GB de armazenamento provisionado. [Aumente o armazenamento provisionado](howto-create-manage-server-portal.md#scale-storage-up) para dimensionar o IOPS para melhorar o desempenho. 

    * Se você já estiver usando o armazenamento de IOPS provisionado, Provisione a [capacidade de taxa de transferência adicional](howto-create-manage-server-portal.md#scale-storage-up). 

* **Computação em escala**: a carga de trabalho do banco de dados também pode ser limitada devido à CPU ou memória e isso pode ter um impacto sério no processamento da transação. Observe que a computação (tipo de preço) pode ser ampliada ou reduzida somente entre as camadas de [uso geral ou com otimização de memória](concepts-pricing-tiers.md) . 

* **Teste de failover**: failover de teste manual para sua instância de servidor para entender quanto tempo o processo leva para seu caso de uso e para garantir que o aplicativo que acessa a instância do servidor possa se conectar automaticamente à nova instância do servidor após o failover.

* **Usar chave primária**: Verifique se as tabelas têm uma chave primária ou exclusiva enquanto você opera no banco de dados do Azure para MySQL. Isso ajuda muito a fazer backups, réplicas etc. e melhora o desempenho.

* **Configurar valor TTL**: se o aplicativo cliente estiver armazenando em cache os dados do DNS (serviço de nomes de domínio) de suas instâncias de servidor, defina um valor de vida útil (TTL) inferior a 30 segundos. Como o endereço IP subjacente de uma instância de servidor pode ser alterado após um failover, armazenar em cache os dados DNS por um tempo estendido pode levar a falhas de conexão se seu aplicativo tentar se conectar a um endereço IP que não está mais em serviço.

* Use o pool de conexões para evitar atingir os [limites máximos de conexão](concepts-server-parameters.md#max_connections)e use a lógica de repetição para evitar problemas intermitentes de conexão. 

* Se você estiver usando a réplica, use [ProxySQL para balancear a carga](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) entre o servidor primário e o servidor de réplica secundária legível. Consulte as etapas de instalação aqui. </br> 

* Ao provisionar o recurso, certifique-se [de ter habilitado o crescimento automático](howto-auto-grow-storage-portal.md) para seu banco de dados do Azure para MySQL. Isso não adiciona nenhum custo adicional e protegerá o banco de dados de qualquer afunilamento de armazenamento que você possa encontrar. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Usando o InnoDB com o banco de dados do Azure para MySQL

*   Se `ibdata1` estiver usando o recurso, que é um arquivo de dados de espaço de tabela do sistema, não é possível reduzir ou ser limpo removendo os dados da tabela ou movendo a tabela para arquivo por tabela `tablespaces` .

* Para um banco de dados com mais de 1 TB de tamanho, você deve criar a tabela no **innodb_file_per_table** `tablespace` . Para uma única tabela com tamanho maior que 1 TB, você deve a tabela de [partição](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) .

*   Para um servidor que tem um grande número de `tablespace` , a inicialização do mecanismo será muito lenta devido à verificação de espaço de tabela sequencial durante a inicialização ou o failover do MySQL. 

* Defina innodb_file_per_table = ON antes de criar uma tabela, se o número da tabela total for menor que 500.

* Se você tiver mais de 500 tabelas em um banco de dados, revise o tamanho da tabela para cada tabela individual. Para uma tabela grande, você ainda deve considerar o uso do espaço de tabela file-by-Table para evitar o limite de armazenamento máximo de acesso ao sistema de espaço para arquivo.

> [!NOTE]
> Para tabelas com tamanho inferior a 5 GB, considere usar o espaço de tabela do sistema 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Particione](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) sua tabela na criação de tabela se você tiver uma tabela muito grande que possa ultrapassar 1 TB.

* Use vários servidores MySQL e distribua as tabelas entre esses servidores. Evite colocar muitas tabelas em um único servidor se você tiver cerca de 10000 tabelas ou mais. 

## <a name="next-steps"></a>Próximas etapas
- [Prática recomendada para o desempenho do banco de dados do Azure para MySQL](concept-performance-best-practices.md)
- [Prática recomendada para monitorar o banco de dados do Azure para MySQL](concept-monitoring-best-practices.md)
