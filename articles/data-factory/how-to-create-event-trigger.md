---
title: Criar gatilhos baseados em evento no Azure Data Factory
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em resposta a um evento.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: ae8b1eab81e3c898c25a613f552a49c8de64f49d
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889120"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-storage-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento de armazenamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os gatilhos de eventos de armazenamento que você pode criar em seus pipelines de Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Cenários de integração de dados geralmente exigem que Data Factory clientes disparem pipelines com base em eventos que ocorrem na conta de armazenamento, como a chegada ou a exclusão de um arquivo na conta de armazenamento de BLOBs do Azure. Data Factory integra-se nativamente com a [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/), que permite disparar pipelines nesses eventos.

Para uma introdução de dez minutos e demonstração desse recurso, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]

> [!NOTE]
> A integração descrita neste artigo depende na [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se a assinatura está registrada no provedor de recursos da Grade de Eventos. Para obter mais informações, confira [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Você deve ser capaz de fazer a ação *Microsoft. EventGrid/eventSubscriptions/**. Esta ação faz parte da função interna de colaborador do EventGrid EventSubscription.

## <a name="data-factory-ui"></a>IU do Data Factory

Esta seção mostra como criar um gatilho de evento de armazenamento dentro da interface do usuário do Azure Data Factory.

1. Alterne para a guia **Editar** , mostrada com um símbolo de lápis.

1. Selecione **gatilho** no menu e, em seguida, selecione **novo/editar**.

1. Na página **Adicionar gatilhos** , selecione **escolher gatilho...** e, em seguida, selecione **+ novo**.

1. Selecionar evento de **armazenamento** de tipo de gatilho

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image1.png" alt-text="Captura de tela da página autor para criar um novo gatilho de evento de armazenamento na interface do usuário Data Factory.":::

1. Selecione sua conta de armazenamento na lista suspensa de assinatura do Azure ou manualmente usando a ID do recurso da conta de armazenamento. Escolha em qual contêiner você deseja que os eventos ocorram. A seleção de contêiner é necessária, mas lembre-se de que a seleção de todos os contêineres pode levar a um grande número de eventos.

   > [!NOTE]
   > O gatilho de evento de armazenamento atualmente dá suporte apenas a Azure Data Lake Storage Gen2 e contas de armazenamento da versão 2 de uso geral. Devido a uma limitação da grade de eventos do Azure, Azure Data Factory dá suporte apenas a um máximo de 500 disparadores de eventos de armazenamento por conta de armazenamento. Se você atingir o limite, entre em contato com o suporte para obter recomendações e aumente o limite após a avaliação por equipe de grade de eventos. 

   > [!NOTE]
   > Para criar um novo ou modificar um gatilho de evento de armazenamento existente, a conta do Azure usada para fazer logon no Data Factory e publicar o gatilho de evento de armazenamento deve ter a permissão de controle de acesso baseado em função (RBAC do Azure) na conta de armazenamento. Nenhuma permissão adicional é necessária: a entidade de serviço para o Azure Data Factory _não precisa de_ permissão especial para a conta de armazenamento ou a grade de eventos. Para obter mais informações sobre o controle de acesso, consulte seção [controle de acesso baseado em função](#role-based-access-control) .

1. As propriedades **Caminho do blob começa com**  e **Caminho do blob termina com** permitem especificar os contêineres, as pastas e os nomes de blob para os quais você deseja receber eventos. Seu gatilho de evento de armazenamento exige que pelo menos uma dessas propriedades seja definida. É possível usar diversos padrões tanto para o **caminho do Blob que começa com** como para o **caminho do Blob que termina com propriedades**, conforme mostrado nos exemplos mais adiante neste artigo.

    * **O caminho do blob começa com:** O caminho do blob deve começar com um caminho de pasta. Os valores válidos incluem `2018/` e `2018/april/shoes.csv`. Este campo não poderá ser selecionado se um contêiner não estiver selecionado.
    * **O caminho de blob termina com:** O caminho do blob deve terminar com uma extensão ou nome de arquivo. Os valores válidos incluem `shoes.csv` e `.csv`. Nomes de contêiner e pasta, quando especificados, devem ser separados por um `/blobs/` segmento. Por exemplo, um contêiner chamado 'ordens' pode ter um valor de `/orders/blobs/2018/april/shoes.csv`. Para especificar uma pasta em qualquer contêiner, omita o caractere '/' à esquerda. Por exemplo, `april/shoes.csv` disparará um evento em qualquer arquivo chamado `shoes.csv` na pasta a chamada “abril” em qualquer contêiner.
    * Observe que o caminho de blob **começa com** e **termina com** é o único padrão correspondente permitido no gatilho de evento de armazenamento. Não há suporte para outros tipos de correspondência de curingas para o tipo de gatilho.

1. Selecione se o gatilho responderá a um evento de **Blob criado**, **Blob excluído** ou ambos. No local de armazenamento especificado, cada evento vai disparar os pipelines do Azure Data Factory associados ao gatilho.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image2.png" alt-text="Captura de tela da página de criação do gatilho de evento de armazenamento.":::

1. Selecione se o gatilho ignora ou não BLOBs com zero bytes.

1. Depois de configurar o gatilho, clique em **Avançar: visualização de dados**. Esta tela mostra os BLOBs existentes que correspondem à sua configuração de gatilho de evento de armazenamento. Garanta que você tem filtros específicos. A configuração de filtros muito amplos pode corresponder a um grande número de arquivos criados/excluídos e pode impactar significativamente o seu custo. Depois que as condições de filtro tiverem sido verificadas, clique em **Concluir**.

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image3.png" alt-text="Captura de tela da página de visualização do gatilho de evento de armazenamento.":::

1. Para anexar um pipeline a esse gatilho, vá para a tela do pipeline e clique em **gatilho** e selecione **novo/editar**. Quando a navegação lateral aparecer, clique na lista suspensa **Escolher gatilho...** e selecione o gatilho criado. Clique em **Avançar: Visualização de dados** para confirmar se a configuração está correta. Em seguida, clique em **Avançar** para validar se a visualização de dados está correta.

1. Se o pipeline tiver parâmetros, você poderá especificá-los na navegação lateral do parâmetro de execução do gatilho. O gatilho de evento de armazenamento captura o caminho da pasta e o nome do arquivo do blob nas propriedades `@triggerBody().folderPath` e `@triggerBody().fileName` . Para usar os valores dessas propriedades em um pipeline, é necessário mapear as propriedades para parâmetros de pipeline. Depois de mapear as propriedades para parâmetros, acesse os valores capturados pelo gatilho por meio da expressão `@pipeline().parameters.parameterName` em todo o pipeline. Para obter uma explicação detalhada, consulte [metadados do gatilho de referência em pipelines](how-to-use-trigger-parameterization.md)

    :::image type="content" source="media/how-to-create-event-trigger/event-based-trigger-image4.png" alt-text="Captura de tela de propriedades de mapeamento de gatilho de evento de armazenamento para parâmetros de pipeline.":::

    No exemplo anterior, o gatilho é configurado para ser acionado quando um caminho de blob terminando em. csv é criado na pasta _-teste de evento_ no contêiner de _exemplo-dados_. As propriedades **folderPath** e **fileName** capturam o local do novo blob. Por exemplo, quando MoviesDB.csv é adicionado ao caminho de sample-data/event-testing, `@triggerBody().folderPath` tem um valor de `sample-data/event-testing` e `@triggerBody().fileName` tem um valor de `moviesDB.csv`. Esses valores são mapeados, no exemplo, para os parâmetros de pipeline `sourceFolder` e `sourceFile` , que podem ser usados em todo o pipeline como `@pipeline().parameters.sourceFolder` e, `@pipeline().parameters.sourceFile` respectivamente.

1. Clique em **Concluir** quando terminar.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos de esquema relacionados aos gatilhos de eventos de armazenamento:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **escopo** | A ID do recurso do Azure Resource Manager da Conta de Armazenamento. | String | ID do Azure Resource Manager | Sim |
| **events** | O tipo de eventos que causam o acionamento desse gatilho. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação desses valores. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `/records/blobs/december/` só aciona o gatilho para blobs na pasta `december` no contêiner `records`. | String   | | Forneça um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith` . |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `december/boxes.csv` só aciona o gatilho de blobs denominado `boxes` em uma pasta `december`. | String   | | Você precisa fornecer um valor para pelo menos uma dessas propriedades: `blobPathBeginsWith` ou `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Se os blobs de zero byte dispararão ou não uma execução de pipeline. Por padrão, isso é definido como true. | Boolean | true ou false | Não |

## <a name="examples-of-storage-event-triggers"></a>Exemplos de gatilhos de eventos de armazenamento

Esta seção fornece exemplos de configurações de gatilho de evento de armazenamento.

> [!IMPORTANT]
> Você precisa incluir o segmento `/blobs/` do caminho, como mostrado nos exemplos a seguir, sempre que especificar contêiner e pasta, contêiner e arquivo ou contêiner, pasta e arquivo. Para **blobPathBeginsWith**, a interface do usuário do Data Factory adicionará automaticamente `/blobs/` entre o nome da pasta e do contêiner no JSON de gatilho.

| Propriedade | Exemplo | Descrição |
|---|---|---|
| **O caminho de blob começa com** | `/containername/` | Recebe eventos para qualquer blob no contêiner. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/` | Recebe eventos para todos os blobs no contêiner `containername` e na pasta `foldername`. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/subfoldername/` | Você também pode fazer referência a uma subpasta. |
| **O caminho de blob começa com** | `/containername/blobs/foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` no contêiner `containername`. |
| **O caminho de blob termina com** | `file.txt` | Recebe eventos para um blob denominado `file.txt` em qualquer caminho. |
| **O caminho de blob termina com** | `/containername/blobs/file.txt` | Recebe eventos para um blob denominado `file.txt` no contêiner `containername`. |
| **O caminho de blob termina com** | `foldername/file.txt` | Recebe eventos para um blob denominado `file.txt` na pasta `foldername` em qualquer contêiner. |

## <a name="role-based-access-control"></a>Controle de acesso baseado em funções

Azure Data Factory usa o controle de acesso baseado em função do Azure (RBAC do Azure) para garantir que o acesso não autorizado para escutar, assinar atualizações do e disparar pipelines vinculados a eventos de BLOB, seja estritamente proibido.

* Para criar um novo ou atualizar um gatilho de evento de armazenamento existente com êxito, a conta do Azure conectada ao Data Factory precisa ter acesso apropriado à conta de armazenamento relevante. Caso contrário, a operação com falha com _acesso negado_.
* Data Factory não precisa de permissão especial para sua grade de eventos e você _não_ precisa atribuir permissão de RBAC especial para data Factory entidade de serviço para a operação.

Qualquer uma das seguintes configurações do RBAC funciona para o gatilho de evento de armazenamento:

* Função de proprietário para a conta de armazenamento
* Função de colaborador para a conta de armazenamento
* Permissão _Microsoft. EventGrid/EventSubscriptions/Write_ para a conta de armazenamento _/subscriptions/# # # #/resourceGroups/# # # #/Providers/Microsoft.Storage/storageAccounts/storageAccountName_

Para entender como Azure Data Factory fornece as duas promessas, vamos voltar uma etapa e fazer uma espiada por trás da cena. Aqui estão os fluxos de trabalho de alto nível para integração entre Data Factory, armazenamento e grade de eventos.

### <a name="create-a-new-storage-event-trigger"></a>Criar um novo gatilho de evento de armazenamento

Este fluxo de trabalho de alto nível descreve como Azure Data Factory interage com a grade de eventos para criar um gatilho de evento de armazenamento

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-5-create-subscription.png" alt-text="Fluxo de trabalho da criação do gatilho de evento de armazenamento.":::

Dois tempos de chamada perceptíveis dos fluxos de trabalho:

* Azure Data Factory _não_ faz contato direto com a conta de armazenamento. A solicitação para criar uma assinatura é, em vez disso, retransmitida e processada pela grade de eventos. Portanto, Data Factory não precisa de permissão para a conta de armazenamento para esta etapa.

* O controle de acesso e a verificação de permissão acontecem no lado Azure Data Factory. Antes que o ADF envie uma solicitação para assinar o evento de armazenamento, ele verifica a permissão para o usuário. Mais especificamente, ele verifica se a conta do Azure entrou e tentando criar o gatilho de evento de armazenamento tem acesso apropriado à conta de armazenamento relevante. Se a verificação de permissão falhar, a criação do gatilho também falhará.

### <a name="storage-event-trigger-data-factory-pipeline-run"></a>Gatilho de evento de armazenamento Data Factory execução de pipeline

Esses fluxos de trabalho de alto nível descrevem como o evento de armazenamento dispara o pipeline de execução por meio da grade de eventos

:::image type="content" source="media/how-to-create-event-trigger/storage-event-trigger-6-trigger-pipeline.png" alt-text="Fluxo de trabalho do evento de armazenamento que dispara execuções de pipeline.":::

Quando se trata de pipeline de disparo de eventos em Data Factory, três chamadas perceptíveis no fluxo de trabalho:

* A grade de eventos usa um modelo de push que retransmite a mensagem assim que possível quando o armazenamento descarta a mensagem no sistema. Isso é diferente do sistema de mensagens, como Kafka, em que um sistema de pull é usado.
* O gatilho de evento em Azure Data Factory serve como um ouvinte ativo para a mensagem de entrada e aciona corretamente o pipeline associado.
* O gatilho de evento de armazenamento não faz contato direto com a conta de armazenamento

  * Dito isso, se você tiver uma cópia ou outra atividade dentro do pipeline para processar os dados na conta de armazenamento, Data Factory fará contato direto com o armazenamento, usando as credenciais armazenadas no serviço vinculado. Verifique se o serviço vinculado está configurado adequadamente
  * No entanto, se você não fizer nenhuma referência à conta de armazenamento no pipeline, não será necessário conceder permissão para Data Factory para acessar a conta de armazenamento

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
* Saiba como referenciar metadados de gatilho no pipeline, consulte [metadados de gatilho de referência em execuções de pipeline](how-to-use-trigger-parameterization.md)
