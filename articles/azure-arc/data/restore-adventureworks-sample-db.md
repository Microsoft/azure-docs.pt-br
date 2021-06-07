---
title: Restaurar o banco de dados de exemplo AdventureWorks no SQL Instância Gerenciada
description: Restaurar o banco de dados de exemplo AdventureWorks no SQL Instância Gerenciada
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641805"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>Restaurar o banco de dados de exemplo AdventureWorks no SQL Instância Gerenciada-Arc do Azure

O [AdventureWorks](/sql/samples/adventureworks-install-configure) é um banco de dados de exemplo que contém um banco de dados OLTP que geralmente é usado em tutoriais e exemplos. Ele é fornecido e mantido pela Microsoft como parte do [repositório GitHub de exemplos de SQL Server](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Este documento descreve um processo simples para obter o banco de dados de exemplo AdventureWorks restaurado em seu Arc do SQL Instância Gerenciada-Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Baixar o arquivo de backup AdventureWorks

Baixe o arquivo de backup do AdventureWorks (. bak) no contêiner do SQL Instância Gerenciada. Neste exemplo, use o `kubectl exec` comando para executar remotamente um comando dentro do contêiner do SQL instância gerenciada para baixar o arquivo. bak no contêiner. Baixe esse arquivo de qualquer local acessível pelo `wget` se você tiver outros arquivos de backup de banco de dados que deseja efetuar pull dentro do contêiner do SQL instância gerenciada. Quando estiver dentro do contêiner do SQL Instância Gerenciada, é fácil restaurar usando `RESTORE DATABASE` T-SQL padrão.

Execute um comando como este para baixar o arquivo. bak substituindo o valor do nome do pod e o nome do namespace antes de executá-lo.
> [!NOTE]
>  Seu contêiner precisará ter conectividade com a Internet em 443 para baixar o arquivo do GitHub

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Exemplo

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>Restaurar o banco de dados AdventureWorks

Da mesma forma, você pode executar um `kubectl` comando exec para usar a `sqlcmd` ferramenta CLI incluída no contêiner do SQL instância gerenciada para executar o comando T-SQL para restaurar o banco de dados.

Execute um comando como este para restaurar o banco de dados. Substitua o valor do nome do pod, a senha e o nome do namespace antes de executá-lo.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Exemplo

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
