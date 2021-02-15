---
title: Configurar Postman para chamadas de API de REST dos Serviços de Mídia do Azure
description: Este artigo descreve como configurar o postmaster para chamadas à API REST dos serviços de mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: a95e6c9554c96a530b4c92832a57b4febda86002
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695680"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Configurar o postmaster para chamadas da API REST do Media Services v2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Este tutorial mostra como configurar **Postman** para que ele possa ser usado para chamar APIs de REST dos Serviços de Mídia do Azure (AMS). O tutorial mostra como importar arquivos do ambiente e da coleção no **Postman**. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs de REST dos Serviços de Mídia do Azure (AMS). O arquivo de ambiente contém variáveis que são usadas pela coleção.

Este ambiente e a coleção são usados em artigos que mostram como realizar várias tarefas com APIs de REST dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o cliente REST do [Postman](https://www.getpostman.com/) para executar as APIs de REST mostradas em alguns dos tutoriais do REST AMS. 

    Estamos usando o **Postman** mas qualquer ferramenta REST seria adequada. As outras alternativas são: **Visual Studio Code** com o plug-in do REST ou **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurar o ambiente 

1. Crie um arquivo. JSON contendo as variáveis de ambiente usadas nos tutoriais AMS. Nomeie o arquivo (por exemplo, **AzureMediaServices.postman_environment.json**). Abra o arquivo e cole o código que define o ambiente do Postman nesta [listagem de código](postman-environment.md). 
2. Abra o aplicativo **Postman**.
3. À direita da tela, selecione a opção **Gerenciar ambiente**.

    ![Captura de tela mostra a opção gerenciar ambiente selecionada.](./media/media-services-rest-upload-files/postman-create-env.png)
4. Na caixa de diálogo **Gerenciar ambiente**, clique em **Importar**.
5. Navegue e selecione o arquivo **AzureMediaServices.postman_environment.json**.
6. O ambiente **AzureMedia** é adicionado.
7. Feche a caixa de diálogo.
8. Selecione o ambiente do **AzureMedia**.

    ![Captura de tela mostra o ambiente AzureMedia selecionado.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurar a coleção

1. Crie um arquivo. JSON contendo a coleção do **Postman** com todas as operações que são necessárias para carregar um arquivo nos serviços de mídia. Nomeie o arquivo (por exemplo, **AzureMediaServicesOperations.postman_collection.json**). Abra o arquivo e cole o código que define a coleção do **Postman** nesta [listagem de código](postman-collection.md).
2. Clique em **Importar** para importar o arquivo de coleção.
3. Escolha o arquivo **AzureMediaServicesOperations.postman_collection.json**.

    ![Captura de tela mostra a caixa de diálogo importar com escolher arquivos selecionados.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Próximas etapas

Confira o artigo sobre como [carregar ativos](media-services-rest-upload-files.md).  
