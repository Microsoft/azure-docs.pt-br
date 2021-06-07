---
title: 'PowerShell: habilitar BYOK (traga sua própria chave) TDE'
titleSuffix: Azure SQL Managed Instance
description: Saiba como configurar o Azure SQL Instância Gerenciada para começar a usar o BYOK (traga sua própria chave) Transparent Data Encryption (TDE) para criptografia em repouso usando o PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323203"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption no SQL Instância Gerenciada usando sua própria chave de Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Este exemplo de script do PowerShell configura Transparent Data Encryption (TDE) com uma chave gerenciada pelo cliente para o SQL Instância Gerenciada do Azure, usando uma chave de Azure Key Vault. Isso é muitas vezes chamado de cenário BYOK (traga sua própria chave) para TDE. Para saber mais, confira [Azure SQL Transparent Data Encryption com chave gerenciada pelo cliente](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma instância gerenciada existente. Consulte [usar o PowerShell para criar uma instância gerenciada](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Usar o PowerShell localmente ou usando Azure Cloud Shell requer Azure PowerShell 2.3.2 ou uma versão posterior. Se você precisar atualizar, consulte [instalar Azure PowerShell módulo](/powershell/azure/install-az-ps)ou execute o script de exemplo abaixo para instalar o módulo para o usuário atual:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/).

Exemplos adicionais de scripts do PowerShell para a Instância Gerenciada SQL podem ser encontrados nos [Scripts de PowerShell da Instância Gerenciada de SQL do Azure](../../database/powershell-script-content-guide.md).
