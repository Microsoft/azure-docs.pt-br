---
title: Restaurar o banco de dados de exemplo AdventureWorks para a hiperescala PostgreSQL habilitada para Arc do Azure
description: Restaurar o banco de dados de exemplo AdventureWorks para a hiperescala PostgreSQL habilitada para Arc do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b1ee779be118fcafd0efa2bd2718ece1c34c50d1
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954321"
---
# <a name="restore-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Restaurar o banco de dados de exemplo AdventureWorks para a hiperescala PostgreSQL habilitada para Arc do Azure

O [AdventureWorks](/sql/samples/adventureworks-install-configure) é um banco de dados de exemplo que contém um banco de dados OLTP usado em tutoriais e exemplos. Ele é fornecido e mantido pela Microsoft como parte do [repositório GitHub de exemplos de SQL Server](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Um projeto de código-fonte aberto converteu o banco de dados AdventureWorks para ser compatível com a hiperescala PostgreSQL habilitada para o Arc do Azure.
- [Projeto original](https://github.com/lorint/AdventureWorks-for-Postgres)
- [Siga no projeto que converte previamente os arquivos CSV para serem compatíveis com o PostgreSQL](https://github.com/NorfolkDataSci/adventure-works-postgres)

Este documento descreve um processo simples para obter o banco de dados de exemplo AdventureWorks restaurado em seu grupo de servidores de hiperescala do PostgreSQL.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Baixar o arquivo de backup AdventureWorks

Baixe o arquivo AdventureWorks. SQL no contêiner do grupo de servidores de hiperescala do PostgreSQL. Neste exemplo, usaremos o `kubectl exec` comando para executar remotamente um comando no contêiner do grupo de servidores de hiperescala PostgreSQL para baixar o arquivo no contêiner. Você pode baixar esse arquivo de qualquer local acessível pelo `curl` . Use esse mesmo método se você tiver outros arquivos de backup de banco de dados que deseja efetuar pull no contêiner do grupo de servidores de hiperescala PostgreSQL. Quando estiver no contêiner do grupo de servidores de hiperescala do PostgreSQL, é fácil criar o banco de dados, o esquema e preencher os mesmos.

Execute um comando como este para baixar os arquivos substitua o valor do nome do pod e o nome do namespace antes de executá-lo:

> [!NOTE]
>  Seu contêiner precisará ter conectividade com a Internet em 443 para baixar o arquivo do GitHub.

> [!NOTE]
>  Use o nome do Pod do nó de coordenador do grupo de servidores de hiperescala Postgres. Seu nome é <server group name> -0.  Se você não tiver certeza do nome do pod, execute o comando `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-restore-the-adventureworks-database"></a>Etapa 2: restaurar o banco de dados AdventureWorks

Da mesma forma, você pode executar um comando kubectl exec para usar a ferramenta CLI do psql que está incluída nos contêineres do grupo de servidores de hiperescala do PostgreSQL para criar e carregar o banco de dados.

Execute um comando como este para criar o banco de dados vazio primeiro substituindo o valor do nome do pod e o nome do namespace antes de executá-lo.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

Em seguida, execute um comando como este para restaurar o banco de dados, substituindo o valor do nome do pod e o nome do namespace antes de executá-lo.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **Observação: você não verá tantos benefícios de desempenho de execução em hiperescala PostgreSQL habilitada para o Arc do Azure até que você Escale/distribua os dados/tabelas entre os nós de trabalho do seu grupo de servidores de hiperescala PostgreSQL. Consulte [as próximas etapas sugeridas](#suggested-next-steps).**

## <a name="suggested-next-steps"></a>Próximas etapas sugeridas
- Leia os conceitos e os guias de instruções do banco de dados do Azure para PostgreSQL de hiperescala para distribuir seus dados em vários nós de hiperescala do PostgreSQL e para se beneficiar de toda a potência do banco de dado do Azure para PostgreSQL em hiperescala. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* Nos documentos acima, ignore as seções **entrar no portal do Azure**, & **criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

- [Escalar horizontalmente o grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
