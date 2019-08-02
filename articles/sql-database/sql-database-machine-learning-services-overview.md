---
title: Serviços do Azure SQL banco de dados de Machine Learning com visão geral do R (versão prévia)
description: Este artigo descreve os serviços de aprendizado de máquina do Azure SQL banco de dados (com R) e explica como ele funciona.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427938"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Serviços do Azure SQL banco de dados de Machine Learning com R (versão prévia)

Os Serviços do Machine Learning são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. O recurso inclui pacotes R da Microsoft para análise preditiva de alto desempenho e machine learning. Os dados relacionais podem ser usados em scripts R por meio de procedimentos armazenados, do script T-SQL que contém instruções do R ou do código R que contém o T-SQL.

> [!IMPORTANT]
> Serviços do Azure SQL banco de dados de Machine Learning (com R) está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A visualização pública está disponível para bancos de dados únicos e pools Elásticos usando o modelo de compra baseado em vCore na **finalidade geral** e **comercialmente crítico** camadas de serviço. Nesta versão prévia pública inicial, não há suporte para a camada de serviço **hiperescala** e para a opção de implantação **instância gerenciada**. Atualmente, R é a única linguagem com suporte. No momento, não há suporte para Python.
>
> A visualização está atualmente disponível nas seguintes regiões: Europa Ocidental, Europa Setentrional, oeste dos EUA 2, Leste dos EUA, Centro-Sul dos EUA, Centro-Norte dos EUA, Canadá Central, Sudeste Asiático, Sul da Índia e Sudeste da Austrália.
>
> [Inscreva-se na versão prévia](#signup) abaixo.

## <a name="what-you-can-do-with-r"></a>O que você pode fazer com o R

Use o poder da linguagem R para fornecer análise avançada e machine learning no banco de dados. Essa capacidade leva os cálculos e o processamento para o local em que os dados residem, eliminando a necessidade de efetuar pull de dados na rede. Além disso, você pode aproveitar o poder dos pacotes de R enterprise para fornecer análise avançada em escala.

Os Serviços do Machine Learning incluem uma distribuição base do R, sobreposta com pacotes empresariais do R da Microsoft. As funções e os algoritmos do R da Microsoft foram desenvolvidos para escala e utilidade, fornecendo análise preditiva, modelagem estatística, visualizações de dados e algoritmos de machine learning de ponta.

### <a name="r-packages"></a>Pacotes R

Pacotes de R de código-fonte aberto mais comuns são pré-instalados no serviços de Machine Learning. Os seguintes pacotes R da Microsoft também estão incluídos:

| Pacote R | DESCRIÇÃO|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | O Microsoft R Open é a distribuição aprimorada de R da Microsoft. É uma plataforma completa de código-fonte aberto para a ciência de dados e análise estatística. É baseada no R e totalmente compatível com ele, além de incluir outras funcionalidades para um melhor desempenho e capacidade de reprodução. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | O RevoScaleR é a biblioteca principal para o R escalonável. As funções nessa biblioteca estão entre mais amplamente usadas. As transformações e a manipulação de dados, resumo estatístico, visualização e muitas formas de modelagem e de análises são encontrados nessas bibliotecas. Além disso, as funções nessas bibliotecas distribuem automaticamente as cargas de trabalho entre os núcleos disponíveis para processamento paralelo, com a capacidade de trabalhar em partes de dados coordenadas e gerenciadas pelo mecanismo de cálculo. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | O MicrosoftML adiciona algoritmos de machine learning para criar modelos personalizados para análise de texto, análise de imagem e de sentimento. |

Além dos pacotes pré-instalados, você pode [instalar pacotes adicionais](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se para obter a visualização

Para se inscrever na versão prévia pública, siga estas etapas:

1. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

2. Envie um email para a Microsoft pelo endereço [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) a fim de se inscrever na versão prévia pública. A versão prévia pública dos Serviços de Machine Learning (com R) no Banco de Dados SQL não está habilitada por padrão.

Depois que você está inscrito no programa, a Microsoft irá integrar você para a visualização pública e habilitar R nova ou de seu banco de dados.

Serviços de Machine Learning com R não é recomendado para cargas de trabalho de produção durante a visualização pública.

## <a name="next-steps"></a>Próximas etapas

- Consulte a [as principais diferenças de serviços do SQL Server Machine Learning](sql-database-machine-learning-services-differences.md).
- Para saber como usar R para consultar o banco de dados para SQL Azure Machine Learning Services (versão prévia), consulte o [guia de início rápido](sql-database-connect-query-r.md).
- Para se familiarizar com alguns scripts simples do R, consulte [criar e executar scripts simples do R no banco de dados para SQL Azure Machine Learning Services (versão prévia)](sql-database-quickstart-r-create-script.md).
