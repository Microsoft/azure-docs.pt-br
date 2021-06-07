---
title: Tutorial – Importar e publicar sua primeira API no Gerenciamento de API do Azure
description: Neste tutorial, você importa uma API de Especificação OpenAPI para o Gerenciamento de API do Azure e testa a API no portal do Azure.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626931"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Tutorial: Importar e publicar sua primeira API

Este tutorial mostra como importar uma API de back-end da Especificação OpenAPI em formato JSON para o Gerenciamento de API do Azure. A Microsoft fornece a API de back-end usada neste exemplo e a hospeda no Azure em [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Após importar a API de back-end para o Gerenciamento de API, sua API do Gerenciamento de API se tornará uma fachada para a API de back-end. Você pode personalizar a fachada de acordo com suas necessidades no Gerenciamento de API sem mexer na API de back-end. Para obter mais informações, consulte [Transformar e proteger sua API](transform-api.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Importar uma API para o Gerenciamento de API
> * Testar a API no Portal do Azure

Após a importação, você pode gerenciar a API no portal do Azure.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nova API no Gerenciamento de API":::

## <a name="prerequisites"></a>Pré-requisitos

- Noções básicas sobre a [terminologia do Gerenciamento de API do Azure](api-management-terminology.md).
- [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Importar e publicar uma API de back-end

Esta seção mostra como importar e publicar uma API de back-end da Especificação OpenAPI.

1. No painel de navegação esquerdo de sua instância do Gerenciamento de API, selecione **APIs**.
1. Selecione o bloco **OpenAPI**.
1. Na janela **Criar a partir da especificação de OpenAPI**, selecione **Completo**.
1. Insira os valores da tabela a seguir. Depois, selecione **Criar** para criar a API.

   Você pode definir os valores da API durante a criação ou mais tarde, acessando a guia **Configurações**.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Criar uma API":::


   |Configuração|Valor|Descrição|
   |-------|-----|-----------|
   |**Especificação OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|O serviço que implementa a API. O Gerenciamento de API envia as solicitações para esse endereço.|
   |**Nome de exibição**|Depois de inserir a URL de serviço anterior, o Gerenciamento de API preencherá esse campo com base no JSON.|O nome exibido no [portal do desenvolvedor](api-management-howto-developer-portal.md).|
   |**Nome**|Depois de inserir a URL de serviço anterior, o Gerenciamento de API preencherá esse campo com base no JSON.|Um nome exclusivo para a API.|
   |**Descrição**|Depois de inserir a URL de serviço anterior, o Gerenciamento de API preencherá esse campo com base no JSON.|Uma descrição opcional da API.|
   |**Esquema de URL**|**HTTPS**|Quais protocolos podem acessar a API.|
   |**Sufixo da URL da API**|*conference*|O sufixo acrescentado à URL base do serviço de Gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo; assim, o sufixo deve ser único para cada API para um editor específico.|
   |**Marcas**| |As marcas para organizar APIs para pesquisa, agrupamento ou filtragem.|
   |**Produtos**|**Ilimitado**|Associação de uma ou mais APIs. Cada instância de Gerenciamento de API é fornecida com dois produtos função Web: **Starter** e **Ilimitado**. Você publica uma API associando-a a um produto, **Ilimitado** neste exemplo.<br/><br/> É possível incluir várias APIs em um produto e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. Para adicionar essa API a outro produto, digite ou selecione o nome dele. Repita essa etapa para adicionar a API a vários produtos. Você também pode adicionar APIs aos produtos mais tarde por meio da página **Configurações**.<br/><br/>  Para obter mais informações sobre os produtos, confira [Criar e publicar um produto](api-management-howto-add-products.md).|
   |**Gateways**|**Gerenciado**|Gateways de API que expõem a API. Esse campo está disponível somente nos serviços dos níveis **Desenvolvedor** e **Premium**.<br/><br/>**Gerenciado** indica o gateway interno do serviço de Gerenciamento de API, que é hospedado pela Microsoft no Azure. [Gateways auto-hospedados](self-hosted-gateway-overview.md) estão disponíveis apenas nas camadas de serviço Premium e Desenvolvedor. Você pode implantá-los localmente ou em outras nuvens.<br/><br/> Se não houver gateway selecionado, a API não estará disponível e suas solicitações de API não terão sucesso.|
   |**Controlar a versão desta API?**|Selecionar ou cancelar seleção|Para obter mais informações, consulte [Publicar várias versões de sua API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Para publicar a API para consumidores de API, você deve associá-la a um produto.

2. Selecione **Criar**.

Se você tiver problemas com a importação de uma definição de API, confira a [lista de problemas e restrições conhecidos](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testar a nova API no portal do Azure

Você pode chamar operações de API diretamente do portal do Azure, que oferece uma forma fácil de exibir e testar as operações.

1. No painel de navegação esquerdo de sua instância do Gerenciamento de API, selecione **APIs** > **Importar a API Conferência de Demonstração**.
1. Selecione a guia **Testar** e, em seguida, **GetSpeakers**. A página mostrará **Parâmetros de consulta** e **Cabeçalhos**, se houver. O **Ocp-Apim-Subscription-Key** é preenchido automaticamente para a chave de assinatura associada a esta API.
1. Selecione **Enviar**.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Testar API no portal do Azure":::

   O back-end responde com **200 OK** e alguns dados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Importar sua primeira API
> * Testar a API no Portal do Azure

Vá para o próximo tutorial para aprender a criar e publicar um produto:

> [!div class="nextstepaction"]
> [Criar e publicar um produto](api-management-howto-add-products.md)
