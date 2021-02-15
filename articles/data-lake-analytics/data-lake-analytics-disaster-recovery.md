---
title: Diretrizes de recuperação de desastre para Azure Data Lake Analytics
description: Saiba como planejar a recuperação de desastres para suas contas de Azure Data Lake Analytics.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220968"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Diretrizes de recuperação de desastre para Azure Data Lake Analytics

O Azure Data Lake Analytics é um serviço de análise sob demanda que simplifica big data. Em vez de implantar, configurar e ajustar o hardware, você escreve consultas para transformar seus dados e extrair informações importantes. O serviço de análise pode manipular trabalhos de qualquer escala de maneira instantânea, simplesmente configurando o controle para a quantidade de potência necessária. Você só paga pelo trabalho quando ele está em execução, o que o torna econômico. Este artigo fornece orientação sobre como proteger seus trabalhos contra interrupções raras em toda a região ou exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres

Ao usar Azure Data Lake Analytics, é essencial preparar seu próprio plano de recuperação de desastres. Este artigo ajuda você a criar um plano de recuperação de desastres. Há recursos adicionais que podem ajudá-lo a criar seu próprio plano:
- [Recuperação de desastre e falha para aplicativos do Azure](/azure/architecture/reliability/disaster-recovery)
- [Orientações técnicas de resiliência do Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Práticas recomendadas e diretrizes de cenário

Você pode executar um trabalho do U-SQL recorrente em uma conta do ADLA em uma região que lê e grava tabelas U-SQL, bem como dados não estruturados.  Prepare-se para um desastre executando estas etapas:

1. Crie contas ADLA e ADLS na região secundária que será usada durante uma interrupção.

   > [!NOTE]
   > Como os nomes de conta são globalmente exclusivos, use um esquema de nomenclatura consistente que indica qual conta é secundária.

2. Para dados não estruturados, faça referência [à orientação de recuperação de desastre para dados no Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Para os dados estruturados armazenados em tabelas e bancos de dado do ADLA, crie cópias dos artefatos de metadados, como bancos de dados, tabelas, funções com valor de tabela e assemblies. Você precisa ressincronizar periodicamente esses artefatos quando as alterações acontecem na produção. Por exemplo, os dados inseridos recentemente têm que ser replicados para a região secundária copiando os dados e inserindo na tabela secundária.

   > [!NOTE]
   > Esses nomes de objeto têm o escopo definido para a conta secundária e não são globalmente exclusivos, portanto, eles podem ter os mesmos nomes da conta de produção primária.

Durante uma interrupção, você precisa atualizar seus scripts para que os caminhos de entrada apontem para o ponto de extremidade secundário. Em seguida, os usuários enviam seus trabalhos para a conta ADLA na região secundária. A saída do trabalho será então gravada na conta ADLA e ADLS na região secundária.

## <a name="next-steps"></a>Próximas etapas

[Orientação de recuperação de desastre para dados no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
