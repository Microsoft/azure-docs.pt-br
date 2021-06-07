---
title: 'ML Studio (clássico): consumir serviço Web no Excel – Azure'
description: Azure Machine Learning Studio (clássico) facilita a chamada de serviços da Web diretamente do Excel sem a necessidade de escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517851"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Consumindo um serviço Web Azure Machine Learning Studio (clássico) do Excel

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (clássico) facilita a chamada de serviços da Web diretamente do Excel sem a necessidade de escrever nenhum código.

Se estiver usando o Excel 2013 (ou posterior) ou o Excel Online, é recomendável usar o [suplemento do Excel](excel-add-in-for-web-services.md).



## <a name="steps"></a>Etapas
Publicar um serviço Web. [Tutorial 3: implantar o modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md) explica como fazer isso. Atualmente, o recurso de pasta de trabalho do Excel só tem suporte para serviços de Solicitação/Resposta que têm uma única saída (ou seja, um único rótulo de pontuação). 

Após ter um serviços Web, clique na seção **SERVIÇOS WEB** à esquerda do estúdio e selecione o serviço Web a ser consumido no Excel.

**Serviço Web Clássico**

1. Na guia **PAINEL** para o serviço Web, há uma linha para o serviço de **SOLICITAÇÃO/RESPOSTA**. Se esse serviço tiver uma única saída, você deve ver o link **Baixar Pasta de Trabalho do Excel** nessa linha.

    ![Baixar a pasta de trabalho do Excel usando o portal do serviço Web Studio (clássico)](./media/consuming-from-excel/excellink.png)
2. Clique em **Baixar Pasta de Trabalho do Excel**.

**Novo serviço Web**

1. No portal do Serviço Web de Azure Machine Learning, selecione **Consumir**.
2. Na página Consumir, na seção **Opções de consumo do serviço Web** , clique no ícone do Excel.

**Usando a pasta de trabalho**

1. Abra a pasta de trabalho.
2. Será exibido um Aviso de Segurança; clique no botão **Habilitar Edição** .

    ![Habilitar edição para remover o aviso de segurança de exibição protegida](./media/consuming-from-excel/enableeditting.png)
3. Será exibido um Aviso de Segurança. Clique no botão **Habilitar Conteúdo** para executar macros na sua planilha.

    ![Habilitar conteúdo para ignorar o aviso de segurança desabilitando macros](./media/consuming-from-excel/enablecontent.png)
4. Depois que as macros estiverem habilitadas, uma tabela será gerada. As colunas em azul são requeridas como entrada para o serviço Web RRS ou **PARÂMETROS**. Observe a saída do serviço RRS, **VALORES PREVISTOS** , em verde. Quando todas as colunas para uma determinada linha estiverem preenchidas, a pasta de trabalho automaticamente chama a API de pontuação e exibe os resultados de pontuação.

    ![Tabela para entradas de parâmetro e os valores previstos resultantes](./media/consuming-from-excel/sampletable.png)
5. Para pontuar mais de uma linha, preencha a segunda linha com dados e os valores previstos serão produzidos. Você pode até mesmo colar várias linhas ao mesmo tempo.

Você pode usar qualquer um dos recursos do Excel (grafos, Power Map, formatação condicional, etc.) com os valores previstos para ajudar na visualização dos dados.

## <a name="sharing-your-workbook"></a>Compartilhar sua pasta de trabalho
Para as macros funcionarem, sua Chave de API precisa fazer parte da planilha. Isso significa que você deve compartilhar a pasta de trabalho apenas com entidades/pessoas confiáveis.

## <a name="automatic-updates"></a>Atualizações automáticas
É feita uma chamada RRS nessas duas situações:

1. Na primeira vez em que uma linha tem conteúdo em todos os seus **PARÂMETROS**
2. Sempre que qualquer um dos **PARÂMETROS** for alterado em uma linha que tinha todos os seus **PARÂMETROS** preenchidos.