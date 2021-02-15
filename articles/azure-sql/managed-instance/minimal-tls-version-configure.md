---
title: Configurar a instância gerenciada de versão mínima do TLS
description: Saiba como configurar a versão mínima do TLS para a instância gerenciada
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788393"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Configurar a versão mínima do TLS no Azure SQL Instância Gerenciada
A configuração de versão mínima do [protocolo TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite que os clientes controlem a versão do TLS usada por seus instância gerenciada de SQL do Azure.

No momento, damos suporte a TLS 1,0, 1,1 e 1,2. A definição de uma versão mínima do TLS garante que as versões mais recentes do TLS sejam suportadas. Por exemplo, ex.: escolhendo uma versão de TLS maior que 1,1. significa que somente as conexões com TLS 1,1 e 1,2 são aceitas e o TLS 1,0 é rejeitado. Após o teste para confirmar que seus aplicativos dão suporte a ele, é recomendável definir a versão mínima do TLS como 1,2, já que ela inclui correções para vulnerabilidades encontradas em versões anteriores e é a versão mais recente do TLS com suporte no Azure SQL Instância Gerenciada.

Para clientes com aplicativos que dependem de versões mais antigas do TLS, é recomendável definir a versão mínima do TLS de acordo com os requisitos de seus aplicativos. Para clientes que dependem de aplicativos para se conectarem usando uma conexão não criptografada, recomendamos não definir nenhuma versão mínima de TLS. 

Para obter mais informações, consulte [Considerações sobre TLS para conectividade de banco de dados SQL](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Depois de definir a versão mínima do TLS, as tentativas de logon de clientes que estão usando uma versão TLS inferior à versão mínima do TLS do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Definir a versão mínima do TLS por meio do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de **versão mínima do TLS** no nível da instância:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Definir a versão mínima do TLS via CLI do Azure

> [!IMPORTANT]
> Todos os scripts nesta seção exigem [CLI do Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

O script de CLI a seguir mostra como alterar a configuração de **versão mínima do TLS** em um shell bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```