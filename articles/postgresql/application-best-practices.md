---
title: Práticas recomendadas de desenvolvimento de aplicativo-banco de dados do Azure para PostgreSQL
description: Saiba mais sobre as práticas recomendadas para criar um aplicativo usando o banco de dados do Azure para PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364553"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Práticas recomendadas para criar um aplicativo com o banco de dados do Azure para PostgreSQL

Aqui estão algumas práticas recomendadas para ajudá-lo a criar um aplicativo pronto para a nuvem usando o banco de dados do Azure para PostgreSQL. Essas práticas recomendadas podem reduzir o tempo de desenvolvimento para seu aplicativo.

## <a name="configuration-of-application-and-database-resources"></a>Configuração de recursos de aplicativo e banco de dados

### <a name="keep-the-application-and-database-in-the-same-region"></a>Manter o aplicativo e o banco de dados na mesma região
Verifique se todas as suas dependências estão na mesma região ao implantar seu aplicativo no Azure. A difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo.

### <a name="keep-your-postgresql-server-secure"></a>Mantenha seu servidor PostgreSQL seguro
Configure seu servidor PostgreSQL para ser [seguro](./concepts-security.md) e não acessível publicamente. Use uma destas opções para proteger seu servidor:
- [Regras de firewall](./concepts-firewall-rules.md)
- [Redes virtuais](./concepts-data-access-and-security-vnet.md)
- [Link Privado do Azure](./concepts-data-access-and-security-private-link.md)

Para segurança, você deve sempre se conectar ao servidor PostgreSQL por SSL e configurar seu servidor PostgreSQL e seu aplicativo para usar o TLS 1,2. Consulte [como configurar SSL/TLS](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Ajustar os parâmetros do servidor
Para cargas de trabalho de leitura pesadas que ajustam parâmetros de servidor `tmp_table_size` e `max_heap_table_size` podem ajudar a otimizar o melhor desempenho. Para calcular os valores necessários para essas variáveis, examine o total de valores de memória por conexão e a memória base. A soma dos parâmetros de memória por conexão, exceto `tmp_table_size` , combinada com as contas de memória base para a memória total do servidor.

### <a name="use-environment-variables-for-connection-information"></a>Usar variáveis de ambiente para informações de conexão
Não salve suas credenciais de banco de dados no código do aplicativo. Dependendo do aplicativo de front-end, siga as orientações para configurar as variáveis de ambiente. Para o uso do serviço de aplicativo, consulte[como definir configurações de aplicativo](../app-service/configure-common.md#configure-app-settings) e para o serviço Kuberentes do Azure, consulte [como usar segredos do kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/).

## <a name="performance-and-resiliency"></a>Desempenho e resiliência
Aqui estão algumas ferramentas e práticas que você pode usar para ajudar a depurar problemas de desempenho com seu aplicativo.

### <a name="use-connection-pooling"></a>Usar o pool de conexões
Com o pool de conexões, um conjunto fixo de conexões é estabelecido no tempo de inicialização e mantido. Isso também ajuda a reduzir a fragmentação de memória no servidor que é causado pelas novas conexões dinâmicas estabelecidas no servidor de banco de dados. O pooling de conexão poderá ser configurado no lado do aplicativo se a estrutura do aplicativo ou o driver de banco de dados oferecer suporte a ele. Se isso não for suportado, a outra opção recomendada é aproveitar um serviço de pooler de conexão de proxy como [PgBouncer](https://pgbouncer.github.io/) ou [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) em execução fora do aplicativo e conectar-se ao servidor de banco de dados. PgBouncer e pgpool são ferramentas baseadas na Comunidade que funcionam com o banco de dados do Azure para PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Lógica de repetição para manipular erros transitórios
Seu aplicativo pode apresentar erros transitórios em que as conexões com o banco de dados são descartadas ou perdidas intermitentemente. Nessas situações, o servidor está em execução após uma ou duas repetições em 5 a 10 segundos. Uma prática recomendada é aguardar 5 segundos antes de sua primeira tentativa. Em seguida, siga cada nova tentativa aumentando a espera gradualmente, até 60 segundos. Limite o número máximo de repetições em que o seu aplicativo considera que a operação falhou, para que você possa investigar ainda mais. Consulte [como solucionar problemas de erros de conexão](./concepts-connectivity.md) para saber mais.

### <a name="enable-read-replication-to-mitigate-failovers"></a>Habilitar a replicação de leitura para mitigar failovers
Você pode usar [replicação de dados](./concepts-read-replicas.md) para cenários de failover. Quando você estiver usando réplicas de leitura, ocorrerá nenhum failover automatizado entre os servidores de origem e de réplica. Você perceberá um atraso entre a origem e a réplica porque a replicação é assíncrona. O retardo de rede pode ser influenciado por muitos fatores, como o tamanho da carga de trabalho em execução no servidor de origem e a latência entre datacenters. Na maioria dos casos, a latência de réplica varia de alguns segundos a alguns minutos.


## <a name="database-deployment"></a>Implantação de banco de dados

### <a name="configure-cicd-deployment-pipeline"></a>Configurar o pipeline de implantação de CI/CD
Ocasionalmente, você precisa implantar alterações em seu banco de dados. Nesses casos, você pode usar a CI (integração contínua) por meio de [ações do GitHub](https://github.com/Azure/postgresql/blob/master/README.md) para o servidor PostgreSQL atualizar o banco de dados executando um script personalizado em relação a ele.

### <a name="define-manual-database-deployment-process"></a>Definir processo de implantação manual de banco de dados
Durante a implantação manual do banco de dados, siga estas etapas para minimizar o tempo de inatividade ou reduzir o risco de falha na implantação:

- Crie uma cópia de um banco de dados de produção em um novo banco de dados usando pg_dump.
- Atualize o novo banco de dados com suas novas alterações de esquema ou atualizações necessárias para seu banco de dados.
- Coloque o banco de dados de produção em um estado somente leitura. Você não deve ter operações de gravação no banco de dados de produção até que a implantação seja concluída.
- Teste seu aplicativo com o banco de dados recentemente atualizado da etapa 1.
- Implante as alterações do aplicativo e verifique se o aplicativo agora está usando o novo banco de dados que tem as atualizações mais recentes.
- Mantenha o banco de dados de produção antigo para que você possa reverter as alterações. Em seguida, você pode avaliar para excluir o banco de dados de produção antigo ou exportá-lo no armazenamento do Azure, se necessário.

>  [!NOTE]
> Se o aplicativo for como um aplicativo de comércio eletrônico e você não puder colocá-lo no estado somente leitura, implante as alterações diretamente no banco de dados de produção depois de fazer um backup. A alteração de alterações deve ocorrer fora do horário de pico, com baixo tráfego para o aplicativo para minimizar o impacto, pois alguns usuários podem ter solicitações com falha. Verifique se o código do aplicativo também lida com as solicitações com falha.

## <a name="database-schema-and-queries"></a>Esquema e consultas de banco de dados
Aqui estão algumas dicas para ter em mente quando você cria seu esquema de banco de dados e suas consultas.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>Usar BIGINT ou UUID para chaves primárias
Ao criar aplicativos personalizados ou algumas estruturas que eles podem usar `INT` em vez de `BIGINT` para chaves primárias. Ao usar ```INT``` o, você corre o risco de onde o valor em seu banco de dados pode exceder a capacidade de armazenamento do ```INT``` tipo de dado. Fazer essa alteração em um aplicativo de produção existente pode ser demorado com mais tempo de desenvolvimento. Outra opção é usar o [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) para chaves primárias. Esse identificador usa uma cadeia de caracteres de 128 bits gerada automaticamente, por exemplo ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Saiba mais sobre [os tipos de dados PostgreSQL](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Usar índices

Há muitos tipos de [índices](https://www.postgresql.org/docs/9.1/indexes.html) no postgres que podem ser usados de maneiras diferentes. O uso de um índice ajuda o servidor a localizar e recuperar linhas específicas com muito mais rapidez do que poderia fazer sem um índice. Mas os índices também adicionam sobrecarga ao servidor de banco de dados, portanto, evite ter muitos índices.

### <a name="use-autovacuum"></a>Usar o autovácuo
Você pode otimizar seu servidor com o vácuo autoaspirar em um servidor de banco de dados do Azure para PostgreSQL. O PostgreSQL permite maior simultaneidade de banco de dados, mas com cada atualização resulta em Insert e Delete. Para excluir, os registros são marcados de forma flexível que serão limpos posteriormente. Para executar essas tarefas, o PostgreSQL executa um trabalho de vácuo. Se você não executar o vácuo periodicamente, as tuplas inativas acumuladas poderão levar a:

- Sobrecarga de dados, como bancos de dados e tabelas maiores.
- Maiores índices de qualidade inferior.
- Aumento de E/S.

Saiba mais sobre [como otimizar com o autovácuo](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Usar pg_stats_statements
Pg_stat_statements é uma extensão do PostgreSQL habilitada por padrão no Banco de Dados do Azure para PostgreSQL. A extensão fornece um modo para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. Consulte [como usar pg_statement](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Usar o Repositório de Consultas
O recurso [Repositório de Consultas](./concepts-query-store.md) no Banco de Dados do Azure para PostgreSQL fornece um método mais eficaz para rastrear as estatísticas da consulta. Recomendamos esse recurso como uma alternativa ao uso de pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Otimizar inserções em massa e usar dados transitórios
Se você tem operações de carga de trabalho que envolvem dados temporários ou inserem grandes conjuntos de dados em massa, considere o uso de tabelas não registradas. Por padrão, ele fornece atomicidade e durabilidade. Atomicidade, consistência, isolamento e durabilidade são as propriedades ACID. Consulte [como otimizar inserções em massa](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Próximas etapas
[Guia do postgres](http://postgresguide.com/)
