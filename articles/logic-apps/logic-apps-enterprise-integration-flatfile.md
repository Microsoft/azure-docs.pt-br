---
title: "Codificar ou decodificar arquivos simples nos aplicativos lógicos do Azure | Microsoft Docs"
description: "Como usar o codificador ou decodificador de arquivos simples no Enterprise Integration Pack em seus aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; mandia
ms.openlocfilehash: bc3430624844cdeb92958433fba295f67a8ae0ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>Visão geral da integração corporativa com arquivos simples

Talvez você queira codificar o conteúdo XML antes de enviá-lo para um parceiro de negócios em um cenário B2B (entre empresas). Em um aplicativo lógico, você pode usar o conector de codificação de arquivo simples para fazer isso. O aplicativo lógico criado por você pode obter seu conteúdo XML de diversas fontes, incluindo de um gatilho de solicitação HTTP ou de outro aplicativo, ou até mesmo de um dos vários [conectores](../connectors/apis-list.md). Para saber mais sobre os aplicativos lógicos, veja o [documentação dos aplicativos lógicos](logic-apps-what-are-logic-apps.md "Saiba mais sobre os Aplicativos Lógicos").  

## <a name="create-the-flat-file-encoding-connector"></a>Como criar o conector de codificação de arquivo simples
Execute estas etapas para adicionar um conector de codificação de arquivo simples ao seu aplicativo lógico.

1. Crie um aplicativo lógico e [vincule-o à sua conta de integração](logic-apps-enterprise-integration-accounts.md "Saiba como vincular uma conta de integração a um Aplicativo lógico"). Esta conta contém o esquema que será usado para codificar os dados XML.  
2. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.  
   ![Captura de tela do gatilho a ser selecionado](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. Adicione uma ação de codificação de arquivo simples, desta maneira:
   
    a. Selecione o sinal de **mais** .
   
    b. Selecione o link **Adicionar uma ação** (que aparece após a seleção do sinal de adição).
   
    c. Na caixa de pesquisa, insira *Simples* para filtrar todas as ações para aquela que você deseja usar.
   
    d. Escolha a ação **Codificação de Arquivo Simples** na lista.   
   ![Captura de tela da opção Codificação de Arquivo Simples](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. Na caixa de texto **Codificação de Arquivo Simples**, selecione a caixa de texto **Conteúdo**.  
   ![Captura de tela da caixa de texto Conteúdo](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. Selecione a marca body como o conteúdo que será codificado. A marca body preencherá o campo de conteúdo.     
   ![Captura de tela da marca body](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. Escolha a caixa de listagem **Nome do Esquema** e escolha o esquema que você deseja usar para codificar o conteúdo da entrada.    
   ![Captura de tela da caixa de listagem Nome do Esquema](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. Salve seu trabalho.   
   ![Captura de tela do ícone Salvar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

Neste ponto, você já configurou seu conector de codificação de arquivo simples. Em um aplicativo real, convém armazenar os dados codificados em um aplicativo de linha de negócios, como o Salesforce. Ou você pode enviar os dados codificados para um parceiro comercial. Você pode adicionar facilmente uma ação para enviar a saída da ação de codificação para o Salesforce ou seu parceiro comercial usando qualquer um dos outros conectores fornecido.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML no corpo da solicitação.  

## <a name="create-the-flat-file-decoding-connector"></a>Como criar o conector de decodificação de arquivo simples

> [!NOTE]
> Para concluir estas etapas, você precisa ter um arquivo de esquema já carregado na conta de integração.

1. Adicione um gatilho **Solicitação - quando uma solicitação HTTP é recebida** ao seu Aplicativo Lógico.  
   ![Captura de tela do gatilho a ser selecionado](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. Adicione a ação de decodificação de arquivo simples, desta maneira:
   
    a. Selecione o sinal de **mais** .
   
    b. Selecione o link **Adicionar uma ação** (que aparece após a seleção do sinal de adição).
   
    c. Na caixa de pesquisa, insira *Simples* para filtrar todas as ações para aquela que você deseja usar.
   
    d. Escolha a ação **Decodificação de Arquivo Simples** na lista.   
   ![Captura de tela da opção Decodificação de Arquivo Simples](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. Selecione o controle **Conteúdo** . Isso produz uma lista do conteúdo das etapas anteriores que você pode usar como o conteúdo para decodificação. Observe que o *Corpo* da solicitação HTTP de entrada estará disponível para uso como o conteúdo para decodificação. Você também pode inserir o conteúdo para decodificação diretamente no controle **Conteúdo** .     
4. Selecione a marca *Body* . Observe que agora a marca body está no controle **Conteúdo** .
5. Selecione o nome do esquema que você deseja usar para decodificar o conteúdo. A captura de tela a seguir mostra que *OrderFile* é o nome do esquema selecionado. Esse nome de esquema foi carregado anteriormente na conta de integração.
   
   ![Captura de tela da caixa de diálogo Decodificação de Arquivo Simples](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. Salve seu trabalho.  
   ![Captura de tela do ícone Salvar](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

Neste ponto, você já configurou seu conector de decodificação de arquivo simples. Em um aplicativo real, convém armazenar os dados decodificados em um aplicativo de linha de negócios, como o Salesforce. Você pode adicionar facilmente uma ação para enviar a saída da ação de decodificação para o Salesforce.

Agora você pode testar seu conector fazendo uma solicitação ao ponto de extremidade HTTP e incluindo o conteúdo XML que você deseja decodificar no corpo da solicitação.  

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack").  

