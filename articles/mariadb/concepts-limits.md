---
title: Limitações - Banco de Dados do Azure para MariaDB
description: Este artigo descreve as limitações no Banco de Dados do Azure para o MariaDB, como o número de opções de mecanismo de conexão e armazenamento.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: d4546732f067988c9d7dd2d11c718a15fbe32d23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664292"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações no Banco de Dados do Azure para o MariaDB
As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados.

## <a name="server-parameters"></a>Parâmetros do Servidor

> [!NOTE]
> Se você estiver procurando por valores mínimos/máximos para parâmetros de servidor como `max_connections` e `innodb_buffer_pool_size` , essas informações foram movidas para o artigo **[parâmetros de servidor](./concepts-server-parameters.md)** .

O banco de dados do Azure para MariaDB dá suporte ao ajuste dos valores dos parâmetros do servidor. O valor mínimo e máximo de alguns parâmetros (ex. `max_connections`, `join_buffer_size` , `query_cache_size` ) é determinado pelo tipo de preço e vCores do servidor. Consulte [parâmetros do servidor](./concepts-server-parameters.md) para obter mais informações sobre esses limites.

Após a implantação inicial, um servidor do Azure para MariaDB inclui tabelas de sistemas para informações de fuso horário, mas essas tabelas não são populadas. As tabelas de fuso horário podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o Workbench do MySQL. Consulte os artigos [Portal do Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou [CLI do Azure](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) para saber como chamar o procedimento armazenado e definir os fusos horários globais ou no nível da sessão.

Os plug-ins de senha, como "validate_password" e "caching_sha2_password", não são suportados pelo serviço.

## <a name="storage-engine-support"></a>Suporte do mecanismo de armazenamento

### <a name="supported"></a>Com suporte
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Sem suporte
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Privilégios & suporte à manipulação de dados

Muitos parâmetros de servidor e configurações podem degradar inadvertidamente o desempenho do servidor ou negar as propriedades ACID do servidor MariaDB. Para manter a integridade do serviço e o SLA em um nível de produto, esse serviço não expõe várias funções. 

O serviço MariaDB não permite acesso direto ao sistema de arquivos subjacente. Não há suporte para alguns comandos de manipulação de dados. 

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Sem suporte

Os itens a seguir não têm suporte:
- Função DBA: restrita. Como alternativa, você pode usar o usuário administrador (criado durante a criação do novo servidor), permitindo que você execute a maioria das instruções DDL e DML. 
- SUPER privilégio: da mesma forma, o [super privilégio](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- DEFINER: Requer superprivilégios para criar e é restrito. Se estiver importando dados usando um backup, remova os comandos `CREATE DEFINER`manualmente ou usando o comando `--skip-definer` ao executar um mysqldump.
- Bancos de dados do sistema: o [banco de dados do sistema MySQL](https://mariadb.com/kb/en/the-mysql-database-tables/) é somente leitura e usado para dar suporte a várias funcionalidades de PaaS. Você não pode fazer alterações no `mysql` banco de dados do sistema.
- `SELECT ... INTO OUTFILE`: Sem suporte no serviço.

### <a name="supported"></a>Com suporte
- `LOAD DATA INFILE` tem suporte, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado por meio do SMB).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="point-in-time-restore"></a>Restauração pontual
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
- Não há suporte para restaurar um servidor eliminado.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="storage-size"></a>Tamanho de armazenamento
- Consulte [tipos de preço](concepts-pricing-tiers.md) para saber os limites de tamanho de armazenamento por faixa de preço.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- Instância de servidor MariaDB exibe a versão de servidor incorreto após o estabelecimento de conexão. Para obter a versão correta do mecanismo de instância de servidor, use o comando `select version();`.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões com suporte de banco de dados MariaDB](concepts-supported-versions.md)
