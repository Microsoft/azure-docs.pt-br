---
title: Criar disparadores de eventos personalizados no Azure Data Factory
description: Saiba como criar um gatilho personalizado no Azure Data Factory que executa um pipeline em resposta a um evento personalizado publicado na grade de eventos.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785641"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Criar um gatilho que executa um pipeline em resposta a um evento personalizado (versão prévia)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os gatilhos de evento personalizados que você pode criar em seus pipelines de Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Cenários de integração de dados geralmente exigem Data Factory clientes para disparar pipelines com base em determinados eventos ocorrendo. Data Factory integração nativa com a [grade de eventos do Azure](https://azure.microsoft.com/services/event-grid/) agora aborda [eventos personalizados](../event-grid/custom-topics.md): os clientes enviam eventos arbitrários para um tópico da grade de eventos e data Factory assinam e escutam o tópico e dispara os pipelines de acordo.

> [!NOTE]
> A integração descrita neste artigo depende na [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se a assinatura está registrada no provedor de recursos da Grade de Eventos. Para obter mais informações, confira [Provedores e tipos de recursos](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Você deve ser capaz de fazer a ação *Microsoft. EventGrid/eventSubscriptions/**. Esta ação faz parte da função interna de colaborador do EventGrid EventSubscription.

Além disso, combinando os parâmetros de pipeline e o gatilho de evento personalizado, os clientes podem analisar e referenciar a carga de _dados_ personalizada em execuções de pipeline. o campo de _dados_ na carga do evento personalizado é uma estrutura de valor de chave JSON de forma livre, dando aos clientes o controle máximo sobre as execuções de pipeline acionadas por evento.

> [!IMPORTANT]
> A cada tanta frequência, uma chave referenciada na parametrização pode estar ausente na carga do evento personalizado. A _execução do gatilho_ falhará com um erro, informando que a expressão não pode ser avaliada porque o _KeyName_ da propriedade não existe. __Nenhuma__ _execução de pipeline_ será disparada pelo evento.

## <a name="setup-event-grid-custom-topic"></a>Configurar tópico personalizado da grade de eventos

Para usar o gatilho de evento personalizado no Data Factory, _primeiro_ você precisa configurar um [tópico personalizado na grade de eventos](../event-grid/custom-topics.md). O fluxo de trabalho é diferente do gatilho de evento de armazenamento, em que Data Factory irá configurar o tópico para você. Aqui, você precisa navegar na grade de eventos do Azure e criar o tópico por conta própria. Para obter mais informações sobre como criar o tópico personalizado, consulte [tutoriais do portal](../event-grid/custom-topics.md#azure-portal-tutorials) de grade de eventos do Azure e [tutoriais da CLI](../event-grid/custom-topics.md#azure-cli-tutorials)

As fábricas de dados esperam que os eventos sigam o [esquema de evento da grade de eventos](../event-grid/event-schema.md). Verifique se as cargas de evento têm os campos a seguir.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>IU do Data Factory

Esta seção mostra como criar um gatilho de evento de armazenamento dentro da interface do usuário do Azure Data Factory.

1. Alterne para a guia **Editar** , mostrada com um símbolo de lápis.

1. Selecione **gatilho** no menu e, em seguida, selecione **novo/editar**.

1. Na página **Adicionar gatilhos** , selecione **escolher gatilho...** e, em seguida, selecione **+ novo**.

1. Selecionar **eventos personalizados** de tipo de gatilho

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Captura de tela da página autor para criar um novo gatilho de evento personalizado na interface do usuário Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Selecione o tópico personalizado na lista suspensa assinatura do Azure ou insira manualmente o escopo do tópico de evento.

   > [!NOTE]
   > Para criar um novo ou modificar um gatilho de evento personalizado existente, a conta do Azure usada para fazer logon no Data Factory e publicar o gatilho de evento de armazenamento deve ter a permissão de controle de acesso baseado em função (RBAC do Azure) no tópico. Nenhuma permissão adicional é necessária: a entidade de serviço para o Azure Data Factory _não precisa de_ permissão especial para a grade de eventos. Para obter mais informações sobre o controle de acesso, consulte seção [controle de acesso baseado em função](#role-based-access-control) .

1. O **assunto começa com** e o **assunto termina com** as propriedades permitem filtrar eventos para os quais você deseja disparar o pipeline. Ambas as propriedades são opcionais.

1. Use **+ novo** para adicionar os **tipos de eventos** que você deseja filtrar. Funcionário de gatilho de evento personalizado uma relação OR para a lista: se um evento personalizado tiver uma propriedade _EventType_ que corresponda a qualquer listada aqui, ele disparará uma execução de pipeline. O tipo de evento não diferencia maiúsculas de minúsculas. Por exemplo, na captura de tela abaixo, o gatilho corresponde a todos os eventos _copycompleted_ ou _copysucceeded_ com o assunto começa com _fábricas_

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Captura de tela da página Editar gatilho para explicar os tipos de evento e a filtragem de assunto na interface do usuário Data Factory.":::

1. O gatilho de evento personalizado pode analisar e enviar uma carga de _dados_ personalizada para seu pipeline. Primeiro, crie os parâmetros de pipeline e preencha os valores na página **parâmetros** . Use Format **@triggerBody (). Event. Data._keyName_** para analisar a carga de dados e passar valores para parâmetros de pipeline. Para obter uma explicação detalhada, consulte [metadados do gatilho de referência em pipelines](how-to-use-trigger-parameterization.md) e [variáveis do sistema no gatilho de evento personalizado](control-flow-system-variables.md#custom-event-trigger-scope)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Captura de tela da configuração de parâmetros de pipeline.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Captura de tela da página de parâmetros para referenciar a carga de dados no evento personalizado.":::

1. Clique em **OK** quando terminar.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos de esquema relacionados aos disparadores de eventos personalizados:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Necessário** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **escopo** | A ID de recurso Azure Resource Manager do tópico da grade de eventos. | String | ID do Azure Resource Manager | Sim |
| **events** | O tipo de eventos que causam o acionamento desse gatilho. | Matriz de cadeia de caracteres    |  | Sim, pelo menos um valor é esperado |
| **subjectBeginsWith** | O campo assunto deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, `factories` só dispara o gatilho para o assunto do evento a partir de `factories` . | String   | | Não |
| **subjectEndsWith** | O campo de assunto deve terminar com o padrão fornecido para o gatilho ser acionado. | String   | | Não |

## <a name="role-based-access-control"></a>Controle de acesso baseado em funções

Azure Data Factory usa o controle de acesso baseado em função do Azure (RBAC do Azure) para garantir que o acesso não autorizado para escutar, assinar atualizações do e disparar pipelines vinculados a eventos personalizados seja estritamente proibido.

* Para criar um novo ou atualizar um gatilho de evento personalizado existente com êxito, a conta do Azure conectada ao Data Factory precisa ter acesso apropriado à conta de armazenamento relevante. Caso contrário, a operação com falha com _acesso negado_.
* Data Factory não precisa de permissão especial para sua grade de eventos e você _não_ precisa atribuir permissão do RBAC do Azure especial para data Factory entidade de serviço para a operação.

Especificamente, o cliente precisa da permissão _Microsoft. EventGrid/EventSubscriptions/Write_ em _/subscriptions/# # # #/resourceGroups//# # # #/Providers/Microsoft.EventGrid/topics/someTopics_

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
* Saiba como referenciar metadados de gatilho no pipeline, consulte [metadados de gatilho de referência em execuções de pipeline](how-to-use-trigger-parameterization.md)
