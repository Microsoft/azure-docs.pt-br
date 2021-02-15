---
title: Principais diferenças para Serviços de Machine Learning (versão prévia)
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
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324526"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Principais diferenças entre os Serviços de Machine Learning na Instância Gerenciada de SQL do Azure e SQL Server

A funcionalidade de [serviços de Machine Learning no SQL instância gerenciada do Azure (versão prévia)](machine-learning-services-overview.md) é quase idêntica à [SQL Server serviços de Machine Learning](/sql/advanced-analytics/what-is-sql-server-machine-learning). A seguir estão algumas diferenças importantes.

> [!IMPORTANT]
> O Serviços de Machine Learning no Azure SQL Instância Gerenciada está atualmente em visualização pública. Para se inscrever, consulte [inscrever-se para a versão prévia](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Limitações de visualização

Durante a versão prévia, o serviço tem as seguintes limitações:

- As conexões de loopback não funcionam (consulte [conexão de loopback para SQL Server de um script Python ou R](/sql/machine-learning/connect/loopback-connection)).
- Pools de recursos externos não têm suporte.
- Somente Python e R têm suporte. Linguagens externas, como Java, não podem ser adicionadas.
- Não há suporte para cenários que usam a MPI ( [interface de transmissão de mensagens](/message-passing-interface/microsoft-mpi) ).

No caso de uma atualização de SLO (objetivo de nível de serviço), atualize o SLO e aumente um tíquete de suporte para reabilitar os limites de recursos dedicados para R/Python.

## <a name="language-support"></a>Suporte ao idioma

Serviços de Machine Learning no SQL Instância Gerenciada e SQL Server oferecem suporte ao Python e ao R [Extensibility Framework](/sql/advanced-analytics/concepts/extensibility-framework). As principais diferenças são:

- As versões iniciais do Python e do R são diferentes entre Serviços de Machine Learning no SQL Instância Gerenciada e SQL Server:

  | Sistema               | Python | R     |
  |----------------------|--------|-------|
  | Instância Gerenciada de SQL | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Não é necessário configurar `external scripts enabled` por meio de `sp_configure`. Quando você se [inscreveu](machine-learning-services-overview.md#signup) para a versão prévia, o Machine Learning está habilitado para o Azure SQL instância gerenciada.

## <a name="packages"></a>Pacotes

O gerenciamento de pacotes do Python e do R funciona de maneira diferente entre o SQL Instância Gerenciada e o SQL Server. Essas diferenças são:

- Não há suporte para pacotes que dependem de runtimes externos (como Java) ou que precisam acessar APIs de OS para instalação ou uso.
- Os pacotes podem executar chamadas de rede de saída (alterar de antes na versão prévia). Você pode definir as regras de segurança de saída corretas no nível do [grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md) para habilitar chamadas de rede de saída.

Para obter mais informações sobre como gerenciar pacotes python e R, consulte:

- [Obter informações sobre o pacote do Python](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Obter informações sobre o pacote do R](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Governança de recursos

Não é possível limitar os recursos de R por meio de [resource governor](/sql/relational-databases/resource-governor/resource-governor) e pools de recursos externos.

Durante a visualização pública, os recursos do R são definidos como um máximo de 20% dos recursos da Instância Gerenciada de SQL e dependem da camada de serviço escolhida. Para saber mais, confira [Modelos de compra do Banco de Dados SQL do Azure](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não houver memória suficiente disponível para o R, você receberá uma mensagem de erro. As mensagens de erro comuns são:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

O uso de memória depende de quanta memória é usada nos scripts R e o número de consultas paralelas em execução. Se você receber os erros acima, poderá dimensionar seu banco de dados para uma camada de serviço superior para resolvê-los.

## <a name="next-steps"></a>Próximas etapas

- Consulte a visão geral [serviços de Machine Learning no Azure SQL instância gerenciada](machine-learning-services-overview.md).
- Para saber como usar o Python no Serviços de Machine Learning, consulte [executar scripts Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Para saber como usar o R no Serviços de Machine Learning, consulte [executar scripts R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).