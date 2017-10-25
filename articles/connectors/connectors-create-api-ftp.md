---
title: "Saiba como usar o conector de FTP em aplicativos lógicos | Microsoft Docs"
description: "Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se ao servidor FTP para gerenciar seus arquivos. Você pode executar várias ações, como carregar, atualizar, obter e excluir arquivos no servidor FTP."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 61bfbedfd4f1e84b6976099323a32f3a720634c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-ftp-connector"></a>Introdução ao conector de FTP
Use o conector de FTP para monitorar, gerenciar e criar arquivos em um servidor FTP. 

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Conectar-se ao FTP
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](connectors-overview.md) fornece uma conectividade entre um aplicativo lógico e outro serviço.  

### <a name="create-a-connection-to-ftp"></a>Criar uma conexão com o FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Usar o gatilho de FTP
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!IMPORTANT]
> O conector de FTP exige um servidor FTP que possa ser acessado pela Internet e esteja configurado para operar com o modo PASSIVO. Além disso, o conector de FTP **não é compatível com FTPS implícito (FTP por SSL)**. O conector de FTP permite apenas FTPS explícito (FTP por SSL).  
> 
> 

Neste exemplo, mostrarei como usar o gatilho **FTP – Quando um arquivo é adicionado ou modificado** para iniciar um fluxo de trabalho do aplicativo lógico quando um arquivo é adicionado, ou modificado, em um servidor FTP. Em um exemplo corporativo, você pode usar esse gatilho para monitorar uma pasta FTP em busca de novos arquivos que representam pedidos de clientes.  Você pode usar uma ação de conector FTP, como **Obter conteúdo do arquivo** para obter o conteúdo do pedido para processamento posterior e armazenamento em seu banco de dados de pedidos.

1. Digite *ftp* na caixa de pesquisa no designer de aplicativos lógicos e selecione o gatilho **FTP - Quando um arquivo é criado ou modificado**   
   ![Imagem 1 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   O controle **Quando um arquivo é adicionado ou modificado** é aberto  
   ![Imagem 2 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Escolha **...** , localizado no lado direito do controle. Isso abre o controle de seletor de pasta   
   ![Imagem 3 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Escolha **>** (seta para a direita) e procure a pasta que deseja monitorar em busca de arquivos novos ou modificados. Selecione a pasta e observe que a pasta agora é exibida no controle **Pasta**.  
   ![Imagem 4 do gatilho de FTP](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e as ações no fluxo de trabalho quando um arquivo é, ou modificado, ou criado, na pasta FTP específica. 

> [!NOTE]
> Para que um aplicativo lógico funcione, ele deve conter pelo menos um gatilho e uma ação. Siga as etapas na próxima seção para adicionar uma ação.  
> 
> 

## <a name="use-a-ftp-action"></a>Usar uma ação de FTP
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Agora que você adicionou um gatilho, siga estas etapas para adicionar uma ação que obterá o conteúdo do arquivo novo ou modificado encontrado pelo gatilho.    

1. Escolha **+ Nova etapa** a fim de adicionar a ação para obter o conteúdo do arquivo no servidor FTP  
2. Selecione o link **Adicionar uma ação** .  
   ![Imagem 1 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Insira *FTP* para pesquisar todas as ações relacionadas ao FTP.
4. Escolha **FTP - Obter conteúdo do arquivo** como a ação a ser tomada quando um arquivo novo ou modificado é encontrado na pasta FTP.      
   ![Imagem 2 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-2.png)  
   O controle **Obter conteúdo do arquivo** é aberto. **Observação**: será solicitado que você autorize o aplicativo lógico a acessar sua conta no servidor FTP, caso não tenha feito isso anteriormente.  
   ![Imagem 3 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Escolha o controle **Arquivo** (o espaço em branco localizado abaixo de **ARQUIVO***). Aqui, você pode usar qualquer uma das várias propriedades do arquivo novo ou modificado encontrado no servidor FTP.  
6. Escolha a opção **Conteúdo do arquivo**.  
   ![Imagem 4 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. O controle é atualizado, indicando que a ação **FTP – Obter conteúdo do arquivo** obterá o *conteúdo do arquivo* novo ou modificado no servidor FTP.      
   ![Imagem 5 da ação de FTP](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Salve seu trabalho e adicione um arquivo à pasta FTP para testar o fluxo de trabalho.    

Neste ponto, o aplicativo lógico foi configurado com um gatilho para monitorar uma pasta em um servidor FTP e iniciar o fluxo de trabalho quando encontrar um arquivo novo ou modificado no servidor FTP. 

O aplicativo lógico também foi configurado com uma ação para obter o conteúdo do arquivo novo ou modificado.

Agora você pode adicionar outra ação, como a ação [SQL Server – inserir linha](connectors-create-api-sqlazure.md), para inserir o conteúdo do arquivo novo ou modificado em uma tabela de banco de dados SQL.  

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)

