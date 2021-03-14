---
title: 'ML Studio (clássico): suplemento do Excel para serviços Web-Azure'
description: Como usar os serviços Web do Azure Machine Learning diretamente no Excel sem escrever nenhum código.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 45d9e494b9f885cfa0680bec595aefcd4074d41e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519993"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Suplemento do Excel para serviços Web Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


O Excel torna fácil chamar serviços Web diretamente, sem a necessidade de escrever nenhum código.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Etapas para usar um serviço Web existente na pasta de trabalho

1. Abra o [arquivo de exemplo do Excel](https://aka.ms/amlexcel-sample-2), que contém o suplemento do Excel e os dados sobre passageiros do Titanic. 
 
    > [!NOTE]
    > - Você verá a lista de serviços Web relacionados ao arquivo e, na parte inferior, uma caixa de seleção para "Previsão automática". Se você habilitar a previsão automática, as previsões de **todos** os seus serviços serão atualizadas sempre que houver uma alteração nas entradas. Se estiver desmarcada, clique em "Prever tudo" para atualizar. Para habilitar a previsão automática em um nível de serviço, vá para a etapa 6.
    > - O Azure Machine Learning suplemento do Excel chamará o armazenamento de suplementos do Office para carregar. Se sua organização proibir o acesso ao repositório de suplementos do Office, você verá um erro ao carregar o suplemento. Para esse caso, implante o Azure Machine Learning suplemento do Excel no centro de administração do Microsoft 365. Em seguida, invoque o suplemento e adicione o serviço Web manualmente colando a URL e a chave de API.

 

2. Escolha o serviço Web clicando nele - "Titanic Survivor Predictor (exemplo de suplemento do Excel) [Pontuação]" neste exemplo.
   
    ![Selecionar o serviço Web](./media/excel-add-in-for-web-services/image1.png)
3. Isso levará você à seção **Prever**.  Esta pasta de trabalho já contém dados de exemplo, mas para uma pasta de trabalho em branco você pode selecionar uma célula no Excel e clicar em **Usar dados de exemplo**.
4. Selecione os dados com cabeçalhos e clique no ícone de intervalo de dados de entrada.  Verifique se a caixa "Meus dados contêm cabeçalhos" está marcada.
5. Em **Saída**, insira o número da célula em que você deseja colocar a saída, por exemplo, "H1" aqui.
6. Clique em **Prever**. Se você marcar a caixa de seleção "Previsão automática", qualquer alteração nas áreas selecionadas (aquelas especificadas como entrada) disparará uma solicitação e uma atualização das células de saída sem a necessidade de você pressionar o botão de previsão.
   
    ![Seção Prever](./media/excel-add-in-for-web-services/image1.png)

Implante um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).

Obtenha a chave de API para o seu serviço Web. O local em que você executa essa ação depende se você publicou um serviço Web Clássico do Machine Learning ou um Novo serviço Web do Machine Learning.

**Usar um serviço Web clássico** 

1. Em Machine Learning Studio (clássico), clique na seção **Serviços Web** no painel esquerdo e selecione o serviço Web.
   
    ![Selecione um serviço Web do Studio](./media/excel-add-in-for-web-services/image4.png)
2. Copie a chave de API para o serviço Web.
   
    ![Chave de API do Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na guia **painel** do serviço Web, clique no link **solicitação/resposta** .
4. Procure a seção **URI da solicitação** .  Copie e salve a URL.

> [!NOTE]
> Agora é possível entrar no portal de [serviços web Azure Machine Learning](https://services.azureml.net) para obter a chave de API para um serviço Web clássico Machine Learning.
> 
> 

**Usar um novo serviço Web**

1. No portal de [Serviços Web do Azure Machine Learning](https://services.azureml.net) , clique em **Serviços Web** e selecione seu serviço Web. 
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo** . Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.

## <a name="steps-to-add-a-new-web-service"></a>Etapas para adicionar um Novo serviço Web

1. Implante um serviço Web ou use um serviço Web existente. Para obter mais informações sobre como implantar um serviço Web, consulte [tutorial 3: implantar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
2. Clique em **Consumo**.
3. Procure a seção **Informações básicas de consumo** . Copie e salve a **Chave primária** e a URL de **solicitação-resposta**.
4. No Excel, vá para a seção **Serviços Web** (se você estiver na seção **previsão** , clique na seta para voltar para ir para a lista de serviços Web).
   
    ![Vá para a seleção de serviço Web](./media/excel-add-in-for-web-services/image3.png)
5. Clique em **Adicionar Serviço Web**.
6. Cole a URL na caixa de texto do complemento do Excel chamada **URL**.
7. Cole a chave de API/primária na caixa de texto chamada **Chave de API**.
8. Clique em **Adicionar**.
   
    ![URL e chave de API para um serviço Web clássico.](./media/excel-add-in-for-web-services/image6.png)
9. Para usar o serviço Web, siga as instruções anteriores de “Etapas para usar um serviço Web existente”.

## <a name="sharing-your-workbook"></a>Compartilhar sua pasta de trabalho
Se você salvar sua pasta de trabalho, as chaves de API/primária dos serviços Web adicionados também serão salvas. Isso significa que você só deve compartilhar a pasta de trabalho com pessoas confiáveis.

Faça suas perguntas na seção de comentário abaixo ou em nosso [fórum](/answers/topics/azure-machine-learning.html).