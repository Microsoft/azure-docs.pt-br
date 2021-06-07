---
author: baanders
description: incluir um arquivo do Visual Studio no processo de publicação de uma função do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630242"
---
Para publicar o projeto em um aplicativo de funções do Azure, clique com o botão direito do mouse em *Gerenciador de Soluções*, selecione o projeto, depois clique em **Publicar**.

> [!IMPORTANT] 
> Ao fazer uma publicação em um aplicativo de funções do Azure, sua assinatura incorrerá em encargos adicionais, mesmo que você use os Gêmeos Digitais do Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Captura de tela do Visual Studio mostrando o menu de solução que aparece ao clicar com o botão direito do mouse. A publicação é realçada no menu.":::

Na página *Publicar* a seguir, deixe a seleção de destino padrão do **Azure** e selecione *Avançar*. 

Para um destino específico, escolha **Aplicativo de Funções do Azure (Windows)** e selecione *Avançar*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Captura de tela do Visual Studio na caixa de diálogo da função Publicar no Azure. O Aplicativo de Função do Azure (Windows) está selecionado na página de destino específica.":::

Na página da *instância do Azure Functions*, escolha sua assinatura. Em seguida, selecione o ícone *+* para criar uma função do Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Captura de tela do Visual Studio na caixa de diálogo da função Publicar no Azure. O botão + para criar uma função é realçado na página de instância do Functions.":::

Na janela *Aplicativo de Funções (Windows) – Criar*, preencha os campos da seguinte maneira:
* **Nome** é o nome do plano de consumo que o Azure usará para hospedar o aplicativo do Azure Functions. Ele também se tornará o nome do aplicativo de funções que contém sua função real. Você pode escolher seu valor exclusivo ou deixar a sugestão padrão.
* **Assinatura** deve corresponder à assinatura que você deseja usar 
* Da mesma forma, o **Grupo de recursos** deve corresponder ao que você deseja usar
* Deixe o **Tipo de plano** como *Consumo*
* Selecione **Local** correspondente ao local de seu grupo de recursos
* Crie um recurso do **Armazenamento do Azure** usando o link *Novo...* . Defina o local de modo que corresponda ao seu grupo de recursos, use os outros valores padrão e selecione "OK".

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Captura de tela do Visual Studio na caixa de diálogo da função Publicar no Azure. Os detalhes de um novo aplicativo de funções estão sendo preenchidos, incluindo nome, assinatura, grupo de recursos, tipo de plano, local e Armazenamento do Azure.":::

Em seguida, selecione **Criar**.

Após uma breve espera enquanto o serviço de aplicativo é criado, a caixa de diálogo deve retornar à página *Instância de funções*, com seu novo aplicativo de funções aparecendo na área **Aplicativos de Funções** aninhada embaixo do seu grupo de recursos. Selecione *Concluir*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publicar função do Azure no Visual Studio: instância do Azure Functions (após o aplicativo de funções)":::

No painel *Publicar* que é aberto na janela principal do Visual Studio, verifique se todas as informações parecem corretas e selecione **Publicar**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Captura de tela do Visual Studio na caixa de diálogo da função Publicar no Azure. O novo aplicativo de funções aparece na lista de aplicativos de funções e há um botão Concluir.":::

> [!NOTE]
> Você verá um pop-up como este: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Captura de tela da janela pop-up do Visual Studio chamada Publicar credenciais. Ele contém campos para um nome de usuário e uma senha, além de um botão para Tentar recuperar credenciais do Azure." border="false":::
> Selecione **Tentativa de recuperar credenciais do Azure** e escolha **Salvar**.
>
> Se você vir um aviso para *Versão das Funções de Atualização no Azure* ou se *Sua versão do runtime de funções não corresponder à versão em execução no Azure*:
>
> siga os prompts para atualizar para a versão mais recente do Azure Functions runtime. Esse problema poderá ocorrer caso esteja usando uma versão anterior do Visual Studio.

Seu aplicativo de funções agora é publicado no Azure.