---
title: Modo de Depuração do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Iniciar uma sessão de depuração interativa ao construir fluxos de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147384"
---
# <a name="mapping-data-flow-debug-mode"></a>Modo de Depuração do Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Modo de depuração do Azure Data Factory de mapeamento de fluxo de dados pode ser alternado com o botão "Dados de fluxo Debug" na parte superior da superfície de design. Quando projetar fluxos de dados, ativar o modo de depuração será permitem que você assista interativamente os dados de forma a transformação enquanto você compila e depurar seus fluxos de dados. A sessão de depuração pode ser usada tanto em sessões de design de fluxo de dados, bem como durante a execução do pipeline depuração de fluxos de dados.

![Botão de depuração](media/data-flow/debugbutton.png "no botão depurar")

## <a name="overview"></a>Visão geral
Quando o modo de depuração está ativado, você compilará interativamente seu fluxo de dados com um cluster Spark do Active Directory. A sessão fecha quando você desativa a depuração no Azure Data Factory. Você deve estar ciente das cobranças por hora incorridas pelo Azure Databricks durante o tempo em que a sessão de depuração está ativa.

Na maioria dos casos, é uma boa prática para criar seus fluxos de dados no modo de depuração para que você possa validar sua lógica de negócios e exibir suas transformações de dados antes de publicar seu trabalho no Azure Data Factory. Use o botão de "Debug" no painel de pipeline para testar o fluxo de dados dentro de um pipeline.

> [!NOTE]
> Embora a luz do modo de depuração é verde na barra de ferramentas do Data Factory, você será cobrado na taxa de fluxo de dados de depuração de 8 núcleos/horas de computação geral com um minuto 60 time-to-live 

> [!NOTE]
>Ao executar no Modo de Depuração no Fluxo de Dados, seus dados não são gravados na transformação de Coletor. Uma sessão de depuração destina-se a servir como um agente de teste para transformações. Os coletores não são necessários durante a depuração e são ignorados no fluxo de dados. Se você quiser testar a gravação dos dados no seu coletor, execute o fluxo de dados de um Pipeline do Azure Data Factory e usar a depuração de execução de um pipeline.

## <a name="debug-settings"></a>Configurações de depuração
Depurar configurações podem ser editadas clicando em "Configurações de depuração" na barra de ferramentas tela de fluxo de dados. Você pode selecionar os limites de e/ou a fonte de arquivo a ser usado para cada uma das transformações do código-fonte aqui. Os limites de linha nesta configuração são apenas para a sessão de depuração atual. Você também pode selecionar o serviço vinculado de preparo a serem usadas para uma fonte de SQL DW. 

![Configurações de depuração](media/data-flow/debug-settings.png "configurações de depuração")

## <a name="cluster-status"></a>Status do cluster
Há um indicador de status do cluster na parte superior da superfície de design que ficará verde quando o cluster está pronto para depuração. Se o seu cluster já está em estado passivo, o indicador verde aparece quase que instantaneamente. Se o cluster já não estava em execução quando você entrar no modo de depuração, em seguida, você terá de esperar minutos de 5 a 7 para o cluster acelerar. O indicador girará até seu pronto.

Quando tiver terminado com a sua depuração, desative a opção de depuração para que seu cluster do Databricks do Azure possa ser encerrado e você não será cobrado para a atividade de depuração.

## <a name="data-preview"></a>Visualização de dados
Com a depuração ativa, a guia Visualização dos Dados fica destacada no painel inferior. Sem o modo de depuração ativo, o Fluxo de Dados mostra apenas os metadados atuais dentro e fora de cada uma de suas transformações na guia Inspecionar. A visualização de dados só consultará o número de linhas que você definiu como seu limite em suas configurações de depuração. Talvez seja necessário clicar em "Buscar dados" para atualizar a visualização dos dados.

![Visualização de dados](media/data-flow/datapreview.png "visualização de dados")

## <a name="data-profiles"></a>Perfis de dados
Selecionar colunas individuais em sua guia de visualização de dados exibirá um gráfico na extremidade direita da sua grade de dados com estatísticas detalhadas sobre cada campo. O Azure Data Factory faz uma determinação com base na amostragem de dados do tipo de gráfico a ser exibido. Campos de alta cardinalidade padrão serão /NOT NULL gráficos enquanto dados categóricos e numéricos com baixa cardinalidade exibirá os gráficos de barras que mostra a frequência de valor de dados. Você também verá máx/com comprimento de campos de cadeia de caracteres, valores mínimos/máximos em campos numéricos, desenvolvimento padrão, percentuais, contagens e média. 

![Estatísticas de coluna](media/data-flow/stats.png "estatísticas de coluna")

## <a name="next-steps"></a>Próximas etapas

Depois que tiver terminado de compilação e depuração de fluxo de dados, [executá-lo em um pipeline.](control-flow-execute-data-flow-activity.md)

Ao testar seu pipeline com um fluxo de dados, usar o pipeline [depuração executa a opção de execução.](iterative-development-debugging.md)
