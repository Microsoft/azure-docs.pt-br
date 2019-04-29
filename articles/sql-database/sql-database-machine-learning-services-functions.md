---
title: Gravar funções avançadas de R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como escrever uma função do R para computação estatística avançada no banco de dados do SQL Azure usando serviços de Machine Learning (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702445"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Escrever funções avançadas do R no banco de dados do SQL Azure usando serviços de Machine Learning (versão prévia)

Este artigo descreve como inserir R matemático e procedimento armazenado de funções de utilitário em um SQL. Funções estatísticas avançadas que são complicadas para implementar em T-SQL podem ser feitas em R com apenas uma única linha de código.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um Banco de dados SQL do Azure com os Serviços do Machine Learning (com R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Criar um procedimento armazenado para gerar números aleatórios

Para simplificar, vamos usar o R `stats` pacote que tem instalado e carregado por padrão com o banco de dados do SQL Azure usando serviços de Machine Learning (versão prévia). O pacote contém centenas de funções para tarefas estatísticas comuns, entre eles o `rnorm` função. Esta função gera um número especificado de números aleatórios usando a distribuição normal, dado um desvio padrão e significa.

Por exemplo, o seguinte código R retorna 100 números em uma média de 50, dado um desvio padrão de 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Para chamar esta linha de R de T-SQL, execute `sp_execute_external_script` e adicione a função R no parâmetro de script de R, como este:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

E se você gostaria de tornar mais fácil de gerar um conjunto de números aleatórios diferente?

Isso é fácil quando combinado com o SQL. Você define um procedimento armazenado que obtém os argumentos do usuário e, em seguida, passe esses argumentos para o script de R como variáveis.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- A primeira linha define cada um dos parâmetros de entrada SQL que são necessários quando o procedimento armazenado é executado.

- A linha que começa com `@params` define todas as variáveis usadas pelo código R e os tipos de dados SQL correspondentes.

- As linhas imediatamente após mapeiam os nomes de parâmetro SQL para os nomes de variável R correspondentes.

Agora que você encapsulou a função do R em um procedimento armazenado, você pode facilmente chamar a função e passar valores diferentes, como este:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Usar funções de utilitário do R para solução de problemas

O `utils` pacote, instalado por padrão, fornece uma variedade de funções de utilitário para investigar o atual ambiente de R. Essas funções podem ser útil se você estiver encontrando discrepâncias na maneira como seu código R é executado no SQL e em ambientes externos. Por exemplo, você pode usar o R `memory.limit()` função para obter memória para o ambiente atual do R.

Porque o `utils` pacote é instalado, mas não é carregado por padrão, você deve usar o `library()` função carregá-lo pela primeira vez.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Muitos usuários gostam de usar as funções de tempo do sistema em R, como `system.time` e `proc.time`, para capturar o tempo usado por processos de R e analisar problemas de desempenho.
