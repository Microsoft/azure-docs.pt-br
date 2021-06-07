---
title: A importação e a exportação de um banco de dados leva muito tempo
description: O banco de dados SQL do Azure e o serviço de importação/exportação do Azure SQL Instância Gerenciada levam muito tempo para importar ou exportar um banco de dados
ms.custom: seo-lt-2019, sqldbrb=1
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: e69bba858ccf62f1b3a3b45b08771ddba71f11cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92671404"
---
# <a name="azure-sql-database-and-managed-instance-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>O banco de dados SQL do Azure e Instância Gerenciada serviço de importação/exportação leva muito tempo para importar ou exportar um banco de dados

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Quando você usa o serviço de importação/exportação, o processo pode demorar mais do que o esperado. Este artigo descreve as possíveis causas para esse atraso e métodos de solução alternativa alternativos.

## <a name="azure-sql-database-importexport-service"></a>Serviço de importação/exportação do banco de dados SQL do Azure

O serviço de importação/exportação do banco de dados SQL do Azure é um serviço Web baseado em REST que é executado em cada data center do Azure. Esse serviço é chamado quando você usa a opção [importar banco de dados](database-import.md#using-azure-portal) ou [Exportar](./database-import.md#using-azure-portal) para mover seu banco de dados no portal do Azure. O serviço fornece enfileiramento de solicitações e serviços de computação gratuitos para executar importações e exportações entre o banco de dados SQL do Azure e o armazenamento de BLOBs

As operações de importação e exportação não representam um backup de banco de dados físico tradicional, mas sim um backup lógico do banco de dados que usa um formato BACPAC especial. O formato BACPAC permite que você evite ter que usar um formato físico que possa variar entre as versões do Microsoft SQL Server, do banco de dados SQL do Azure e do SQL Instância Gerenciada do Azure.

## <a name="what-causes-delays-in-the-process"></a>O que causa atrasos no processo?

O serviço de importação/exportação do banco de dados SQL do Azure fornece um número limitado de VMs (máquinas virtuais) de computação por região para processar operações de importação e exportação. As VMs de computação são hospedadas por região para garantir que a importação ou exportação Evite atrasos e encargos de largura de banda entre regiões. Se muitas solicitações forem feitas ao mesmo tempo na mesma região, poderão ocorrer atrasos significativos no processamento das operações. O tempo necessário para concluir as solicitações pode variar de alguns segundos a muitas horas.

> [!NOTE]
> Se uma solicitação não for processada dentro de quatro dias, o serviço cancelará automaticamente a solicitação.

## <a name="recommended-solutions"></a>Soluções recomendadas

Se suas exportações de banco de dados forem usadas somente para recuperação de exclusão acidental de dado, todas as edições do banco de dados SQL do Azure fornecerão o recurso de restauração de autoatendimento de backups gerados pelo sistema. Mas se você precisar dessas exportações por outros motivos, e se precisar de um desempenho de importação/exportação consistentemente mais rápido ou previsível, considere as seguintes opções:

* [Exporte para um arquivo BACPAC usando o utilitário SqlPackage](./database-export.md#sqlpackage-utility).
* [Exportar para um arquivo BACPAC usando SQL Server Management Studio (SSMS)](./database-export.md#sql-server-management-studio-ssms).
* Execute a importação ou exportação de BACPAC diretamente em seu código usando a API do Microsoft SQL Server Data-Tier Application Framework (DacFx). Para obter informações adicionais, consulte:
  * [Exportar um aplicativo da camada de dados](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Namespace Microsoft. SqlServer. Dac](/dotnet/api/microsoft.sqlserver.dac)
  * [Baixar o DACFx](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-a-database"></a>Itens a serem considerados ao exportar ou importar um banco de dados

* Todos os métodos discutidos neste artigo usam a cota de DTU (unidade de transação de banco de dados), que causa limitação pelo serviço de banco de dados SQL do Azure. Você pode [exibir as estatísticas de DTU para o banco de dados no portal do Azure](./monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Se o banco de dados tiver atingido seus limites de recursos, [atualize a camada de serviço](./scale-resources.md) para adicionar mais recursos.
* O ideal é que você execute aplicativos cliente (como o utilitário SqlPackage ou seu aplicativo de DAC personalizado) de uma VM na mesma região que o banco de dados. Caso contrário, você poderá ter problemas de desempenho relacionados à latência de rede.
* A exportação de tabelas grandes sem índices clusterizados pode ser muito lenta ou até mesmo causar falha. Esse comportamento ocorre porque a tabela não pode ser dividida e exportada em paralelo. Em vez disso, ele deve ser exportado em uma única transação e isso causa um baixo desempenho e falha potencial durante a exportação, especialmente para tabelas grandes.


## <a name="related-documents"></a>Documentos relacionados

[Considerações ao exportar um banco de dados](./database-export.md#considerations)