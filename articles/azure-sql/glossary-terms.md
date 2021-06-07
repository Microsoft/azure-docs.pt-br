---
title: Glossário de termos
titleSuffix: Azure SQL
description: Um glossário de termos para trabalhar com o banco de dados SQL do Azure, o SQL Instância Gerenciada do Azure e o SQL na VM do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/09/2020
ms.openlocfilehash: 8bd1e312463cf89ae30d54bc4a32a497fc7a9a78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249645"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Glossário de termos do Banco de Dados SQL do Azure
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Banco de Dados SQL do Azure

|Contexto|Termo|Mais informações|
|:---|:---|:---|
|Serviço do Azure|Banco de Dados SQL do Azure ou Banco de Dados SQL|[Banco de Dados SQL do Azure](database/sql-database-paas-overview.md)|
|Modelo de compra|Modelo de compra baseado em DTU|[Modelo de compra baseado em DTU](database/service-tiers-dtu.md)|
||Modelo de compra baseado em vCore|[Modelo de compra baseado em vCore](database/service-tiers-vcore.md)|
|Opção de implantação |Banco de dados individual|[Bancos de dados únicos](database/single-database-overview.md)|
||Pool elástico|[Pool elástico](database/elastic-pool-overview.md)|
|Camada de serviço|Básico, Standard, Premium, Uso Geral, hiperescala Comercialmente Crítico|Para as camadas de serviço no modelo vCore, consulte [camadas de serviço do banco de dados SQL](database/service-tiers-vcore.md#service-tiers). Para as camadas de serviço no modelo de DTU, consulte [modelo de DTU](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Camada de computação|Computação sem servidor|[Computação sem servidor](database/service-tiers-vcore.md#compute-tiers)
||Computação provisionada|[Computação provisionada](database/service-tiers-vcore.md#compute-tiers)
|Geração de computação|Gen5, série M, Fsv2, série DC|[Gerações de hardware](database/service-tiers-vcore.md#hardware-generations)
|Entidade do servidor| Servidor |[Servidores SQL Lógicos](database/logical-servers.md)|
|Tipo de recurso|vCore|Um núcleo de CPU fornecido ao recurso de computação para um único banco de dados, pool elástico. |
||Tamanho da computação e valor de armazenamento|O tamanho da computação é a quantidade máxima de CPU, memória e outros recursos não relacionados ao armazenamento disponíveis para um único banco de dados ou pool elástico.  Tamanho do armazenamento é a quantidade máxima de armazenamento disponível para um único banco de dados ou pool elástico. Para opções de dimensionamento no modelo vCore, consulte [bancos de dados individuais VCORE](database/resource-limits-vcore-single-databases.md)e [pools elásticos de VCORE](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  Para opções de dimensionamento no modelo de DTU, consulte [bancos de dados individuais de DTU](database/resource-limits-dtu-single-databases.md) e [pools elásticos de DTU](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Instância Gerenciada do Azure SQL

|Contexto|Termo|Mais informações|
|:---|:---|:---|
|Serviço do Azure|Instância Gerenciada do Azure SQL|[Instância Gerenciada de SQL](managed-instance/sql-managed-instance-paas-overview.md)|
|Modelo de compra|Modelo de compra baseado em vCore|[Modelo de compra baseado em vCore](database/service-tiers-vcore.md)|
|Opção de implantação |Instância única|[Instância única](managed-instance/sql-managed-instance-paas-overview.md)|
||Pool de instâncias (visualização)|[Pools da instância](managed-instance/instance-pools-overview.md)|
|Camada de serviço|Uso Geral, Comercialmente Crítico|[Camadas de serviço do SQL Instância Gerenciada](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Camada de computação|Computação provisionada|[Computação provisionada](database/service-tiers-vcore.md#compute-tiers)|
|Geração de computação|Gen5|[Gerações de hardware](database/service-tiers-vcore.md#hardware-generations)
|Entidade do servidor|Instância ou instância gerenciada| N/A como o SQL Instância Gerenciada está em si mesmo no servidor |
|Tipo de recurso|vCore|Um núcleo de CPU fornecido ao recurso de computação para o SQL Instância Gerenciada.|
||Tamanho da computação e valor de armazenamento|O tamanho da computação é a quantidade máxima de CPU, memória e outros recursos não relacionados ao armazenamento para o SQL Instância Gerenciada.  Tamanho do armazenamento é a quantidade máxima de armazenamento disponível para um Instância Gerenciada do SQL.  Para opções de dimensionamento, [instâncias gerenciadas do SQL](managed-instance/resource-limits.md). |
