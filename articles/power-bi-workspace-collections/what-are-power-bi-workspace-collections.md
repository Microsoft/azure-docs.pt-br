---
title: "O que são Coleções de Espaços de Trabalho do Power BI?"
description: "O Power BI Embedded possibilita que você integre relatórios do Power BI a seus aplicativos móveis ou Web para que não precise compilar soluções personalizadas."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 7df172895bb926f1715370b941964e2c29ab393d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-power-bi-workspace-collections"></a>O que são Coleções de Espaços de Trabalho do Power BI?

Com as **Coleções de Espaços de Trabalho do Power BI**, você pode integrar relatórios do Power BI diretamente a seus aplicativos móveis ou Web.

![Diagrama de aplicativo](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> As Coleções do Espaço de Trabalho do Power BI foram preteridas e estarão disponíveis até junho de 2018 ou conforme a indicação do seu contrato. Recomendamos planejar a migração para o Power BI Embedded a fim de evitar interrupções em seu aplicativo. Para saber mais sobre como migrar seus dados para o Power BI Embedded, confira [Como migrar o conteúdo das Coleções do Espaço de Trabalho do Power BI para o Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

As Coleções de Espaços de Trabalho do Power BI são um **serviço do Azure** que permite que ISVs e desenvolvedores de aplicativo revelem experiências de dados do Power BI em seus aplicativos. Como desenvolvedor, você criou aplicativos, e esses aplicativos têm seus próprios usuários e um conjunto distinto de recursos. Esses aplicativos também podem ter alguns elementos de dados internos, como gráficos e relatórios que agora podem ser alimentados pelas Coleções de Espaços de Trabalho do Microsoft Power BI. Você não precisa de uma conta do Power BI para usar seu aplicativo. Você pode continuar entrando no aplicativo como antes e exibir e interagir com a experiência de emissão de relatórios do Power BI sem precisar qualquer licença adicional.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licenciamento para Coleções de Espaços de Trabalho do Microsoft Power BI

No modelo de uso de **Coleções de Espaços de Trabalho do Microsoft Power BI**, o licenciamento para o Power BI não é de responsabilidade do usuário final.  Em vez disso, **sessões** são compradas pelo desenvolvedor do aplicativo que está consumindo os elementos visuais e são cobradas da assinatura proprietária de tais recursos. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Modelo conceitual de Coleções de Espaços de Trabalho do Microsoft Power BI

![Fluxo do aplicativo com coleções de espaços de trabalho](media/what-are-power-bi-workspace-collections/model.png)

Como qualquer outro serviço no Azure, os recursos para o Coleções de Espaços de Trabalho do Power BI são provisionados por meio de [APIs do Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). Nesse caso, o recurso provisionado é uma **Coleção de Espaços de Trabalho do Power BI**.

## <a name="workspace-collection"></a>Coleção de espaços de trabalho

Uma **Coleção de Espaços de Trabalho** é o contêiner do Azure de nível mais elevado para recursos que contém 0 ou mais **Espaços de Trabalho**.  Uma **Coleção** de **Espaços de Trabalho** tem todas as propriedades padrão do Azure, bem como o seguinte:

* **Chaves de Acesso** – chaves usadas ao chamar com segurança as APIs do Power BI (descritas em uma seção posterior).
* **Usuários** – Os usuários do AAD (Azure Active Directory) que têm direitos de administrador para gerenciar a Coleção de Espaços de Trabalho do Power BI por meio do portal do Azure ou da API do Azure Resource Manager.
* **Região** – como parte do provisionamento de uma **Coleção de Espaços de Trabalho**, você pode selecionar uma região na qual ela será provisionada. Para obter mais informações, consulte [Regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Espaço de trabalho

Um **Espaço de Trabalho** é um contêiner de conteúdo do Power BI, que pode incluir conjuntos de dados e relatórios. Um **Espaço de Trabalho** fica vazio logo após ser criado pela primeira vez. Durante a Visualização, você criará todo o conteúdo usando o Power BI Desktop e implantará o PBIX por meio de programação em um dos seus espaços de trabalho usando a [API de Importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx). Você pode criar também programaticamente o conjunto de dados e então criar relatórios de dentro de seu aplicativo em vez de usar o Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Usando espaços de trabalhos e coleções de espaços de trabalho

**Coleções de Espaços de Trabalho** e **Espaços de Trabalho** são contêineres de conteúdo que são usados e organizados da forma que melhor se encaixar no design do aplicativo que você está criando. Haverá muitas maneiras diferentes em que você poderia organizar o conteúdo deles. Você pode optar por colocar todo o conteúdo dentro de um espaço de trabalho e, depois, usar tokens de aplicativo para subdividir ainda mais o conteúdo entre seus clientes. Você também pode optar por colocar todos os seus clientes em espaços de trabalho separados, para que haja alguma separação entre eles. Ou, você pode optar por organizar os usuários por região em vez de organizá-los por cliente. Esse design flexível permite que você escolha a melhor maneira de organizar o conteúdo.

## <a name="cached-datasets"></a>Conjuntos de dados em cache

Conjuntos de dados em cache podem ser usados.  No entanto, você não pode atualizar os dados armazenados em cache após eles serem carregados nas **Coleções de Espaços de Trabalho do Microsoft Power BI**. Um conjunto de dados em cache significa que você importou os dados para o Power BI Desktop em vez de usar DirectQuery.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticação e autorização com tokens de aplicativo

As **Coleções de Espaços de Trabalho do Microsoft Power BI** transferem para o seu aplicativo para executar toda a autorização e autenticação de usuário necessárias. Não há nenhum requisito explícito de que os usuários finais sejam os clientes do Azure Active Directory (Azure AD).  Em vez disso, o aplicativo dá às **Coleções de Espaços de Trabalho do Microsoft Power BI** autorização expressa para renderizar um relatório do Microsoft Power BI pelo uso de **Tokens de Autenticação de Aplicativo (Tokens de Aplicativo)**.  Esses **Tokens de Aplicativo** são criados conforme necessário, quando seu aplicativo deseja renderizar um relatório.

![Diagrama de uso do token do aplicativo](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Tokens de Autenticação de Aplicativo (Tokens de Aplicativo)** são usados para autenticação nas **Coleções de Espaços de Trabalho do Microsoft Power BI**.  Há três tipos de **Tokens de Aplicativo**:

1. Tokens de Provisionamento – usados ao provisionar um novo **Espaço de Trabalho** em uma **Coleção de Espaços de Trabalho**
2. Tokens de Desenvolvimento – Usados ao fazer chamadas diretamente para as **APIs REST do Power BI**
3. Tokens de Inserção - usados ao fazer chamadas para renderizar um relatório no iframe inserido

Esses tokens são usados para as várias fases de suas interações com **Coleções de Espaços de Trabalho do Microsoft Power BI**.  Os tokens são projetados para que você possa delegar permissões do seu aplicativo para o Power BI. Para obter mais informações, consulte o [Fluxo de Tokens de Aplicativo](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Criar ou editar relatórios dentro de seu aplicativo

Você agora pode editar relatórios existentes ou criar novos relatórios diretamente no seu aplicativo sem precisar usar o Power BI Desktop. Isso requer a existência de um conjunto de dados dentro de seu espaço de trabalho.

## <a name="see-also"></a>Consulte também

[Cenários comuns das Coleções de Espaços de Trabalho do Microsoft Power BI](scenarios.md)  
[Introdução às Coleções de Espaços de Trabalho do Microsoft Power BI](get-started.md)  
[Introdução a exemplos](get-started-sample.md)  
[Inserir um relatório](embed-report.md)  
[Autenticando e autorizando em Coleções de Espaços de Trabalho do Power BI](app-token-flow.md)  
[Amostra de inserção de JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repositório de Git PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)  
[Repositório de Git PowerBI-Node](https://github.com/Microsoft/PowerBI-Node)  

Mais perguntas? [Experimentar a comunidade do Power BI](http://community.powerbi.com/)
