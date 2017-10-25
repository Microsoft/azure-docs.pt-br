---
title: "Saiba como usar o conector de Twitter em aplicativos lógicos | Microsoft Docs"
description: "Visão geral do conector do Twitter com os parâmetros da API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: be8163043535833ce45b3d50939a537406cf8152
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-twitter-connector"></a>Introdução ao conector do Twitter
Com o conector do Twitter, você pode:

* Postar tweets e obter tweets
* Acessar linhas do tempo, amigos e seguidores
* Executar qualquer um dos gatilhos e ações descritos abaixo  

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../logic-apps/logic-apps-create-a-logic-app.md).  

## <a name="connect-to-twitter"></a>Conectar-se ao Twitter
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](connectors-overview.md) fornece uma conectividade entre um aplicativo lógico e outro serviço.  

### <a name="create-a-connection-to-twitter"></a>Criar uma conexão com o Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Usar um gatilho do Twitter
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, mostrarei como usar o gatilho **Quando um novo tweet é postado** para procurar #Seattle e, se #Seattle for encontrada, mostrarei como atualizar um arquivo no Dropbox com o texto do tweet. Em um exemplo corporativo, você pode pesquisar o nome da sua empresa e atualizar um banco de dados SQL com o texto do tweet.

1. Digite *twitter* na caixa de pesquisa no designer de aplicativos lógicos e escolha o gatilho **Twitter – quando um novo tweet é postado**   
   ![Imagem 1 do gatilho do Twitter](./media/connectors-create-api-twitter/trigger-1.png)  
2. Digite *#Seattle* no controle **Texto de Pesquisa**  
   ![Imagem 2 do gatilho do Twitter](./media/connectors-create-api-twitter/trigger-2.png) 

Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e ações no fluxo de trabalho. 

> [!NOTE]
> Para que um aplicativo lógico funcione, ele deve conter pelo menos um gatilho e uma ação. Siga as etapas na próxima seção para adicionar uma ação.  
> 
> 

## <a name="add-a-condition"></a>Adicione uma condição
Uma vez que estamos interessados em tweets de usuários com mais de 50 usuários, uma condição que confirma o número de seguidores deve ser adicionada ao aplicativo lógico.  

1. Escolha **+ Nova etapa** para adicionar a ação que deseja tomar quando #Seattle for encontrada em um novo tweet  
   ![Imagem 1 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Escolha o link **Adicionar uma ação**.  
   ![Imagem 1 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Isso abre o controle **Condição**, onde é possível verificar condições como *é igual a*, *é menor que*, *é maior que*, *contém*, etc.  
   ![Imagem 2 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Selecione o controle **Escolher um valor**.  
   Nesse controle, é possível selecionar, como valor, uma ou mais propriedades de quaisquer ações ou gatilhos anteriores, cuja condição será avaliada como verdadeira ou falsa.
   ![Imagem 3 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Escolha **...** para expandir a lista de propriedades, de modo que você possa ver todas as propriedades que estão disponíveis.        
   ![Imagem 4 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Escolha a propriedade **Contagem de seguidores**.    
   ![Imagem 5 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Observe que a propriedade Contagem de seguidores agora está no controle de valor.    
   ![Imagem 6 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Escolha **é maior que** na lista de operadores.    
   ![Imagem 7 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Digite 50 como o operando para o operador *é maior que*.  
   Agora a condição está adicionada. Salve seu trabalho usando o link **Salvar** no menu acima.    
   ![Imagem 8 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Usar uma ação do Twitter
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Agora que você adicionou um gatilho, siga estas etapas para adicionar uma ação que postará um novo tweet com o conteúdo dos tweets encontrados pelo gatilho. Para este passo a passo, serão postados apenas tweets de usuários com mais de 50 seguidores.  

Na próxima etapa, você adicionará uma ação do Twitter que postará um tweet usando algumas das propriedades de cada tweet que foi postado por um usuário com mais de 50 seguidores.  

1. Escolha **Adicionar uma ação**. Isso abre o controle de pesquisa, onde é possível procurar outros gatilhos e ações.  
   ![Imagem 9 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Insira *twitter* na caixa de pesquisa e escolha a ação **Twitter – postar um tweet**. Isso abre o controle **Postar um tweet**, onde você vai inserir todos os detalhes para o tweet que está sendo postado.      
   ![Imagem de 1 a 5 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Escolha o controle **Texto do tweet**. Todas as saídas de ações e gatilhos anteriores no aplicativo lógico agora estão visíveis. Você pode escolher qualquer um deles e usá-los como parte do texto do tweet do novo tweet.     
   ![Imagem 2 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Escolha **Nome de usuário**   
5. Digite *diz:* no controle de texto do tweet. Faça isso logo após o Nome de usuário.  
6. Escolha *Texto do tweet*.       
   ![Imagem 3 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Salve seu trabalho e envie um tweet com a hashtag #Seattle para ativar o fluxo de trabalho.  


## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)

