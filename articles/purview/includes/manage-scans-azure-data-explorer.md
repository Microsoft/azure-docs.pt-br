---
author: amberz
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.author: amberz
ms.openlocfilehash: bf872feae9c3a7ca94e5252872adee2b653f5524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896081"
---
## <a name="creating-and-running-a-scan"></a>Criando e executando uma verificação

> [!Note] 
> As etapas e capturas de tela mostradas abaixo ilustram o processo geral para o gerenciamento de verificações em diferentes tipos de fonte de dados. Suas opções podem diferir ligeiramente, dependendo dos tipos de fontes de dados com os quais você está trabalhando.

Para criar e executar uma nova verificação, faça o seguinte:

1. Navegue até as **Fontes**

1. Selecione a fonte de dados que você registrou.

1. Selecione **+ Nova verificação**

1. Selecione a credencial para se conectar à fonte de dados. 

   :::image type="content" source="media/manage-scans/set-up-scan-data-explorer.png" alt-text="Configurar verificação":::

1. Você pode delimitar o escopo da sua verificação para partes específicas da fonte de dados, como pastas, coleções ou esquemas, verificando os itens apropriados na lista.

   :::image type="content" source="media/manage-scans/scope-your-scan-data-explorer.png" alt-text="Escopo da verificação":::

1. Selecione um conjunto de regras de verificação para a sua verificação. Você pode escolher entre o padrão do sistema, os personalizados que já existem ou criar outro embutido.

   :::image type="content" source="media/manage-scans/scan-rule-set-data-explorer.png" alt-text="Verificar conjunto de regras":::

1. Escolha o gatilho da verificação. Você pode configurar um agendamento ou executar a verificação uma vez.

   :::image type="content" source="media/manage-scans/trigger-scan-data-explorer.png" alt-text="trigger":::

1. Examine a verificação e selecione **Salvar e executar**.

## <a name="viewing-your-scans-and-scan-runs"></a>Exibindo as verificações e as execuções de verificação

Para exibir as verificações existentes, faça o seguinte:

1. Navegue até o centro de gerenciamento. Selecione **Fontes de dados** na seção **Fontes e verificação**. 

2. Selecione a fonte de dados desejada. Você verá uma lista de verificações existentes nessa fonte de dados.

3. Selecione a verificação cujos resultados você está interessado em exibir.

4. Esta página mostrará que todas as verificações anteriores são executadas junto com as métricas e o status de cada execução de verificação. Ele também exibirá se a verificação foi agendada ou manual, quantos ativos tiveram classificações aplicadas, quantos ativos totais foram descobertos, a hora de início e de término da verificação e a duração total da verificação.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Gerenciar suas verificações – editar, excluir ou cancelar

Para gerenciar ou excluir uma verificação, faça o seguinte:

1. Navegue até o centro de gerenciamento. Selecione **Fontes de dados** na seção **Fontes e verificação** e selecione a fonte de dados desejada.

2. Selecione a verificação que você deseja gerenciar. Você pode editar a verificação selecionando **Editar**.

3. Você pode excluir sua verificação selecionando **Excluir**. 
