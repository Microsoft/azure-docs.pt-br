---
title: "Chamar uma função do PowerApps | Microsoft Docs"
description: "Crie um conector personalizado e, em seguida, chame uma função usando esse conector."
services: functions
keywords: "aplicativos de nuvem, serviços de nuvem, PowerApps, processos de negócios, aplicativo de negócios"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 1e262fde37b68bcfcee3c974deb91bd07965de19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-function-from-powerapps"></a>Chamar uma função do PowerApps
A plataforma [PowerApps](https://powerapps.microsoft.com) destina-se a especialistas comerciais para o build de aplicativos sem código de aplicativo tradicional. Desenvolvedores profissionais podem usar o Azure Functions para estender os recursos do PowerApps e, ao mesmo tempo, proteger os construtores de aplicativo do PowerApps de detalhes técnicos.

Compile um aplicativo neste tópico, com base em um cenário de manutenção de turbinas eólicas. Este tópico mostra como chamar a função que você definiu em [Criar uma definição de OpenAPI para uma função](functions-openapi-definition.md). A função determina se um reparo de emergência em uma turbina eólica é econômico.

![Aplicativo concluído no PowerApps](media/functions-powerapps-scenario/finished-app.png)

Para obter informações sobre como chamar a mesma função do Microsoft Flow, consulte [Chamar uma função do Microsoft Flow](functions-flow-scenario.md).

Neste tópico, você aprenderá a:

> [!div class="checklist"]
> * Preparar os dados de exemplo no Excel.
> * Exportar uma definição de API.
> * Adicionar uma conexão à API.
> * Criar um aplicativo e adicionar fontes de dados.
> * Adicionar controles para exibir dados no aplicativo.
> * Adicionar controles para chamar a função e exibir dados.
> * Executar o aplicativo para determinar se um reparo é econômico.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma [conta do PowerApps](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md) ativa com as mesmas credenciais de entrada da sua conta do Azure. 
+ Excel, pois você usará o Excel como uma fonte de dados para seu aplicativo.
+ Conclua o tutorial [Criar uma definição de OpenAPI para uma função](functions-openapi-definition.md).


## <a name="prepare-sample-data-in-excel"></a>Preparar os dados de exemplo no Excel
Você começa a preparação dos dados de exemplo que usará no aplicativo. Copie a tabela a seguir no Excel. 

| Title      | Latitude  | Longitude  | LastServiceDate | MaxOutput | ServiceRequired | EstimatedEffort | InspectionNotes                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| Turbina 1  | 47.438401 | -121.383767 | 2/23/2017       | 2850      | Sim             | 6               | Este é o segundo problema deste mês.       |
| Turbina 4  | 47.433385 | -121.383767 | 5/8/2017        | 5400      | Sim             | 6               |                                            |
| Turbina 33 | 47.428229 | -121.404641 | 6/20/2017       | 2800      |                 |                 |                                            |
| Turbina 34 | 47.463637 | -121.358824 | 2/19/2017       | 2800      | Sim             | 7               |                                            |
| Turbina 46 | 47.471993 | -121.298949 | 3/2/2017        | 1.200      |                 |                 |                                            |
| Turbina 47 | 47.484059 | -121.311171 | 8/2/2016        | 3350      |                 |                 |                                            |
| Turbina 55 | 47.438403 | -121.383767 | 10/2/2016       | 2400      | Sim             | 40               | Temos algumas partes recebidas para este. |

1. No Excel, selecione os dados e, na guia **Início**, clique em **Formatar como tabela**.

    ![Formatar como tabela](media/functions-powerapps-scenario/format-table.png)

1. Selecione um estilo e, em seguida, clique em **OK**.

1. Com a tabela selecionada, na guia **Design**, insira `Turbines` para **Nome da tabela**.

    ![Nome da tabela](media/functions-powerapps-scenario/table-name.png)

1. Salve a pasta de trabalho do Excel.

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Adicionar uma conexão à API
A API personalizada (também conhecida como um conector personalizado) está disponível no PowerApps, mas você deve fazer uma conexão para a API antes de poder usá-la em um aplicativo.

1. Em [web.powerapps.com](https://web.powerapps.com), clique em **Conexões**.

    ![Conexões do PowerApps](media/functions-powerapps-scenario/powerapps-connections.png)

1. Clique em **Nova Conexão**, role para baixo até o conector **Reparo da Turbina** e clique nele.

    ![Nova conexão](media/functions-powerapps-scenario/new-connection.png)

1. Insira a chave de API e clique em **Criar**.

    ![Criar conexão](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Se você compartilhar seu aplicativo com outras pessoas, cada pessoa que trabalha ou usa o aplicativo também deverá inserir a chave de API para conectar-se à API. Esse comportamento pode ser alterado no futuro e atualizaremos este tópico para refletir isso.

## <a name="create-an-app-and-add-data-sources"></a>Criar um aplicativo e adicionar fontes de dados
Agora você está pronto para criar o aplicativo no PowerApps e adicionar os dados do Excel e a API personalizada como fontes de dados para o aplicativo.

1. Em [web.powerapps.com](https://web.powerapps.com), no painel esquerdo, clique em **Novo Aplicativo**.

1. Em **Aplicativo em branco**, clique em **Layout do telefone**.

    ![Criar aplicativo de tablet](media/functions-powerapps-scenario/create-phone-app.png)

    O aplicativo é aberto no PowerApps Studio para Web. A imagem a seguir mostra as diferentes partes do PowerApps Studio. Esta imagem é para o aplicativo concluído. Você verá uma tela em branco primeiramente no painel central.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) Barra de navegação esquerda**, na qual você vê uma exibição hierárquica de todos os controles em cada tela

    **(2) Painel central**, que mostra a tela na qual você está trabalhando

    **(3) Painel direito**, no qual você define opções como fontes de dados e layout

    **(4) lista suspensa Propriedade**, na qual você pode selecionar as propriedades as quais as fórmulas se aplicam

    **(5) Barra de fórmulas**, na qual você pode adicionar fórmulas (como no Excel) que definem o comportamento do aplicativo
    
    **(6) Faixa de opções**, na qual você adiciona controles e personaliza os elementos de design

1. Adicione o arquivo do Excel como uma fonte de dados.

    1. No painel direito, na guia **Dados**, clique em **Adicionar fonte de dados**.

        ![Adicionar fonte de dados](media/functions-powerapps-scenario/add-data-source.png)

    1. Clique em **Adicionar dados estáticos em seu aplicativo**.

        ![Adicionar fonte de dados](media/functions-powerapps-scenario/add-static-data.png)

        Normalmente você deve ler e gravar dados de uma fonte externa, mas você está adicionando os dados do Excel como dados estáticos porque este é um exemplo.

    1. Navegue até o arquivo do Excel que você salvou, selecione a tabela **Turbinas** e, em seguida, clique em **Conectar**.

        ![Adicionar fonte de dados](media/functions-powerapps-scenario/choose-table.png)

1. Adicione a API personalizada como uma fonte de dados.

    1. Na guia **Dados**, clique em **Adicionar fonte de dados**.

    1. Clique em **Reparo da turbina**.

        ![Conector de reparo da turbina](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Adicionar controles para exibir dados no aplicativo
Agora que as fontes de dados estão disponíveis no aplicativo, adicione uma tela em seu aplicativo para exibir os dados da turbina.

1. Na guia **Início**, clique em **Nova tela** > **Tela de lista**.

    ![Tela de lista](media/functions-powerapps-scenario/list-screen.png)

    O PowerApps adiciona uma tela que contém uma *galeria* para exibir itens e outros controles que permitem pesquisar, classificar e filtrar.

1. Altere a barra de título para `Turbine Repair` e redimensione a galeria para que haja espaço para outros controles.

    ![Alterar o título e redimensionar a galeria](media/functions-powerapps-scenario/gallery-title.png)

1. Com a galeria selecionada, no painel direito, na guia **Dados**, altere a fonte de dados de **CustomGallerySample** para **Turbinas**.

    ![Alterar fonte de dados](media/functions-powerapps-scenario/change-data-source.png)

    O conjunto de dados não contém uma imagem, então, em seguida, altere o layout para ajustar melhor os dados. 

1. Ainda no painel direito, altere **Layout** para **Título, subtítulo e corpo**.

    ![Alterar o layout de galeria](media/functions-powerapps-scenario/change-layout.png)

1. Como última etapa no painel direito, altere os campos que são exibidos na galeria.

    ![Alterar campos da galeria](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** = Title 

1. Com a galeria selecionada, defina a propriedade **TemplateFill** para a seguinte fórmula: `If(ThisItem.IsSelected, Orange, White)`.

    ![Fórmula de preenchimento de modelo](media/functions-powerapps-scenario/formula-fill.png)

    Agora é mais fácil ver qual item da galeria está selecionado.

    ![Item selecionado](media/functions-powerapps-scenario/selected-item.png)

1. Você não precisa da tela original no aplicativo. No painel esquerdo, passe o mouse sobre **Screen1**, clique em **. . .** e em **Excluir**.

    ![Tela de exclusão](media/functions-powerapps-scenario/delete-screen.png)

Há muitos outros recursos de formatação que normalmente faria em um aplicativo de produção, mas vamos para a parte importante deste cenário: chamar a função.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Adicionar controles para chamar a função e exibir dados
Você tem um aplicativo que exibe dados de resumo para cada turbina, então agora é hora de adicionar controles que chamem a função que você criou e exibir os dados que são retornados. Acesse a função com base na maneira como você a nomeou na definição de OpenAPI. Nesse caso, `TurbineRepair.CalculateCosts()`.

1. Na faixa de opções, na guia **Inserir**, clique em **Botão**. Em seguida, na mesma guia, clique em **Rótulo**

    ![Botão de inserção e rótulo](media/functions-powerapps-scenario/insert-controls.png)

1. Arraste o botão e o rótulo para a parte de baixo da galeria e redimensione o rótulo. 

1. Selecione o texto do botão e altere-o para `Calculate costs`. O aplicativo deverá ter a seguinte imagem.

    ![Aplicativo com botão](media/functions-powerapps-scenario/move-button-label.png)

1. Selecione o botão e insira a fórmula a seguir para a propriedade **OnSelect** do botão.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Essa fórmula é executada quando o botão é clicado e faz o seguinte se o item da galeria selecionado tiver um valor **ServiceRequired** de `Yes`:

    + Limpa a *coleção* `DetermineRepair` para remover dados de chamadas anteriores. Uma coleção é uma variável tabular.

    + Atribui à coleção os dados retornados chamando a função `TurbineRepair.CalculateCosts()`. 
    
        Os valores passados para a função são provenientes dos campos **EstimatedEffort** e **MaxOutput** para o item selecionado na galeria. Esses campos não são exibidos na galeria, mas eles ainda estão disponíveis para uso em fórmulas.

1. Selecione o rótulo e digite a seguinte fórmula na propriedade **Texto** do rótulo.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Esta fórmula usa a função `First()` para acessar a primeira (e única) linha da coleção `DetermineRepair`. Em seguida, exibe os três valores que a função retorna: `message`, `costToFix` e `revenueOpportunity`. Esses valores ficam em branco antes de o aplicativo ser executado pela primeira vez.

    O aplicativo concluído deverá ter a seguinte imagem.

    ![Aplicativo concluído antes da execução](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Execute o aplicativo
Você tem um aplicativo concluído! Agora é hora de executá-lo e ver que as chamadas da função em ação.

1. No canto superior direito do PowerApps Studio, clique no botão de execução: ![Botão de execução do aplicativo](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Selecione uma turbina com um valor de `Yes` para **ServiceRequired**, em seguida, clique no botão **Calcular custos**. Você verá algo semelhante ao mostrado na imagem a seguir.

    ![Aplicativo concluído no PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Teste as outras turbinas para ver o que é retornado pela função a cada vez.

## <a name="next-steps"></a>Próximas etapas
Neste tópico, você aprendeu a:

> [!div class="checklist"]
> * Preparar os dados de exemplo no Excel.
> * Exportar uma definição de API.
> * Adicionar uma conexão à API.
> * Criar um aplicativo e adicionar fontes de dados.
> * Adicionar controles para exibir dados no aplicativo.
> * Adicionar controles para chamar a função e exibir dados
> * Executar o aplicativo para determinar se um reparo é econômico.

Para saber mais sobre o PowerApps, consulte [Introdução ao PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Para saber mais sobre outros cenários interessantes que usam Azure Functions, consulte [Chamar uma função do Microsoft Flow](functions-flow-scenario.md) e [Criar uma função que se integre ao Aplicativo Lógico do Azure](functions-twitter-email.md).