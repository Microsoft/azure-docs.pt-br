---
title: Descobrir endereço IP do ponto de extremidade de gerenciamento
titleSuffix: Azure SQL Managed Instance
description: Saiba como obter o endereço IP público do ponto de extremidade de gerenciamento do SQL Instância Gerenciada do Azure e verificar sua proteção de firewall interna
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91617359"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Determinar o endereço IP do ponto de extremidade de gerenciamento-SQL do Azure Instância Gerenciada 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O cluster virtual do Azure SQL Instância Gerenciada contém um ponto de extremidade de gerenciamento que o Azure usa para operações de gerenciamento. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível da rede e verificação de certificado mútua no nível do aplicativo. Você pode determinar o endereço IP do ponto de extremidade de gerenciamento, mas não pode acessar esse ponto de extremidade.

Para determinar o endereço IP de gerenciamento, faça uma [pesquisa de DNS](/windows-server/administration/windows-commands/nslookup) em seu FQDN do SQL instância gerenciada: `mi-name.zone_id.database.windows.net` . Isso retornará uma entrada DNS como essa `trx.region-a.worker.vnet.database.windows.net` . Em seguida, você pode fazer uma pesquisa de DNS nesse FQDN com a ". vnet" removida. Isso retornará o endereço IP de gerenciamento. 

Esse código do PowerShell fará tudo para você se substituir \<MI FQDN\> pela entrada DNS do SQL instância gerenciada: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Para obter mais informações sobre o SQL Instância Gerenciada e conectividade, consulte [arquitetura de conectividade do sql instância gerenciada do Azure](connectivity-architecture-overview.md).
