---
title: Moderar imagens com listas personalizadas e o console de API – Content Moderator
titleSuffix: Azure Content Moderator
description: Você pode usar a API de Gerenciamento da Lista no Content Moderator do Azure para criar listas personalizadas de imagens.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 2b2ab138945d32ca874dc20576d412c862965dc9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564366"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderar com as listas de imagem personalizada no console de API

Você pode usar a [API de Gerenciamento da Lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) no Content Moderator do Azure para criar listas personalizadas de imagens. Use as listas personalizadas de imagens com a API de Moderação de Imagem. A operação de moderação de imagem avalia sua imagem. Se você criar listas personalizadas, a operação também compara as imagens em suas listas personalizadas. Você pode usar listas personalizadas para bloquear ou permitir a imagem.

> [!NOTE]
> Há um limite máximo de **cinco listas de imagens**, e cada lista **não deve exceder 10 mil imagens**.
>

Você pode usar a API de Gerenciamento de Lista para fazer as seguintes tarefas:

- Cria uma lista.
- Adicionar imagens a uma lista.
- Examinar imagens com imagens na lista.
- Excluir imagens de uma lista.
- Excluir uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações da lista sejam incluídas em uma nova verificação.

## <a name="use-the-api-console"></a>Usar o console de API
Antes de você pode testar a API no console on-line, você precisa da chave de assinatura. Ela está localizada na guia **Configurações**, na caixa **Ocp-Apim-Subscription-Key**. Para mais informações, confira [Visão Geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar o índice de pesquisa

Após você fazer alterações em uma lista de imagens, você deve atualizar o índice para que as alterações sejam incluídas nas verificações futuras. Esta etapa é semelhante a como um mecanismo de pesquisa na área de trabalho (se habilitado) ou um mecanismo de pesquisa da web continuamente atualiza o índice para incluir novos arquivos ou páginas.

1. Na [referência de API de Gerenciamento de Lista de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu à esquerda, selecione **Listas de imagens**e, em seguida, selecione **Atualizar o índice de pesquisa**.

   A página **Listas de imagens - Atualizar o índice de pesquisa** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 
 
    ![Seleção de região de página de Listas de imagens - Atualizar o índice de pesquisa](images/test-drive-region.png)

    O console de API **Listas de imagens - atualizar o índice de pesquisa** será aberto.

3. Na caixa **listId**, digite a ID da lista. Insira a chave de assinatura e selecione **Enviar**.

   ![A Caixa de conteúdo de resposta do console Listas de imagens - Atualizar o índice de pesquisa](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Criar uma lista de imagens

1. Vá para a [referência de API de Gerenciamento de Lista de Imagens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   A página **Listas de Imagens - Criar** será aberta. 

3. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.

   ![Seleção da região da página Lista de Imagens - Criar](images/test-drive-region.png)

   O console da API **Listas de Imagens - Criar** será aberto.
 
4. Na caixa **Ocp-Apim-Subscription-Key**, insira sua chave de assinatura.

5. Na caixa **Corpo da solicitação**, insira valores para **Nome** (por exemplo, MyList) e **Descrição**.

   ![Nome e descrição de corpo de solicitação do console Listas de imagens - Criar](images/try-terms-list-create-1.png)

6. Use espaços reservados de par chave-valor para atribuir metadados mais descritivos à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Adicione metadados de lista como pares chave-valor e não as imagens reais.
 
7. Selecione **Enviar**. Sua lista é criada. Observe o valor **ID** associado à nova lista. Você precisa dessa ID para outras funções de gerenciamento da lista de imagem.

   ![A Caixa de conteúdo de resposta do console Listas de imagens - Criar mostra a ID da lista](images/try-terms-list-create-2.png)
 
8. Em seguida, adicione imagens ao MyList. No menu à esquerda, selecione **Imagem**e, em seguida, selecione **Adicionar imagem**.

   A página **Imagem - Adicionar imagem** será aberta. 

9. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.

   ![Seleção de região da página Imagem - Adicionar imagem](images/test-drive-region.png)

   O console da API **Imagem - Adicionar imagem** será aberto.
 
10. Na caixa **listId**, insira a ID da lista que é gerada e, em seguida, insira a URL da imagem que você deseja adicionar. Insira a chave de assinatura e selecione **Enviar**.

11. Para verificar se a imagem foi adicionada à lista, no menu à esquerda, selecione **Imagem**e, em seguida, selecione **Obter todas as Ids de imagem**.

    O console de API **Imagem - Obter todas as Ids de imagem** será aberto.
  
12. Na caixa **listId**, insira a ID da lista e, em seguida, insira a chave de assinatura. Selecione **Enviar**.

    ![A Caixa de conteúdo de resposta do console Imagem - Obter todas as Ids de imagem lista as imagens que você inseriu](images/try-image-list-create-11.png)
 
10. Adicione mais algumas imagens. Agora que você criou uma lista personalizada de imagens, tente [Avaliar imagens](try-image-api.md) usando a lista de imagens personalizadas. 

## <a name="delete-images-and-lists"></a>Excluir imagens e listas

Excluir uma imagem ou uma lista é simples. Você pode usar a API para realizar as seguinte tarefas:

- Exclua uma imagem. (**Imagem - Excluir**)
- Exclua todas as imagens em uma lista sem excluir a lista. (**Imagem - Excluir todas as imagens**)
- Exclua uma lista e todo seu conteúdo. (**Listas de imagens - Excluir**)

Este exemplo exclui uma única imagem:

1. Na [referência de API de Gerenciamento de Lista de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu à esquerda, selecione **Imagem**e, em seguida, selecione **Excluir**. 

   A página **Imagem - Excluir** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção de região da página Imagem - Excluir](images/test-drive-region.png)
 
   O console da API **Imagem - Excluir** é aberto.
 
3. Na caixa **listId**, digite a ID da lista para excluir uma imagem.  Este é o número retornado no console **Imagem - Obter todas as Ids de imagem** para MyList. Em seguida, insira o **ImageId** da imagem a ser excluída. 

Em nosso exemplo, a ID da lista é **58953**, o valor de **ContentSource**. A ID da imagem é **59021**, o valor para **ContentIds**.

1. Insira a chave de assinatura e selecione **Enviar**.

1. Para verificar se a imagem foi excluída, use o console **Imagem - Obter todas as Ids de imagem**.
 
## <a name="change-list-information"></a>Alterar informações da lista

Você pode editar o nome e a descrição de uma lista e adicionar itens de metadados.

1. Na [referência de API de Gerenciamento de Lista de Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), no menu à esquerda, selecione **Listas de imagens**e, em seguida, selecione **Atualizar detalhes**. 

   A página **Listas de imagens - Atualizar detalhes** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.  

    ![Seleção de região de página Listas de imagens - Atualizar detalhes](images/test-drive-region.png)

    O console de API **Listas de imagens - Atualizar detalhes** será aberto.
 
3. Na caixa **listId**, insira a ID da lista e, em seguida, insira a chave de assinatura.

4. Na caixa **Corpo da solicitação**, faça as edições e, em seguida, selecione o botão **enviar** na página.

   ![Edições do Corpo de solicitação do console Lista de imagens - Atualizar detalhes](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou inicie com o [Início rápido do .NET de Lista de Imagens](image-lists-quickstart-dotnet.md) para integrar o aplicativo.
