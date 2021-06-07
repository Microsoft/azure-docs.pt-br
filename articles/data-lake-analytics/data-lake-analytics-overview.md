---
title: Visão geral do Azure Data Lake Analytics
description: O Data Lake Analytics permite a você conduzir seus negócios usando insights decorrentes de seus dados de nuvem em qualquer escala.
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: f2916b45c04aac3e36e8dfb82a6bb9b332f55286
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449145"
---
# <a name="what-is-azure-data-lake-analytics"></a>O que é a Análise Azure Data Lake?

O Azure Data Lake Analytics é um serviço de análise sob demanda que simplifica big data. Em vez de implantar, configurar e ajustar o hardware, você escreve consultas para transformar seus dados e extrair informações importantes. O serviço de análise pode manipular trabalhos de qualquer escala de maneira instantânea, simplesmente configurando o controle para a quantidade de potência necessária. Você só paga pelo trabalho quando ele está em execução, o que o torna econômico. 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Informações de atualização recentes do Azure Data Lake Analytics

O serviço do Azure Data Lake Analytics é atualizado periodicamente para finalidades específicas. Continuamos fornecendo suporte para esse serviço com a atualização do componente, a versão prévia beta do componente e assim por diante. 

- Para obter informações gerais sobre a atualização recente, confira [O que há de novo no Data Lake Analytics?](data-lake-analytics-whats-new.md).
- Para ver os detalhes de cada atualização, confira a [Nota sobre a versão do Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).

## <a name="dynamic-scaling"></a>Escala dinâmica
  
O Data Lake Analytics provisiona recursos de maneira dinâmica e permite a análise de dados em terabytes ou até mesmo em petabytes. Pague apenas pela capacidade de processamento usada. À medida que o tamanho dos dados armazenados ou da quantidade de recursos de computação usados aumenta ou diminui, você não precisa reescrever os códigos. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Use ferramentas com as quais você está familiarizado para desenvolver, depurar e otimizar de maneira mais rápida e inteligente
  
O Data Lake Analytics integra-se ao Visual Studio profundamente. Você pode usar ferramentas conhecidas para executar, depurar e ajustar seu código. A visualização dos trabalhos do U-SQL permite ver como ocorre a execução de códigos em escala, para que você possa identificar gargalos de desempenho e otimizar custos.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: simples e familiar, poderoso e extensível
  
A Análise Data Lake inclui o U-SQL, uma linguagem de consulta que estende a natureza declarativa simples e familiar do SQL com o poder expressivo do C#. A linguagem U-SQL usa o mesmo runtime distribuído que alimenta o data lake interno da Microsoft em escala de exabytes. Os desenvolvedores do SQL e do .NET agora podem processar e analisar dados com as habilidades que eles já têm.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Integração total com seus investimentos de TI
  
O Data Lake Analytics usa seus investimentos de TI em identidade, gerenciamento e segurança. Isso simplifica a governança de dados e facilita estender seus aplicativos de dados atuais. O Data Lake Analytics é integrado ao Active Directory para o gerenciamento e permissões de usuário e é fornecido com o monitoramento e auditoria internos.

## <a name="affordable-and-cost-effective"></a>Financeiramente viável e econômico

A Análise Data Lake é uma solução econômica para executar cargas de trabalho de Big Data. Você paga por trabalho quando os dados são processados. Não é necessário nenhum hardware, licenças ou contratos de suporte específico do serviço. O sistema é automaticamente escalado vertical ou horizontalmente conforme a tarefa é iniciada e concluída, para que você só pague pelo serviço de que precisa. [Saiba mais sobre o controle de custos e como reduzir os custos](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>Funciona com todos os seus dados do Azure
  
O Data Lake Analytics funciona com o Azure Data Lake Storage para proporcionar o mais alto desempenho, a taxa de transferência e a paralelização e funciona com blobs do Armazenamento do Azure, o Banco de Dados SQL do Azure e o Azure Synapse Analytics.

## <a name="in-region-data-residency"></a>Residência de dados na região
  
O Data Lake Analytics não move nem armazena dados do cliente fora da região em que ele está implantado.


## <a name="next-steps"></a>Próximas etapas

* Confira a atualização recente do Azure Data Lake Analytics usando [O que há de novo no Azure Data Lake Analytics?](data-lake-analytics-whats-new.md)
* Introdução ao Data Lake Analytics usando: o [Portal do Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md)
* Gerencie o Azure Data Lake Analytics usando [portal do Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [SDK do Azure .NET](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
* [Como controlar os custos e economizar dinheiro com o Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
