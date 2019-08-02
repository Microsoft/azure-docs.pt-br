---
title: Receita de instância de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Saiba como implantar contêineres de serviços Cognitivos na instância de contêiner do Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 288894705e1108d6dd511b60cd2bc3bcee4c6d41
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704364"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Implante e execute o contêiner na instância de contêiner do Azure

Com as etapas a seguir, dimensionem aplicativos de serviços Cognitivos do Azure na nuvem com facilidade com o Azure [instância de contêiner](https://docs.microsoft.com/azure/container-instances/). Essa ajuda você a focar na criação de seus aplicativos em vez de gerenciar a infraestrutura.

## <a name="prerequisites"></a>Pré-requisitos

Essa solução funciona com qualquer contêiner de serviços Cognitivos. O recurso de serviço cognitivo deve ser criado no portal do Azure antes de usar essa receita. Cada serviço cognitivo que dá suporte a contêineres tem um documento de "Como instalar o" especificamente para instalar e configurar o serviço para um contêiner. Como alguns serviços exigem um arquivo ou conjunto de arquivos como entrada para o contêiner, é importante que você compreenda e usou o contêiner com êxito antes de usar essa solução.

* Um recurso de serviço cognitivo, criado no portal do Azure.
* Serviço cognitivo **URL de ponto de extremidade** -examine seu do serviço específico "como instalar o" para o contêiner, para localizar onde a URL de ponto de extremidade é de dentro do portal do Azure e o que um exemplo correto da URL é semelhante. O formato exato pode alterar o serviço.
* Serviço cognitivo **chave** -as chaves são sobre o **chaves** página para o recurso do Azure. Você precisa apenas de uma das duas chaves. A chave é uma cadeia de caracteres de 32 caracteres alfanuméricos.
* Um único cognitivas contêiner de serviços em seu host local (computador). Certifique-se de que você pode:
  * Baixar a imagem com um `docker pull` comando.
  * Execute o contêiner local com êxito com todas as definições de configuração necessárias, com um `docker run` comando.
  * Chame o ponto de extremidade do contêiner, obter uma resposta de 2xx e uma resposta JSON de volta.

Todas as variáveis entre colchetes angulares, `<>`, precisam ser substituídos por seus próprios valores. Essa substituição inclui os colchetes angulares.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Usar a instância de contêiner

1. Selecione o **visão geral** e copie o endereço IP. Ele será um endereço IP numérico como `55.55.55.55`.
1. Abra uma nova guia do navegador e usar o endereço IP, por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Você verá homepage do contêiner, permitindo que você saiba que o contêiner está sendo executado.

1. Selecione **descrição do serviço API** para exibir a página de swagger para o contêiner.

1. Selecione qualquer uma da **POST** APIs e selecione **experimentá-lo**.  Os parâmetros são exibidos, incluindo a entrada. Preencha os parâmetros.

1. Selecione **Execute** para enviar a solicitação à instância de contêiner.

    Você criou e usado os contêineres de serviços Cognitivos na instância de contêiner do Azure com êxito.
