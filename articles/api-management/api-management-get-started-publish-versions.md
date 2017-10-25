---
title: "Publicar versões da sua API usando o Gerenciamento de API do Azure | Microsoft Docs"
description: "Execute as etapas deste tutorial para saber como publicar várias versões no Gerenciamento de API."
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>Publicar várias versões de sua API de uma maneira previsível
Este tutorial descreve como configurar versões de sua API e escolher o modo como elas são chamadas por desenvolvedores de API.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisa criar um Serviço de Gerenciamento de API e ter uma API a qual você pode alterar (em vez da API de Conferência).

## <a name="about-versions"></a>Sobre as versões
Às vezes, é impraticável fazer com que todos os chamadores de sua API usem exatamente a mesma versão. Às vezes, você deseja publicar recursos novos ou diferentes da API para alguns usuários, enquanto outros preferem continuar com a API que funciona no momento para eles. Quando os chamadores quiserem atualizar para uma versão posterior, eles vão querer ser capazes de fazer isso usando uma abordagem fácil de entender.  Podemos fazer isso usando **versões** no Gerenciamento de API do Azure.

## <a name="walkthrough"></a>Passo a passo
Neste passo a passo, adicionamos uma nova versão a uma API existente, escolhendo um esquema de controle de versão e identificador de versão.

## <a name="add-a-new-version"></a>Adicionar uma nova versão
![Menu de contexto da API - adicionar versão](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. Navegue até a página **APIs** dentro de seu serviço de Gerenciamento de API no portal do Azure.
2. Selecione **API de Conferência** na lista de APIs, depois, selecione o menu de contexto (**...**) ao lado dela.
3. Selecione **+ Adicionar Versão**.

    > [!TIP]
    > As versões também podem ser habilitadas quando você cria uma nova API - selecione **Versão desta API?** na tela **Adicionar API**.

## <a name="choose-a-versioning-scheme"></a>Escolher um esquema de controle de versão
O Gerenciamento de API do Azure permite que você escolha a maneira com a qual você permite que chamadores especifiquem qual versão de sua API eles desejam. Faça isso escolhendo um **esquema de controle de versão**. Esse esquema pode ser **caminho, cabeçalho ou cadeia de consulta**. Em nosso exemplo, usamos o caminho.
![Adicionar a tela de versão](media/api-management-getstarted-publish-versions/AddVersion.PNG)
1. Deixe **caminho** selecionado como seu **esquema de controle de versão**.
2. Adicione **v1** como seu **identificador de versão**.

    > [!TIP]
    > Se você selecionar **cabeçalho** ou **cadeia de consulta** como um esquema de controle de versão, será necessário fornecer um valor adicional - o nome do cabeçalho ou parâmetro de cadeia de consulta.

3. Forneça uma descrição, se quiser.
4. Selecione **Criar** para configurar sua nova versão.
5. Em **API de Conferência Grande** na Lista de APIs, você pode ver duas APIs distintas - **Original** e **v1**.
![Versões listadas em uma API no portal do Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Se você adicionar uma versão a uma API sem controle de versão, sempre criamos uma **Original** para você - respondendo na URL padrão. Isso garante que os chamadores existentes não serão quebrados pelo processo de adição de uma versão. Se você criar uma nova API com versões habilitadas no início, uma Original não será criada.

6. Agora você pode editar e configurar **v1** como uma API completamente separada de **Original**. As alterações em uma versão não afetam a outra.

## <a name="add-the-version-to-a-product"></a>Adicionar a versão a um produto
Para que os chamadores vejam a nova versão, ela deve ser adicionada a um **produto** (produtos não são herdados das versões pai).

1. Selecione **Produtos** da página de gerenciamento de serviço.
2. Selecione **Ilimitado**.
3. Selecione **APIs**.
4. Selecione **Adicionar**.
5. Selecione **API de Conferência, Versão v1**.
6. Retorne à página de gerenciamento de serviço e selecione **APIs**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Procurar a versão no portal do desenvolvedor
1. Selecione **Portal do Desenvolvedor** no menu superior.
2. Selecione **APIs**, observe que **API de Conferência** mostra as versões **Original** e **v1**.
3. Selecione **v1**.
4. Observe a **URL Se solicitação** da primeira operação na lista. Ela mostra que o caminho da URL da API inclui **v1**.
![Versão mostrada no portal do desenvolvedor](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)
