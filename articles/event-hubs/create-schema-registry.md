---
title: Criar um registro de esquema de hubs de eventos do Azure
description: Este artigo mostra como criar um registro de esquema em um namespace de hubs de eventos do Azure.
ms.topic: how-to
ms.date: 12/03/2020
ms.custom: references_regions
ms.openlocfilehash: 45791cd69772be97ca6768184ed17179e04ad9dc
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576801"
---
# <a name="create-an-azure-event-hubs-schema-registry-preview"></a>Criar um registro de esquema de hubs de eventos do Azure (versão prévia)
Este artigo mostra como criar um grupo de esquema com esquemas em um registro de esquema hospedado pelos hubs de eventos do Azure. Para obter uma visão geral do recurso de registro de esquema dos hubs de eventos do Azure, consulte [registro de esquema do Azure em hubs de eventos](schema-registry-overview.md).

> [!NOTE]
> - O recurso de **Registro de Esquema** está em **versão prévia** no momento e não é recomendado para cargas de trabalho de produção.
> - O recurso está disponível somente nas camadas de serviço **Standard** e **Dedicado**, e não na **Básica**.
> - Se o Hub de eventos estiver em uma **rede virtual**, você não poderá criar esquemas no portal do Azure, a menos que acesse o portal de uma VM na mesma rede virtual. 

## <a name="prerequisites"></a>Pré-requisitos
[Crie um namespace de hubs de eventos](event-hubs-create.md#create-an-event-hubs-namespace). Você também pode usar um namespace existente. 

## <a name="create-a-schema-group"></a>Criar um grupo de esquema
1. Navegue até a página de **namespace de hubs de eventos** . 
1. Selecione **registro de esquema** no menu à esquerda. Para criar um grupo de esquemas, selecione **+ grupo de esquema** na barra de ferramentas. 

    :::image type="content" source="./media/create-schema-registry/namespace-page.png" alt-text="Página de registro de esquema":::
1. Na página **Criar grupo de esquemas** , siga estas etapas:
    1. Insira um **nome** para o grupo de esquema.
    1. Para **tipo de serialização**, escolha um formato de serialização que se aplica a todos os esquemas no grupo de esquemas. Atualmente, **Avro** é o único tipo com suporte, portanto, selecione **Avro**. 
    1. Selecione um **modo de compatibilidade** para todos os esquemas no grupo. Para os modos de compatibilidade **Avro**, Forward e Backward têm suporte. 
    1. Em seguida, selecione **criar** para criar o grupo de esquema. 
1. Selecione o nome do **grupo de esquema** na lista de grupos de esquemas.

    :::image type="content" source="./media/create-schema-registry/select-schema-group.png" alt-text="Selecione seu grupo de esquema na lista":::    
1. Você verá a página **grupo de esquemas** para o grupo.

    :::image type="content" source="./media/create-schema-registry/schema-group-page.png" alt-text="Página grupo de esquemas":::
    

## <a name="add-a-schema-to-the-schema-group"></a>Adicionar um esquema ao grupo de esquemas
Nesta seção, você adiciona um esquema ao grupo de esquema usando o portal do Azure. 

1. Na página **grupo de esquemas** , selecione **+ esquema** na barra de ferramentas. 
1. Na página **criar esquema** , siga estas etapas:
    1. Insira um **nome** para o esquema.
    1. Insira o **esquema** a seguir na caixa de texto. Você também pode selecionar arquivo com o esquema.
    
        ```json
        {
            "type": "record",
            "name": "AvroUser",
            "namespace": "com.azure.schemaregistry.samples",
            "fields": [
                {
                    "name": "name",
                    "type": "string"
                },
                {
                    "name": "favoriteNumber",
                    "type": "int"
                }
            ]
        }
        ```
    1. Selecione **Criar**. 
1. Selecione o **esquema** na lista de esquemas. 

    :::image type="content" source="./media/create-schema-registry/select-schema.png" alt-text="Selecione um esquema":::
1. Você verá a página **visão geral do esquema** para o esquema. 

    :::image type="content" source="./media/create-schema-registry/schema-overview-page.png" alt-text="Página Visão geral do esquema":::    
1. Se houver várias versões de um esquema, você as verá na lista suspensa **versões** . Selecione uma versão para alternar para esse esquema de versão. 

## <a name="create-a-new-version-of-schema"></a>Criar uma nova versão do esquema

1. Atualize o esquema na caixa de texto e selecione **validar**. No exemplo a seguir, um novo campo foi `id` adicionado ao esquema. 

    :::image type="content" source="./media/create-schema-registry/update-schema.png" alt-text="Atualizar esquema":::    
    
1. Examine o status de validação e as alterações e selecione **salvar**. 

    :::image type="content" source="./media/create-schema-registry/compare-save-schema.png" alt-text="Examinar o status de validação, alterações e salvar":::     
1. Você verá que `2` está selecionado para a **versão** na página **visão geral do esquema** . 

    :::image type="content" source="./media/create-schema-registry/new-version.png" alt-text="Nova versão do esquema":::    
1. Selecione `1` para ver a versão 1 do esquema. 

    :::image type="content" source="./media/create-schema-registry/select-version.png" alt-text="Selecionar versão":::    


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o registro de esquema, consulte [registro de esquema do Azure em hubs de eventos](schema-registry-overview.md).

