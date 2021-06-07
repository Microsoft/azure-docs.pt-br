---
title: Limitações-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve as limitações no banco de dados do Azure para MySQL-servidor flexível, como o número de opções de mecanismo de armazenamento e conexão.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 48aef337326d58b2a503dc48862571efde0d37ab
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034513"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Limitações no banco de dados do Azure para MySQL – servidor flexível (visualização)

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo descreve as limitações no serviço de servidor flexível do banco de dados do Azure para MySQL. Também são aplicáveis [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) no mecanismo de banco de dados MySQL. Se você quiser saber mais sobre as camadas de recurso (computação, memória, armazenamento), consulte o artigo [computação e armazenamento](concepts-compute-storage.md) .

## <a name="server-parameters"></a>Parâmetros do Servidor

> [!NOTE]
> Se você estiver procurando por valores mínimos/máximos para parâmetros de servidor como `max_connections` e `innodb_buffer_pool_size` , essas informações foram movidas para o artigo parâmetros de servidor conceitos [parâmetros de servidor](./concepts-server-parameters.md) .

O banco de dados do Azure para MySQL dá suporte ao ajuste dos valores dos parâmetros do servidor. O valor mínimo e máximo de alguns parâmetros (ex. `max_connections`, `join_buffer_size` , `query_cache_size` ) é determinado pela camada de computação e o tamanho de computação do servidor. Consulte [parâmetros do servidor](./concepts-server-parameters.md) para obter mais informações sobre esses limites.

Os plug-ins de senha, como "validate_password" e "caching_sha2_password", não são suportados pelo serviço.

## <a name="storage-engines"></a>Mecanismos de armazenamento

O MySQL dá suporte a muitos mecanismos de armazenamento. No banco de dados do Azure para MySQL servidor flexível, os seguintes mecanismos de armazenamento têm suporte e não têm suporte:

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

### <a name="supported"></a>Com suporte
- `LOAD DATA INFILE` tem suporte, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado por meio do SMB).

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="zone-redundant-ha"></a>HA com redundância de zona
- Essa configuração só pode ser definida durante a criação do servidor.
- Sem suporte na camada de computação expansível.

### <a name="networking"></a>Rede
- O método de conectividade não pode ser alterado após a criação do servidor. Se o servidor for criado com *acesso privado (integração VNet)*, ele não poderá ser alterado para *acesso público (endereços IP permitidos)* após Create e vice-versa
- O TLS/SSL está habilitado por padrão e não pode ser desabilitado.
- A versão mínima do TLS com suporte no servidor é TLS 1.2. Consulte [conectar-se usando TLS/SSL](./how-to-connect-tls-ssl.md) para saber mais.

### <a name="stopstart-operation"></a>Parar/iniciar operação
- Sem suporte com configurações de HA com redundância de zona (primária e em espera).
- Sem suporte com configurações de réplica de leitura (origem e réplicas).

### <a name="scale-operations"></a>Operações de dimensionamento
- Não há suporte para a redução do armazenamento de servidor provisionado.

### <a name="read-replicas"></a>Réplicas de leitura
- Sem suporte com configurações de HA com redundância de zona (primária e em espera).

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre as versões do mecanismo de banco de dados principal. Se você quiser atualizar a versão principal, faça um [despejo e restaure](../concepts-migrate-dump-restore.md) -o em um servidor criado com a nova versão do mecanismo.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Com a restauração pontual, novos servidores são criados com as mesmas configurações de computação e armazenamento que o servidor de origem no qual se baseiam. A computação do servidor recém restaurada pode ser dimensionada verticalmente após a criação do servidor.
- Não há suporte para a restauração de um servidor excluído.

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>Recursos disponíveis em um único servidor, mas ainda não têm suporte no servidor flexível 
Nem todos os recursos disponíveis no banco de dados do Azure para MySQL-um servidor único está disponível no servidor flexível ainda. Para obter uma lista completa da comparação de recursos entre um servidor único e um servidor flexível, consulte [a opção certa servidor MySQL na documentação do Azure.](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)

## <a name="next-steps"></a>Próximas etapas

- Saiba [como escolher a opção de servidor MySQL correta na documentação do Azure](../select-right-deployment-type.md)
- Entenda [o que está disponível para as opções de computação e armazenamento em um servidor flexível](concepts-compute-storage.md)
- Saiba mais sobre [as versões do MySQL com suporte](concepts-supported-versions.md)
- Início rápido: [use o portal do Azure para criar um servidor flexível do banco de dados do Azure para MySQL](quickstart-create-server-portal.md)
