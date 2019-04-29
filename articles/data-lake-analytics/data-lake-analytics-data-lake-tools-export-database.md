---
title: Exportar um bancos de dado U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio
description: Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio para exportar um banco de dados U-SQL e importá-lo automaticamente em uma conta local.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: fe28aa8b88f557d4bbcdabf1de1c4bc6491743ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628503"
---
# <a name="export-a-u-sql-database"></a>Exportar um banco de dados U-SQL

Neste artigo, aprenda a usar as [Ferramentas do Azure Data Lake para Visual Studio](https://aka.ms/adltoolsvs) para exportar um banco de dados U-SQL como um script U-SQL único e recursos baixados. Você pode importar o banco de dados exportado para uma conta local no mesmo processo.

Geralmente, os clientes mantêm vários ambientes para desenvolvimento, teste e produção. Esses ambientes são hospedados em uma conta local, no computador local do desenvolvedor e em uma conta do Azure Data Lake Analytics no Azure. 

Quando você desenvolve e ajusta consultas U-SQL em ambientes de desenvolvimento e teste, os desenvolvedores geralmente precisam recriar seu trabalho em um banco de dados de produção. O Assistente para Exportação de Banco de Dados ajuda a acelerar esse processo. Ao usar o assistente, os desenvolvedores podem clonar o ambiente existente de banco de dados e os dados de exemplo para outras contas do Data Lake Analytics.

## <a name="export-steps"></a>Etapas de exportação

### <a name="step-1-export-the-database-in-server-explorer"></a>Etapa 1: Exportar o banco de dados no Gerenciador de servidores

Todas as contas do Data Lake Analytics para as quais você tem permissão estão listadas no Gerenciador de Servidores. Para exportar o banco de dados:

1. No Gerenciador de Servidores, expanda a conta que contém o banco de dados que você deseja exportar.
2. Clique com o botão direito do mouse no banco de dados e selecione **Exportar**. 
   
    ![Gerenciador de Servidores - exportar um banco de dados](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Se a opção de menu **Exportar** não estiver disponível, você precisará [atualizar a ferramenta para a versão mais recente](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Etapa 2: Configurar os objetos que você deseja exportar

Se você precisar de uma pequena parte de um banco de dados grande, poderá configurar um subconjunto dos objetos que deseja exportar no assistente de exportação. 

A ação de exportação é concluída pela execução de um trabalho de U-SQL. Portanto, a exportação de uma conta do Azure incorre em alguns custos.

![Assistente para exportação de banco de dados - selecionar objetos para exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Etapa 3: Verifique a lista de objetos e outras configurações

Nesta etapa, você pode verificar os objetos selecionados na caixa **Lista de objetos para exportação**. Se houver erros, selecione **Anterior** para voltar e configurar corretamente os objetos que você deseja exportar.

Você também pode definir outras configurações para o destino de exportação. As descrições de configuração estão listadas na tabela abaixo:

|Configuração|DESCRIÇÃO|
|-------------|-----------|
|Nome do destino|Esse nome indica onde você deseja salvar os recursos do banco de dados exportado. Os exemplos são assemblies, arquivos adicionais e dados de exemplo. Uma pasta com esse nome é criada na sua pasta raiz local de dados.|
|Diretório do projeto|Esse caminho define onde você deseja salvar o script U-SQL exportado. Todas as definições de objeto de banco de dados são salvas neste local.|
|Somente esquema|Se você selecionar essa opção, apenas definições e recursos de banco de dados (como assemblies e arquivos adicionais) são exportados.|
|Esquema e dados|Se você selecionar essa opção, as definições de banco de dados, recursos e os dados serão exportadas. As linhas N superiores das tabelas são exportadas.|
|Importar automaticamente para o banco de dados local|Se você selecionar essa opção, o banco de dados exportado será importado automaticamente para o banco de dados local quando a exportação é concluída.|

![Assistente de exportação de banco de dados - lista de objetos para exportação e outras configurações](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Etapa 4: Verificar os resultados da exportação

Quando a exportação for concluída, você poderá exibir os resultados exportados na janela de log do assistente. O exemplo a seguir mostra como localizar script U-SQL e banco de dados de recursos exportados, incluindo assemblies, arquivos adicionais e dados de exemplo:

![Assistente para exportação de banco de dados - resultados da exportação](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importar o banco de dados exportado para a conta local

A maneira mais conveniente de importar o banco de dados exportado é marcar a caixa de seleção **Importar para o Banco de Dados Local Automaticamente** durante o processo de exportação na Etapa 3. Se você não marcou esta caixa, primeiro encontre o script U-SQL exportado no log de exportação. Em seguida, execute o script U-SQL localmente para importar o banco de dados para sua conta local.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importar o banco de dados exportado para a conta do Data Lake Analytics

Para importar o banco de dados para outra conta do Data Lake Analytics:

1. Carregue os recursos exportados, incluindo assemblies, arquivos adicionais e dados de exemplo na conta padrão Azure Data Lake Store da conta do Data Lake Analytics para a qual você deseja importar. Você pode encontrar a pasta do recurso exportado na pasta raiz de dados local. Carregue toda a pasta na raiz da conta do Data Lake Store padrão.
2. Quando terminar de carregar, envie o script U-SQL exportado para a conta do Data Lake Analytics na qual você deseja importar o banco de dados.

## <a name="known-limitations"></a>Limitações conhecidas

Atualmente, se você selecionar a opção **Esquema e dados** na Etapa 3, a ferramenta executa um trabalho de U-SQL para exportar os dados armazenados em tabelas. Por isso, o processo de exportação de dados pode ser lento e você pode incorrer em custos. 

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre bancos de dados U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testar e depurar trabalhos U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)


