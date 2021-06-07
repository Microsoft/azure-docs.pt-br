---
title: Principais diferenças para Serviços de Machine Learning
description: Este artigo descreve as principais diferenças entre Serviços de Machine Learning no Azure SQL Instância Gerenciada e SQL Server Serviços de Machine Learning.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599537"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principais diferenças entre os Serviços de Machine Learning na Instância Gerenciada de SQL do Azure e SQL Server

Este artigo descreve algumas das principais diferenças na funcionalidade entre [serviços de Machine Learning no Azure SQL instância gerenciada](machine-learning-services-overview.md) e [SQL Server serviços de Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning).

## <a name="language-support"></a>Suporte ao idioma

Serviços de Machine Learning no SQL Instância Gerenciada e SQL Server dão suporte à [estrutura de extensibilidade](/sql/machine-learning/concepts/extensibility-framework)do Python e do R. As principais diferenças no SQL Instância Gerenciada são:

- Somente Python e R têm suporte. Linguagens externas, como Java, não podem ser adicionadas.

- As versões iniciais do Python e do R são diferentes:

  | Plataforma                   | Versão de runtime do Python           | Versões de runtime do R                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Instância Gerenciada do Azure SQL | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | Microsoft SQL Server 2017            | 3.5.2 e 3.7.2 (CU22 e posterior) | 3.3.3 e 3.5.2 (CU22 e posterior)     |
  | SQL Server 2016            | Não disponível                    | 3.2.2 e 3.5.2 (SP2 CU14 e posterior) |

## <a name="python-and-r-packages"></a>Pacotes do Python e do R

Não há suporte no SQL Instância Gerenciada para pacotes que dependem de tempos de execução externos (como Java) ou que precisam de acesso a APIs do sistema operacional para instalação ou uso.

Para obter mais informações sobre como gerenciar pacotes python e R, consulte:

- [Obter informações sobre o pacote do Python](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Obter informações sobre o pacote do R](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Governança de recursos

No SQL Instância Gerenciada, não é possível limitar os recursos de R por meio de [resource governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true), e não há suporte para pools de recursos externos.

Por padrão, os recursos do R são definidos como um máximo de 20% dos recursos do SQL Instância Gerenciada disponíveis quando a extensibilidade está habilitada. Para alterar esse percentual padrão, crie um tíquete de suporte do Azure em [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

A extensibilidade está habilitada com os seguintes comandos SQL (o SQL Instância Gerenciada será reiniciado e estará indisponível por alguns segundos):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Para desabilitar a extensibilidade e restaurar 100% de memória e recursos de CPU para SQL Server, use os seguintes comandos:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

O total de recursos disponíveis para o SQL Instância Gerenciada depende de qual camada de serviço você escolher. Para saber mais, confira [Modelos de compra do Banco de Dados SQL do Azure](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

O uso de memória depende de quanta memória é usada nos scripts R e o número de consultas paralelas em execução. Se não houver memória suficiente disponível para o R, você receberá uma mensagem de erro. As mensagens de erro comuns são:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Se você receber um desses erros, poderá resolvê-lo dimensionando seu banco de dados para uma camada de serviço superior.

## <a name="sql-managed-instance-pools"></a>Pools de Instância Gerenciada do SQL

Atualmente, não há suporte para Serviços de Machine Learning nos [pools de instância gerenciada do SQL do Azure (versão prévia)](instance-pools-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Consulte a visão geral [serviços de Machine Learning no Azure SQL instância gerenciada](machine-learning-services-overview.md).
- Para saber como usar o Python no Serviços de Machine Learning, consulte [executar scripts Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para saber como usar o R no Serviços de Machine Learning, consulte [executar scripts R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
