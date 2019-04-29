---
title: Suplemento do Excel para serviços Web
titleSuffix: Azure Machine Learning Studio
description: Como usar os serviços Web do Azure Machine Learning diretamente no Excel sem escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 9e801e0d7a26cd4d6c43118959aee1dec7216b1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750156"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Suplemento do Excel para serviços Web do Azure Machine Learning Studio
O Excel torna fácil chamar serviços Web diretamente, sem a necessidade de escrever nenhum código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Etapas para usar um serviço Web existente na pasta de trabalho

1. Abra o [arquivo de exemplo do Excel](https://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros do Titanic. 
 
    > [!NOTE]
    > Você verá a lista de serviços Web relacionados ao arquivo e, na parte inferior, uma caixa de seleção para "Previsão automática". Se você habilitar a previsão automática, as previsões de **todos** os seus serviços serão atualizadas sempre que houver uma alteração nas entradas. Se estiver desmarcada, clique em "Prever tudo" para atualizar. Para habilitar a previsão automática em um nível de serviço, vá para a etapa 6.

2. Escolha o serviço Web clicando nele - "Titanic Survivor Predictor (exemplo de suplemento do Excel) [Pontuação]" neste exemplo.
   
    ![Selecionar o serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isso levará você à seção **Prever**.  Esta pasta de trabalho já contém dados de exemplo, mas para uma pasta de trabalho em branco você pode selecionar uma célula no Excel e clicar em **Usar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Verifique se a caixa "Meus dados contêm cabeçalhos" está marcada.
5. Em **Saída**, insira o número da célula em que você deseja colocar a saída, por exemplo, "H1" aqui.
6. Clique em **Prever**. Se você marcar a caixa de seleção "Previsão automática", qualquer alteração nas áreas selecionadas (aquelas especificadas como entrada) disparará uma solicitação e uma atualização das células de saída sem a necessidade de você pressionar o botão de previsão.
   
    ![Seção Prever](./media/excel-add-in-for-web-services/image1.png)

Implante um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, confira o [Tutorial 3: Implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).

Obtenha a chave de API para o seu serviço Web. O local em que você executa essa ação depende se você publicou um serviço Web Clássico do Machine Learning ou um Novo serviço Web do Machine Learning.

**Usar um serviço Web Clássico** 

1. No Machine Learning Studio, clique na seção **SERVIÇOS WEB** no painel esquerdo e selecione o serviço Web.
   
    ![Selecione um serviço Web do Studio](./media/excel-add-in-for-web-services/image4.png)
2. Copie a chave de API para o serviço Web.
   
    ![Chave de API do Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na guia **PAINEL** do serviço Web, clique no link **SOLICITAÇÃO/RESPOSTA**.
4. Procure a seção **URI da solicitação** .  Copie e salve a URL.

> [!NOTE]
> Agora é possível entrar no portal dos [Serviços Web do Azure Machine Learning](https://services.azureml.net) para obter a chave de API para um serviço Web Clássico do Machine Learning.
> 
> 

**Usar um Novo serviço Web**

1. No portal dos [Serviços Web do Azure Machine Learning](https://services.azureml.net), clique em **Serviços Web** e selecione o serviço Web. 
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo** . Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.

## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um Novo serviço Web

1. Implante um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, confira o [Tutorial 3: Implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo** . Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.
4. No Excel, vá para a seção **Serviços Web** (se você estiver na seção **Prever**, clique na seta para voltar para ir para a lista de serviços Web).
   
    ![Vá para a seleção de serviço Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique em **Adicionar Serviço Web**.
6. Cole a URL na caixa de texto do complemento do Excel chamada **URL**.
7. Cole a chave de API/primária na caixa de texto chamada **Chave de API**.
8. Clique em **Adicionar**.
   
    ![URL e chave de API para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para usar o serviço Web, siga as instruções anteriores de “Etapas para usar um serviço Web existente”.

## <a name="sharing-your-workbook"></a>Compartilhar sua pasta de trabalho
Se você salvar sua pasta de trabalho, as chaves de API/primária dos serviços Web adicionados também serão salvas. Isso significa que você só deve compartilhar a pasta de trabalho com pessoas confiáveis.

Faça suas perguntas na seção de comentário abaixo ou em nosso [fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
