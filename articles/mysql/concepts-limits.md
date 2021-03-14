---
title: Limitações - Banco de Dados do Azure para MySQL
description: Este artigo descreve limitações no Banco de Dados do Azure para MySQL, como número de opções de mecanismo de armazenamento e conexão.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 9b18b24686908ac92f97ea0cae892369919ae4d6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721007"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações no Banco de Dados do Azure para MySQL
As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados. Consulte também as [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao mecanismo de banco de dados MySQL.

## <a name="server-parameters"></a>Parâmetros do Servidor

> [!NOTE]
> Se você estiver procurando por valores mínimos/máximos para parâmetros de servidor como `max_connections` e `innodb_buffer_pool_size` , essas informações foram movidas para o artigo **[parâmetros de servidor](./concepts-server-parameters.md)** .

O banco de dados do Azure para MySQL dá suporte ao ajuste dos valores dos parâmetros do servidor. O valor mínimo e máximo de alguns parâmetros (ex. `max_connections`, `join_buffer_size` , `query_cache_size` ) é determinado pelo tipo de preço e vCores do servidor. Consulte [parâmetros do servidor](./concepts-server-parameters.md) para obter mais informações sobre esses limites.

Após a implantação inicial, um servidor do Azure para MySQL inclui tabelas de sistemas para informações de fuso horário, mas essas tabelas não são populadas. As tabelas de fuso horário podem ser preenchidas, chamando o procedimento armazenado `mysql.az_load_timezone` de uma ferramenta como a linha de comando do MySQL ou o Workbench do MySQL. Consulte os artigos [Portal do Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) para saber como chamar o procedimento armazenado e definir os fusos horários globais ou no nível da sessão.

Os plug-ins de senha, como "validate_password" e "caching_sha2_password", não são suportados pelo serviço.

## <a name="storage-engines"></a>Mecanismos de armazenamento

O MySQL dá suporte a muitos mecanismos de armazenamento. No banco de dados do Azure para MySQL, há suporte para os seguintes mecanismos de armazenamento e sem suporte:

### <a name="supported"></a>Com suporte
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Sem suporte
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Privilégios & suporte à manipulação de dados

Muitos parâmetros de servidor e configurações podem degradar inadvertidamente o desempenho do servidor ou negar as propriedades ACID do servidor MySQL. Para manter a integridade do serviço e o SLA em um nível de produto, esse serviço não expõe várias funções. 

O serviço MySQL não permite acesso direto ao sistema de arquivos subjacente. Não há suporte para alguns comandos de manipulação de dados. 

### <a name="unsupported"></a>Sem suporte

Os itens a seguir não têm suporte:
- Função DBA: restrita. Como alternativa, você pode usar o usuário administrador (criado durante a criação do novo servidor), permitindo que você execute a maioria das instruções DDL e DML. 
- SUPER privilégio: da mesma forma, o [super privilégio](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) é restrito.
- DEFINER: Requer superprivilégios para criar e é restrito. Se estiver importando dados usando um backup, remova os comandos `CREATE DEFINER`manualmente ou usando o comando `--skip-definer` ao executar um mysqldump.
- Bancos de dados do sistema: o [banco de dados do sistema MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) é somente leitura e usado para dar suporte a várias funcionalidades de PaaS. Você não pode fazer alterações no `mysql` banco de dados do sistema.
- `SELECT ... INTO OUTFILE`: Sem suporte no serviço.
- `LOAD_FILE(file_name)`: Sem suporte no serviço.

### <a name="supported"></a>Com suporte
- `LOAD DATA INFILE` tem suporte, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado por meio do SMB).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="point-in-time-restore"></a>Restauração pontual
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
- Não há suporte para restaurar um servidor eliminado.

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="storage-size"></a>Tamanho de armazenamento
- Consulte [tipos de preço](concepts-pricing-tiers.md) para saber os limites de tamanho de armazenamento por faixa de preço.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- A Instância do MySQL Server exibe a versão de servidor incorreta após a conexão ser estabelecida. Para obter a versão correta do mecanismo de instância de servidor, use o comando `select version();`.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões de banco de dados MySQL com suporte](concepts-supported-versions.md)
