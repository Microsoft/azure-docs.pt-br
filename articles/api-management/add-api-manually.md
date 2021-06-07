---
title: Adicionar uma API manualmente usando o Portal do Azure | Microsoft Docs
description: Este tutorial mostra como usar o APIM (Gerenciamento de API) para adicionar uma API manualmente.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183800"
---
# <a name="add-an-api-manually"></a>Adicionar uma API manualmente

As etapas neste artigo mostram como usar o Portal do Azure para adicionar uma API manualmente à instância de APIM (Gerenciamento de API). Um cenário comum quando você deseja criar uma API em branco e defini-la manualmente é quando deseja simular a API. Para obter detalhes sobre a simulação de uma API, consulte [Simular respostas de API](mock-api-responses.md).

Se você deseja importar uma API existente, consulte a seção de [tópicos relacionados](#related-topics).

Neste artigo, criamos uma API em branco e especificamos [httpbin.org](https://httpbin.org) (um serviço de teste público) como a API de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Criar uma API

1. Navegue até o serviço de Gerenciamento de API no portal do Azure e selecione **APIs** no menu.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em Branco** na lista.  
    ![API em Branco](media/add-api-manually/blank-api.png)  
4. Insira as configurações para a API. As configurações são explicadas no tutorial [Importar e publicar sua primeira API](import-and-publish.md#import-and-publish-a-backend-api).
5. Selecione **Criar**.

Agora você não tem nenhuma operação no Gerenciamento de API mapeada para as operações em sua API de back-end. Se você chamar uma operação que é exposta por meio de back-end, mas não por meio do Gerenciamento de API, receberá um **404**.

>[!NOTE] 
> Por padrão, quando você adicionar uma API, mesmo se ela estiver conectada a algum serviço de back-end, o APIM não vai expor nenhuma operação até que você as coloque na lista de permissões. Para permitir uma operação do serviço de back-end, crie uma operação de APIM que mapeia para a operação de back-end.

## <a name="add-and-test-an-operation"></a>Adicionar e testar uma operação

Esta seção mostra como adicionar uma operação "/get" para mapeá-la para a operação de back-end "http://httpbin.org/get".

### <a name="add-an-operation"></a>Adicionar uma operação

1. Selecione a API que você criou na etapa anterior.
2. Clique em **+ Adicionar Operação**.
3. Em **URL**, selecione **GET** e insira " */get*" no recurso.
4. Insira "*FetchData*" para **Nome de exibição**.
5. Clique em **Salvar**.

### <a name="test-an-operation"></a>Testar uma operação

Teste a função no Portal do Azure. Como alternativa, você pode testá-la no **Portal do desenvolvedor**.

1. Selecione a guia **Testar**.
2. Selecione **FetchData**.
3. Pressione **Enviar**.

A resposta que a operação "http://httpbin.org/get"  gera é exibida. Se você deseja transformar suas operações, consulte [Transformar e proteger sua API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Adicionar e testar uma operação parametrizada

Esta seção mostra como adicionar uma operação que utiliza um parâmetro. Nesse caso, mapeamos a operação para "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Adicionar a operação

1. Selecione a API que você criou na etapa anterior.
2. Clique em **+ Adicionar Operação**.
3. Em **URL**, selecione **GET** e insira " */status/{code}* " no recurso. Opcionalmente, você pode fornecer algumas informações associadas a esse parâmetro. Por exemplo, insira "*Número*" para **TIPO**, "*200*" (padrão) para **VALORES**.
4. Insira "GetStatus" para **Nome de exibição**.
5. Clique em **Salvar**.

### <a name="test-the-operation"></a>Testar a operação 

Teste a função no Portal do Azure.  Como alternativa, você pode testá-la no **Portal do desenvolvedor**.

1. Selecione a guia **Testar**.
2. Selecione **GetStatus**. Por padrão, o valor do código é definido como "*200*". Você pode alterá-lo para testar outros valores. Por exemplo, digite "*418*".
3. Pressione **Enviar**.

    A resposta que a operação "http://httpbin.org/status/200"  gera é exibida. Se você deseja transformar suas operações, consulte [Transformar e proteger sua API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transformar e proteger uma API publicada](transform-api.md)
