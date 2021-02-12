---
title: Serviços de Machine Learning no SQL Instância Gerenciada do Azure (versão prévia)
description: Este artigo fornece uma visão geral ou Serviços de Machine Learning no SQL Instância Gerenciada do Azure.
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
ms.date: 06/03/2020
ms.openlocfilehash: c805bacbd4a2219fb79168ad6426efd8b0a390df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324509"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Serviços de Machine Learning no SQL Instância Gerenciada do Azure (versão prévia)

Serviços de Machine Learning é um recurso do Azure SQL Instância Gerenciada (versão prévia) que fornece aprendizado de máquina no banco de dados, dando suporte a scripts Python e R. O recurso inclui pacotes python e R da Microsoft para análise preditiva de alto desempenho e aprendizado de máquina. Os dados relacionais podem ser usados em scripts por meio de procedimentos armazenados, script T-SQL que contém instruções Python ou R ou código Python ou R que contém o T-SQL.

> [!IMPORTANT]
> Serviços de Machine Learning é um recurso da Instância Gerenciada de SQL que está atualmente em visualização pública.
> Essa funcionalidade de visualização está inicialmente disponível em um número limitado de regiões nos EUA, na Europa asiático e na Austrália com regiões adicionais sendo adicionadas posteriormente.
>
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Inscreva-se na versão prévia](#signup) abaixo.

## <a name="what-is-machine-learning-services"></a>O que são os Serviços de Machine Learning?

Serviços de Machine Learning no Azure SQL Instância Gerenciada permite executar scripts do Python e do R no banco de dados. Você pode usá-lo para preparar e limpar dados, fazer engenharia de recursos e treinar, avaliar e implantar modelos de machine learning em um banco de dados. O recurso executa seus scripts onde os dados residem e elimina a transferência dos dados pela rede para outro servidor.

Use Serviços de Machine Learning com suporte de R/Python no Azure SQL Instância Gerenciada para:

- **Execute scripts de r e Python para realizar a preparação de dados e o processamento de dados de uso geral** -agora você pode trazer seus scripts de r/Python para o Azure SQL instância gerenciada em que seus dados residem, em vez de ter que mover dados para outro servidor para executar scripts de R e Python. Você pode eliminar a necessidade de movimentação de dados e problemas associados relacionados à latência, segurança e conformidade.

- **Treinar modelos de aprendizado de máquina no banco de dados** – você pode treinar modelos usando qualquer algoritmo de software livre. Você pode dimensionar facilmente seu treinamento para todo o conjunto de dados, em vez de depender de conjuntos de exemplos de bancos de dados extraídos do banco de dados.

- **Implante seus modelos e scripts em produção em procedimentos armazenados** -os scripts e modelos treinados podem ser operados simplesmente inserindo-os em procedimentos armazenados do T-SQL. Os aplicativos que se conectam ao Azure SQL Instância Gerenciada podem se beneficiar de previsões e inteligência nesses modelos chamando apenas um procedimento armazenado. Você também pode usar a função de previsão T-SQL nativa para colocar em operação os modelos para Pontuação rápida em cenários de Pontuação altamente simultâneas em tempo real.

As distribuições base do Python e do R estão incluídas nos Serviços de Machine Learning. Você pode instalar e usar estruturas e pacotes de software livre, como PyTorch, TensorFlow e Scikit-learn, além dos pacotes da Microsoft [revoscalepy](/sql/advanced-analytics/python/ref-py-revoscalepy) e [microsoftml](/sql/advanced-analytics/python/ref-py-microsoftml) para Python e [RevoScaleR](/sql/advanced-analytics/r/ref-r-revoscaler), [MicrosoftML](/sql/advanced-analytics/r/ref-r-microsoftml), [olapR](/sql/advanced-analytics/r/ref-r-olapr) e [sqlrutils](/sql/advanced-analytics/r/ref-r-sqlrutils) para R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Inscrever-se para obter a visualização

Essa visualização pública limitada está sujeita aos [termos de visualização do Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Se estiver interessado em ingressar no programa de visualização e aceitar esses termos, você poderá solicitar o registro criando um tíquete de suporte do Azure em [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. Na página **criar um tíquete de suporte** , clique em **criar um incidente**.

1. Na página **ajuda + suporte** , clique em **nova solicitação de suporte** para criar um novo tíquete.

1. Selecione as seguintes opções:
   - Tipo de problema- **técnico**
   - Assinatura – *Selecione sua assinatura*
   - Serviço- **SQL instância gerenciada**
   - Recurso – *Selecione sua instância gerenciada*
   - Resumo – *Insira uma breve descrição da sua solicitação*
   - Tipo de problema- **serviços de Machine Learning para SQL instância gerenciada (versão prévia)**
   - Subtipo de problema- **outro problema ou perguntas "como"**

1. Clique em **Avançar: soluções**.

1. Leia as informações sobre a visualização e clique em **Avançar: detalhes**.

1. Nessa página:
   - Para a pergunta **você está tentando se inscrever na versão prévia?**, selecione **Sim**. 
   - Para **Descrição**, insira as especificidades de sua solicitação, incluindo o nome do servidor lógico, a região e a ID da assinatura que você deseja registrar na visualização. Insira outros detalhes conforme apropriado.
   - Selecione seu método de contato preferencial. 

1. Quando tiver terminado, clique em **Avançar: revisar + criar** e, em seguida, clique em **criar**.

Após se inscrever no programa, a Microsoft integrará você à visualização pública e habilitará os Serviços do Machine Learning para seu banco de dados novo ou existente.

Não recomendamos os Serviços de Machine Learning na Instância Gerenciada de SQL para cargas de trabalho de produção durante a visualização pública.

## <a name="next-steps"></a>Próximas etapas

- Consulte as [principais diferenças de SQL Server serviços de Machine Learning](machine-learning-services-differences.md).
- Para saber como usar o Python no Serviços de Machine Learning, consulte [executar scripts Python](/sql/machine-learning/tutorials/quickstart-python-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Para saber como usar o R no Serviços de Machine Learning, consulte [executar scripts R](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Para obter mais informações sobre o aprendizado de máquina em outras plataformas do SQL, consulte a [documentação do SQL Machine Learning](/sql/machine-learning/).