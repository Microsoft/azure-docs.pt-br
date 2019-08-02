---
title: Modere textos com listas de termos personalizados – Content Moderator
titleSuffix: Azure Cognitive Services
description: Use a API de Gerenciamento de Lista para criar listas de termos personalizadas para usar com a API de Moderação de Texto.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0a4e9b7925c2309a9682156934e9d94fa83c0d4b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564400"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderar com as listas de termos personalizadas no console de API

A lista de termos global padrão do Content Moderator do Azure é suficiente para a maioria das necessidades de moderação de conteúdo. No entanto, você precisará de examinar os termos que são específicos para sua organização. Por exemplo, você talvez queira marcar nomes de concorrentes para análise adicional. 

Use a [API de Gerenciamento de Lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) para criar listas de termos personalizadas para usar com a API de Moderação de Texto. A operação **Texto - Examinar** verifica se há obscenidades em seu texto e também compara o texto em listas negras personalizadas e compartilhadas.

> [!NOTE]
> Há um limite máximo de **5 listas de termos** com cada lista para **não exceder 10.000 termos**.
>

Você pode usar a API de Gerenciamento de Lista para realizar as seguintes tarefas:
- Cria uma lista.
- Adicionar termos a uma lista.
- Examinar termos com os termos em uma lista.
- Excluir termos de uma lista.
- Excluir uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações da lista sejam incluídas em uma nova verificação.

## <a name="use-the-api-console"></a>Usar o console de API

Antes de você testar a API no console on-line, você precisa da chave de assinatura. Esta chave está localizada na guia **Configurações**, na caixa **Ocp-Apim-Subscription-Key**. Para mais informações, confira [Visão Geral](overview.md).

## <a name="refresh-search-index"></a>Atualizar o índice de pesquisa

Após você fazer alterações em uma lista de termos, você deve atualizar o índice para que as alterações sejam incluídas nas verificações futuras. Esta etapa é semelhante a como um mecanismo de pesquisa na área de trabalho (se habilitado) ou um mecanismo de pesquisa da web continuamente atualiza o índice para incluir novos arquivos ou páginas.

1. Na [referência de API de Gerenciamento de Lista de Termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu à esquerda, selecione **Listas de termos**e, em seguida, selecione **Atualizar o índice de pesquisa**. 

   A página **Listas de termos - Atualizar o índice de pesquisa** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção de região de página Listas de termos - Atualizar o índice de pesquisa](images/test-drive-region.png)

   O console de API **Listas de termos - Atualizar o índice de pesquisa** será aberto.

3. Na caixa **listId**, digite a ID da lista. Insira a chave de assinatura e selecione **Enviar**.

   ![A Caixa de conteúdo de resposta do console da API Listas de termos - Atualizar o índice de pesquisa](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Criar uma lista de termos
1. Vá para a [referência de API de Gerenciamento de Lista de Termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   A página **Listas de Termos - Criar** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção da região da página Lista de Termos - Criar](images/test-drive-region.png)

   O console de API **Listas de Termos - Criar** será aberto.
 
3. Na caixa **Ocp-Apim-Subscription-Key**, insira sua chave de assinatura.

4. Na caixa **Corpo da solicitação**, insira valores para **Nome** (por exemplo, MyList) e **Descrição**.

   ![Nome e descrição de corpo de solicitação do console Listas de termos - Criar](images/try-terms-list-create-1.png)

5. Use espaços reservados de par chave-valor para atribuir metadados mais descritivos à sua lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Adicione metadados de lista como pares chave-valor e não termos reais.
 
6. Selecione **Enviar**. Sua lista é criada. Observe o valor **ID** associado à nova lista. Você precisa dessa ID para outras funções de gerenciamento da lista de termos.

   ![A Caixa de conteúdo de resposta do console Listas de Termos - Criar mostra a ID da lista](images/try-terms-list-create-2.png)
 
7. Adicionar termos a MyList. No menu à esquerda, em **Termo**, selecione **Adicionar termo**. 

   A página **Termo - Adicionar termo** será aberta. 

8. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção de região de página Termo - Adicionar termo](images/test-drive-region.png)

   O console de API **Termo - Adicionar termo** será aberto.
 
9. Na caixa **listId**, insira a ID da lista que é gerada e selecione um valor para **idioma**. Insira a chave de assinatura e selecione **Enviar**.

   ![Parâmetros de consulta do console Termo - Adicionar termo](images/try-terms-list-create-3.png)
 
10. Para verificar se o termo foi adicionado à lista, no menu à esquerda, selecione **Termo**e, em seguida, selecione **Obter todos os termos**. 

    O console de API **Termo - Obter todos os termos** será aberto.

11. Na caixa **listId**, insira a ID da lista e, em seguida, insira a chave de assinatura. Selecione **Enviar**.

12. Na caixa **Conteúdo de resposta**, verifique os termos que você inseriu.

    ![A Caixa de conteúdo de resposta do console Termo - Obter todos os termos lista os termos que você inseriu](images/try-terms-list-create-4.png)
 
13. Adicione mais alguns termos. Agora que você criou uma lista personalizada de termos, tente [verificação algum texto](try-text-api.md) usando a lista de termos personalizados. 

## <a name="delete-terms-and-lists"></a>Excluir termos e listas

Excluir um termo ou uma lista é simples. Você pode usar a API para realizar as seguinte tarefas:

- Excluir um termo. (**Termo - Excluir**)
- Excluir todos os termos em uma lista sem excluir a lista. (**Termo - Excluir todos os termos**)
- Excluir uma lista e todo seu conteúdo. (**Listas de termos - Excluir**)

Este exemplo exclui um único termo.

1. Na [referência de API de Gerenciamento de Lista de Termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu à esquerda, selecione **Termo** e, em seguida, selecione **Excluir**. 

   O **Termo - Excluir** é aberto.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção de região da página Termo - Excluir](images/test-drive-region.png)

   O console de API **Termo - Excluir** será aberto.
  
3. Na caixa **listId**, digite a ID da lista da qual você deseja excluir um termo. Essa ID é o número (em nosso exemplo, **122**) que é retornado no console **Lista de termos - Obter detalhes** para MyList. Insira o termo e selecione um idioma.
 
   ![Parâmetros de consulta do console Termo - Excluir](images/try-terms-list-delete-1.png)

4. Insira a chave de assinatura e selecione **Enviar**.

5. Para verificar se o termo foi excluído, use o console **Lista de termos - Obter todos**.

   ![A Caixa de conteúdo de resposta do console Listas de termos - Obter todos mostra que o termo foi excluído](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Alterar informações da lista

Você pode editar o nome e a descrição de uma lista e adicionar itens de metadados.

1. Na [referência de API de Gerenciamento de Lista de Termos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), no menu à esquerda, selecione **Listas de termos**e, em seguida, selecione **Atualizar detalhes**. 

   A página **Listas de termos - Atualizar detalhes** será aberta.

2. Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local. 

   ![Seleção de região de página Listas de termos - Atualizar detalhes](images/test-drive-region.png)

   O console de API **Listas de termos - Atualizar detalhes** será aberto.

3. Na caixa **listId**, insira a ID da lista e, em seguida, insira a chave de assinatura.

4. Na caixa **Corpo da solicitação**, faça as edições e, em seguida, selecione **Enviar**.

   ![Edições do Corpo de solicitação do console Lista de Termos - Atualizar detalhes](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou inicie com o [Início rápido do .NET de Lista de Termos](term-lists-quickstart-dotnet.md) para integrar o aplicativo.
