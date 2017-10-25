---
title: Campos personalizados no Log Analytics | Microsoft Docs
description: "A funcionalidade Campos Personalizados do Log Analytics permite que você crie seus próprios campos pesquisáveis por meio de dados do OMS que são adicionados às propriedades de um registro coletado.  Este artigo descreve o processo para criar um campo personalizado e fornece um passo a passo detalhado com um evento de exemplo."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
ms.openlocfilehash: 9e02094f155eaade9bc5fb49c4fbb798e546e989
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-fields-in-log-analytics"></a>Campos personalizados no Log Analytics
A funcionalidade **Campos Personalizados** do Log Analytics permite que você estenda os registros existentes no repositório do OMS adicionando seus próprios campos pesquisáveis.  Os campos personalizados são populados automaticamente por meio dos dados extraídos de outras propriedades no mesmo registro.

![Visão geral dos campos personalizados](media/log-analytics-custom-fields/overview.png)

Por exemplo, o registro de exemplo abaixo tem dados úteis escondidos na descrição do evento.  A extração desses dados em propriedades separadas os torna disponíveis para ações como a classificação e a filtragem.

![Botão Pesquisar Log](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> Na Preview, você está limitado a 100 campos personalizados em seu espaço de trabalho.  Esse limite será expandido quando essa funcionalidade atingir a disponibilidade geral.
> 
> 

## <a name="creating-a-custom-field"></a>Criando um campo personalizado
Quando você cria um campo personalizado, o Log Analytics deve compreender quais dados deseja usar para popular seu valor.  Ele usa uma tecnologia da Microsoft Research chamada FlashExtract para identificar rapidamente esses dados.  Em vez de exigir que você forneça instruções explícitas, o Log Analytics aprende sobre os dados que você deseja extrair de exemplos que você fornece.

As seções a seguir fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo há um passo a passo de uma extração de exemplo.

> [!NOTE]
> O campo personalizado é populado conforme os registros correspondentes aos critérios especificados são adicionados ao armazenamento de dados do OMS, então aparecerão nos registros coletados apenas depois que o campo personalizado for criado.  O campo personalizado não será adicionado aos registros que já estão no armazenamento de dados quando ele for criado.
> 
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Etapa 1: Identificar registros que terão o campo personalizado
A primeira etapa é identificar os registros que obterão o campo personalizado.  Você inicia com uma [pesquisa de log standard](log-analytics-log-searches.md) e, em seguida, seleciona um registro para atuar como o modelo com o qual o Log Analytics aprenderá.  Quando você especifica que extrairá dados em um campo personalizado, o **Field Extraction Wizard** (Assistente de Extração de Campo) é aberto, no qual você valida e refina os critérios.

1. Acesse a **Pesquisa de Log** e use uma [consulta para recuperar os registros](log-analytics-log-searches.md) que terão o campo personalizado.
2. Selecione um registro que o Log Analytics usará para atuar como um modelo para extrair dados para popular o campo personalizado.  Você identificará os dados que deseja extrair desse registro e o Log Analytics usará essas informações para determinar a lógica para popular o campo personalizado para todos os registros semelhantes.
3. Clique no botão à esquerda de qualquer propriedade de texto do registro e selecione **Extract fields from**(Extrair campos de).
4. O **Field Extraction Wizard (Assistente de Extração de Campo) é aberto** e o registro selecionado é exibido na coluna **Main Example** (Exemplo Principal).  O campo personalizado será definido para os registros com os mesmos valores nas propriedades selecionadas.  
5. Se a seleção não for exatamente o que você deseja, selecione campos adicionais para restringir os critérios.  Para alterar os valores de campo para os critérios, você deve cancelar e selecionar um registro diferente que corresponde aos critérios desejados.

### <a name="step-2---perform-initial-extract"></a>Etapa 2: Executar a extração inicial.
Depois de identificar os registros que terão o campo personalizado, você identifica os dados que deseja extrair.  O Log Analytics usará essas informações para identificar padrões semelhantes em registros semelhantes.  Na próxima etapa, você poderá validar os resultados e fornecer mais detalhes para o Log Analytics usar em suas análises.

1. Realce o texto no registro de exemplo que você deseja para popular o campo personalizado.  Em seguida, você verá uma caixa de diálogo para fornecer um nome para o campo e para realizar a extração inicial.  Os caracteres **\_CF** serão acrescentados automaticamente.
2. Clique em **Extrair** para executar uma análise dos registros coletados.  
3. As seções **Resumo** e **Resultados da Pesquisa** exibem os resultados da extração para que você possa inspecionar sua precisão.  **Resumo** exibe os critérios usados para identificar registros e uma contagem de cada um dos valores de dados identificados.  **Resultados da Pesquisa** fornece uma lista detalhada dos registros que correspondem aos critérios.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Etapa 3: Verificar a precisão da extração e criar o campo personalizado
Após você ter executado a extração inicial, o Log Analytics exibirá seus resultados com base nos dados que já foram coletados.  Se os resultados parecerem precisos, você poderá criar o campo personalizado sem trabalho adicional.  Caso contrário, você pode refinar os resultados para que o Log Analytics possa melhorar sua lógica.

1. Se quaisquer valores na extração inicial não estiverem corretos, clique no ícone **Editar** ao lado de um registro impreciso e selecione **Modificar esse realce** para modificar a seleção.
2. A entrada é copiada para a seção **Exemplos adicionais** sob o **Exemplo Principal**.  Você pode ajustar o realce aqui para ajudar o Log Analytics a entender a seleção que ele deveria ter feito.
3. Clique em **Extrair** para usar essas novas informações para avaliar todos os registros existentes.  Os resultados podem ser modificados para registros diferentes daqueles que você acabou de modificar com base nessa nova inteligência.
4. Continue a adicionar correções até que todos os registros na extração identifiquem corretamente os dados para popular o novo campo personalizado.
5. Clique em **Save Extract** (Salvar Extração) quando estiver satisfeito com os resultados.  O campo personalizado agora está definido, mas ele não será adicionado a nenhum registro ainda.
6. Aguarde até que novos registros correspondentes aos critérios especificados sejam coletados e, em seguida, execute novamente a pesquisa de log. Os novos registros devem ter o campo personalizado.
7. Use o campo personalizado como qualquer outra propriedade de registro.  Você pode usá-lo para agregar e agrupar dados e até mesmo usá-lo para gerar novas percepções.

## <a name="viewing-custom-fields"></a>Exibindo campos personalizados
Você pode exibir uma lista de todos os campos personalizados em seu grupo de gerenciamento no bloco **Configurações** do painel do OMS.  Selecione **Dados** e **Campos personalizados** para obter uma lista de todos os campos personalizados no espaço de trabalho.  

![Campos Personalizados](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Removendo um campo personalizado
Há duas maneiras de remover um campo personalizado.  O primeiro é a opção **Remover** para cada campo ao exibir a lista completa conforme descrito acima.  O outro método é recuperar um registro e clicar no botão à esquerda do campo.  O menu terá uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Passo a passo de exemplo
A seção a seguir explica passo a passo um exemplo completo de criação de campo personalizado.  Este exemplo extrai o nome do serviço em eventos do Windows que indicam um serviço alterando o estado.  Isso depende de eventos criados pelo Gerenciador de Controle de Serviço no log do sistema em computadores com Windows.  Se você deseja acompanhar este exemplo, deve estar [coletando eventos de informações para o log do sistema](log-analytics-data-sources-windows-events.md).

Inserimos a seguinte consulta para retornar todos os eventos do Gerenciador de Controle de Serviço que têm uma ID de Evento de 7036, que é o evento que indica um serviço iniciando ou parando.

![Consultar](media/log-analytics-custom-fields/query.png)

Em seguida, selecionamos qualquer registro com a ID de Evento de 7036.

![Registro de origem](media/log-analytics-custom-fields/source-record.png)

Queremos que o nome do serviço apareça na propriedade **RenderedDescription** e selecione o botão ao lado dessa propriedade.

![Extrair campos](media/log-analytics-custom-fields/extract-fields.png)

O **Field Extraction Wizard** (Assistente de Extração de Campo) é aberto e os campos **EventLog** e **EventID** são selecionados na coluna **Main Example** (Exemplo Principal).  Isso indica que o campo personalizado será definido para eventos do log do sistema com uma ID de evento de 7036.  Isso é suficiente para que não precisemos selecionar nenhum outro campo.

![Main Example](media/log-analytics-custom-fields/main-example.png)

Realçamos o nome do serviço na propriedade **RenderedDescription** e usamos **Service** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF**.

![Título do campo](media/log-analytics-custom-fields/field-title.png)

Vemos que o nome do serviço é identificado corretamente para alguns registros, mas não para outros.   Os **Resultados da Pesquisa** mostram que parte do nome do **Adaptador de Desempenho WMI** não foi selecionada.  O **Resumo** mostra que quatro registros com o serviço **DPRMA** incluíram incorretamente uma palavra extra e dois registros identificaram **Instalador de Módulos** em vez de **Instalador de Módulos do Windows**.  

![Resultados da Pesquisa](media/log-analytics-custom-fields/search-results-01.png)

Começamos com o registro **Adaptador de Desempenho WMI** .  Clicamos em seu ícone de edição e em **Modify this highlight**(Modificar esse realce).  

![Modificar o realce](media/log-analytics-custom-fields/modify-highlight.png)

Aumentaremos o realce para incluir a palavra **WMI** e, em seguida, executamos a extração novamente.  

![Exemplo adicional](media/log-analytics-custom-fields/additional-example-01.png)

Podemos ver que as entradas para **Adaptador de Desempenho WMI** foram corrigidas e o Log Analytics também usou essa informação para corrigir os registros para **Instalador de Módulo do Windows**.  No entanto, podemos ver na seção **Resumo** que **DPMRA** ainda não está sendo identificado corretamente.

![Resultados da Pesquisa](media/log-analytics-custom-fields/search-results-02.png)

Podemos rolar para um registro com o serviço DPMRA e usar o mesmo processo para corrigir esse registro.

![Exemplo adicional](media/log-analytics-custom-fields/additional-example-02.png)

 Quando executamos a extração, podemos ver que agora todos os nossos resultados estão precisos.

![Resultados da Pesquisa](media/log-analytics-custom-fields/search-results-03.png)

Podemos ver que **Service_CF** foi criado, mas ainda não foi adicionado a nenhum registro.

![Contagem inicial](media/log-analytics-custom-fields/initial-count.png)

Depois de passar algum tempo para que novos eventos sejam coletados, podemos ver que o campo **Service_CF** agora está sendo adicionado a recursos que correspondem aos nossos critérios.

![Resultados finais](media/log-analytics-custom-fields/final-results.png)

Agora podemos usar o campo personalizado como qualquer outra propriedade de registro.  Para ilustrar isso, criamos uma consulta que agrupa pelo novo campo **Service_CF** para inspecionar quais serviços são os mais ativos.

![Agrupar por consulta](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para criar consultas usando campos personalizados para os critérios.
* Monitorar [arquivos de log personalizados](log-analytics-data-sources-custom-logs.md) que você analisa usando campos personalizados.

