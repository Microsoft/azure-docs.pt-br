---
title: Desenvolver assemblies do U-SQL para trabalhos do Azure Data Lake Analytics | Microsoft Docs
description: 'Saiba como desenvolver assemblies para serem usados e reutilizados em trabalhos do Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Desenvolver assemblies do U-SQL para trabalhos do Azure Data Lake Analytics
Saiba como transformar code-behind em assemblies a serem usados e reutilizados em trabalhos do Data Lake Analytics. 

O U-SQL torna fácil a tarefa de adicionar seu próprio código personalizado em linguagens .Net, como C#, VB.Net ou F#. Você ainda pode implantar seu próprio tempo de execução para dar suporte a outras linguagens.

A maneira mais fácil de usar código personalizado é por meio dos recursos code-behind das Ferramentas do Data Lake para Visual Studio. Para obter mais informações, consulte o [Tutorial: desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Há algumas desvantagens em usar code-behind:

- O código-fonte é carregado em cada envio de script.
- O code-behind não pode ser compartilhado com outros trabalhos.

Para lidar com essas desvantagens, você pode transformar o code-behind em assemblies e registrá-los no catálogo do Data Lake Analytics.

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2017, Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado
* SDK do Microsoft Azure para .NET versão 2.5 ou posterior.  Instale-o usando o Web Platform Installer ou Instalador do Visual Studio
* Uma conta da Análise Data Lake.  Confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).
* Acompanhe o tutorial [Introdução ao U-SQL Studio da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md) .
* Conecte-se ao Azure.
* Carregar os dados de origem, consulte [Introdução ao U-SQL Studio do Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md). 

## <a name="develop-assemblies-for-u-sql"></a>Desenvolver assemblies para U-SQL

**Para criar e enviar um trabalho do U-SQL**

1. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
2. Expanda **Instalados**, **Modelos**, **Azure Data Lake**, **U-SQL(ADLA)**, selecione o modelo **Biblioteca de Classes (para aplicativos U-SQL)** e clique em **OK**.
3. Escreva seu código em Class1.cs.  Veja a seguir um exemplo de código.

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. Clique no menu **Criar** e em **Compilar Solução** para criar a dll.

## <a name="register-assemblies"></a>Registrar assemblies

Veja [Usar o catálogo do Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md).


## <a name="use-the-assemblies"></a>Usar os assemblies

Veja [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

## <a name="see-also"></a>Consulte também
* [Introdução à Análise Data Lake usando o PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Usar as Ferramentas do Data Lake para Visual Studio para desenvolver aplicativos do U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar o catálogo do Data Lake Analytics (U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)