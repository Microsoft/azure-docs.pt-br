---
title: Início Rápido – Criar uma instância de Gerenciamento de API do Azure
description: Crie uma instância de serviço do Gerenciamento de API do Azure usando o portal do Azure.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708199"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>'Início Rápido: Criar uma instância de serviço do Gerenciamento de API do Azure usando o portal do Azure

O APIM (Gerenciamento de API) do Azure ajuda as organizações a publicar APIs para parceiros externos e desenvolvedores internos a fim de liberar o potencial de seus dados e serviços. O Gerenciamento de API fornece as competências essenciais para garantir um programa de API de sucesso através do envolvimento do desenvolvedor, ideias de negócios, análises, segurança e proteção. O APIM permite que você crie e gerencie gateways de API modernos para serviços de back-end hospedados em qualquer lugar. Para obter mais informações, confira a [Visão geral](api-management-key-concepts.md).

Este guia de início rápido descreve as etapas para criar uma nova instância de Gerenciamento de API usando o portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Instância de Gerenciamento de API

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-new-service"></a>Criar um novo serviço

1. No menu do portal do Azure, selecione **Criar um recurso**. Você também pode selecionar **Criar um recurso** na **Página Inicial** do Azure. 
   
   Selecione :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Criar um Recurso":::

   
1. Na página **Novo**, selecione **Integração** > **Gerenciamento de API**.

   Nova instância de Gerenciamento de API do Azure
   
1. Na página **Serviço de Gerenciamento de API**, insira as configurações.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Nova instância":::
   
   | Configuração                 | Descrição   |                                                                     
   |-------------------------|-----------------------------------------------|
   | nome - **                | Um nome exclusivo para o serviço de Gerenciamento de API. O nome não poderá ser alterado posteriormente. O nome do serviço refere-se ao serviço e ao recurso do Azure correspondente. <br/> O nome do serviço é usado para gerar um nome de domínio padrão: *\<name\>.azure-api.net.* Se você quiser usar um nome de domínio personalizado, consulte [Configurar um domínio personalizado](configure-custom-domain.md). |
   | **--subscription**:          | A assinatura sob a qual essa nova instância de serviço será criada.   |
   | **Grupo de recursos**      |  Selecione um grupo de recursos novo ou existente. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. |
   | **Localidade**          | Selecione uma região geográfica perto dos locais do serviço de Gerenciamento de API disponíveis. | 
   | **Nome da organização**   | O nome da sua organização. Esse nome é usado em vários locais, incluindo o título do portal do desenvolvedor e o remetente de emails de notificação. |                                                         
   | **Email do administrador** | O endereço de email ao qual todas as notificações de **Gerenciamento de API** serão enviadas.   |  
   | **Tipo de preços**        | Selecione a camada **Desenvolvedor** para avaliar o serviço. Essa camada não é para uso em produção. Para obter mais informações sobre a colocação em escala das camadas de Gerenciamento de API, confira [atualizar e dimensionar](upgrade-and-scale.md). |

3. Selecione **Criar**.

    > [!TIP]
    > A criação e a ativação de um serviço de Gerenciamento de API nessa camada leva de 30 a 40 minutos. Selecionar **Fixar ao painel** torna a localização de um serviço recém-criado mais fácil.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Examine as propriedades do seu serviço na página **Visão geral**.

   Instância de Gerenciamento de API

Quando sua instância de serviço de Gerenciamento de API estiver online, você estará pronto para usá-la. Comece com o tutorial para [importar e publicar sua primeira API](import-and-publish.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá remover o grupo de recursos e todos os recursos relacionados seguindo estas etapas:

1. No portal do Azure, procure por **Grupos de recursos** e selecione essa opção. Você também pode selecionar **Grupos de recursos** na **Página Inicial**. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Navegação de grupo de recursos":::

1. Na página **Grupos de recursos**, selecione seu grupo de recursos.

   Selecione :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Grupo de Recursos":::.

1. Na página Grupo de recursos, selecione **Excluir grupo de recursos**. 
   
1. Digite o nome do seu grupo de recursos e depois selecione **Excluir**.

   Excluir grupo de recursos

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Importar e publicar sua primeira API](import-and-publish.md)
