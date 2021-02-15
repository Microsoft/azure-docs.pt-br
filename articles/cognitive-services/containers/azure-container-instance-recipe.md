---
title: Receita da instância de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar contêineres de serviços cognitivas na instância de contêiner do Azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704819"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implantar e executar em Instância de Contêiner do Azure

Com as etapas a seguir, dimensione facilmente os aplicativos de serviços cognitivas do Azure na nuvem com as [instâncias de contêiner](../../container-instances/index.yml)do Azure. A criação de contêineres ajuda você a se concentrar em criar seus aplicativos em vez de gerenciar a infraestrutura. Para obter mais informações sobre como usar contêineres, consulte [recursos e benefícios](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Pré-requisitos

A receita funciona com qualquer contêiner de serviços cognitivas. O recurso de serviço cognitiva deve ser criado antes de usar a receita. Cada serviço cognitiva que dá suporte a contêineres tem um artigo "como instalar" para instalar e configurar o serviço para um contêiner. Alguns serviços exigem um arquivo ou conjunto de arquivos como entrada para o contêiner, é importante que você entenda e tenha usado o contêiner com êxito antes de usar essa solução.

* Um recurso do Azure para o serviço cognitiva do Azure que você está usando.
* URL do **ponto de extremidade** de serviço cognitiva-examine o "como instalar" do seu serviço específico para o contêiner, para descobrir onde a URL do ponto de extremidade está dentro do portal do Azure e a aparência do exemplo correto da URL. O formato exato pode mudar de serviço para serviço.
* **Chave** de serviço cognitiva-as chaves estão na página **chaves** do recurso do Azure. Você precisa apenas de uma das duas chaves. A chave é uma cadeia de caracteres de 32 alfanuméricos.

* Um único contêiner de serviços cognitivas no host local (seu computador). Verifique se você pode:
  * Puxe a imagem com um `docker pull` comando.
  * Execute o contêiner local com êxito com todas as definições de configuração necessárias com um `docker run` comando.
  * Chame o ponto de extremidade do contêiner, obtendo uma resposta de HTTP 2xx e uma resposta JSON de volta.

Todas as variáveis entre colchetes angulares, `<>` precisam ser substituídas por seus próprios valores. Essa substituição inclui os colchetes angulares.

> [!IMPORTANT]
> O contêiner LUIS requer um `.gz` arquivo de modelo que é extraído no tempo de execução. O contêiner deve ser capaz de acessar esse arquivo de modelo por meio de uma montagem de volume da instância de contêiner. Para carregar um arquivo de modelo, siga estas etapas:
> 1. [Crie um compartilhamento de arquivos do Azure](../../storage/files/storage-how-to-create-file-share.md). Anote o nome da conta de armazenamento do Azure, a chave e o nome do compartilhamento de arquivos, pois você precisará deles mais tarde.
> 2. [exporte seu modelo Luis (aplicativo empacotado) do portal do Luis](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Na portal do Azure, navegue até a página **visão geral** do recurso de conta de armazenamento e selecione **compartilhamentos de arquivos**. 
> 4. Selecione o nome do compartilhamento de arquivos que você criou recentemente e, em seguida, selecione **carregar**. Em seguida, carregue o aplicativo empacotado. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Usar a instância de contêiner

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Selecione a **visão geral** e copie o endereço IP. Será um endereço IP numérico, como `55.55.55.55` .
1. Abra uma nova guia do navegador e use o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Você verá o home page do contêiner, informando que o contêiner está em execução.

    ![Home page do contêiner](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Selecione **Descrição da API de serviço** para exibir a página do Swagger para o contêiner.

1. Selecione qualquer uma das APIs **post** e selecione **experimentar**.  Os parâmetros são exibidos, incluindo a entrada. Preencha os parâmetros.

1. Selecione **executar** para enviar a solicitação para a instância de contêiner.

    Você criou e usou contêineres de serviços cognitivas na instância de contêiner do Azure.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Se você estiver executando o Análise de Texto para o contêiner de integridade, use a seguinte URL para enviar consultas: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
