---
title: Alterações recente do Data Lake Analytics
description: Este artigo fornece uma lista contínua das alterações recentes feitas no Data Lake Analytics.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: cc3f9623f3b09c14e0e478252768890e4de3acad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025451"
---
# <a name="whats-new-in-data-lake-analytics"></a>Novidades no Data Lake Analytics

O Azure Data Lake Analytics é atualizado periodicamente em componentes específicos. Para se manter a par das atualizações mais recentes, este artigo fornece informações sobre:

- Notificação da versão prévia beta do componente principal
- As informações importantes de versão do componente, por exemplo: a lista de versões disponíveis do componente, a versão padrão atual etc.


## <a name="notification-of-key-component-beta-preview"></a>Notificação da versão prévia beta do componente principal

Nenhuma versão beta do componente principal disponível na versão prévia. 

## <a name="u-sql-runtime"></a>Runtime U-SQL

O runtime U-SQL do Azure Data Lake, incluindo o compilador, o otimizador e o gerenciador de trabalhos, é o que processa seu código U-SQL.

Quando você envia o trabalho do Azure Data Lake Analytics de qualquer ferramenta, seu trabalho usa o runtime padrão disponível atualmente no ambiente de produção. 

A versão do runtime será atualizada periodicamente. E o runtime anterior permanecerá disponível por algum tempo. Quando uma nova versão beta estiver pronta para versão prévia, ela também ficará disponível lá.

> [!CAUTION]
> - A escolha de um runtime diferente do padrão apresenta o potencial de interromper seus trabalhos do U-SQL. Recomendamos expressamente que você não use essas versões não padrão para produção, mas apenas para teste.
> - A versão de runtime não padrão tem um ciclo de vida fixo. Ela expira automaticamente.

A versão a seguir é a versão atual do runtime padrão.

- **release_20200707_scope_2b8d563_usql**

Para saber como solucionar falhas no runtime U-SQL, confira [Solucionar falhas do runtime U-SQL](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

O Azure Data Lake Analytics está usando o **.NET Framework v4.7.2**. 

Se seu código de script U-SQL do Azure Data Lake Analytics usa assemblies personalizados e esses assemblies usam bibliotecas .NET, valide o código para verificar se há interrupções.

Para saber como solucionar problemas de uma atualização do .NET, confira [Solucionar problemas de uma atualização do .NET](runtime-troubleshoot.md).

## <a name="release-note"></a>Nota sobre a versão

Para ver detalhes recentes de cada atualização, confira a [Nota sobre a versão do Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Próximas etapas

* Introdução ao Data Lake Analytics usando: o [Portal do Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)

