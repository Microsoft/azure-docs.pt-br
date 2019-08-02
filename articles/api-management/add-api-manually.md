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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 0094364ed2e5d6c024f75a88db90eb703792f9f3
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405839"
---
# <a name="add-an-api-manually"></a>Adicionar uma API manualmente

As etapas neste artigo mostram como usar o portal do Azure para adicionar uma API manualmente à instância de APIM (Gerenciamento de API). Um cenário comum quando você deseja criar uma API em branco e defini-la manualmente é quando deseja simular a API. Para obter detalhes sobre a simulação de uma API, consulte [Simular respostas de API](mock-api-responses.md).

Se você deseja importar uma API existente, consulte a seção de [tópicos relacionados](#related-topics).

Neste artigo, criamos uma API em branco e especificamos [httpbin.org](https://httpbin.org) (um serviço de teste público) como a API de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Criar uma API

1. Selecione **APIs** em **GERENCIAMENTO DE API**.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em Branco** na lista.

    ![API em Branco](media/add-api-manually/blank-api.png)
4. Insira as configurações para a API.

    |**Nome**|**Valor**|**Descrição**|
    |---|---|---|
    |**Nome de exibição**|*API em Branco*|Esse nome é exibido no Portal do desenvolvedor.|
    |**Nome**|*blank-api*|Fornece um nome exclusivo para a API.|
    |**URL do Serviço Web** (opcional)|*https://httpbin.org*| Se você quiser simular uma API, não poderá inserir nada. <br/>Neste caso, usamos [https://httpbin.org](https://httpbin.org). Este é um serviço de teste público. <br/>Se você deseja importar uma API mapeada para um back-end automaticamente, consulte um dos tópicos na seção de [tópicos relacionados](#related-topics).|
    |**Esquema de URL**|*HTTPS*|Nesse caso, embora o back-end tenha acesso HTTP não seguro especificamos um acesso de APIM de HTTPS seguro para o back-end. <br/>Esse tipo de cenário (HTTPS para HTTP) é chamado de terminação HTTPS. Você pode fazer isso se sua API existe em uma rede virtual (em que você sabe que o acesso é seguro, mesmo se o HTTPS não é usado). <br/>Talvez você queira usar a "Terminação HTTPS" para economizar em alguns ciclos de CPU.|
    |**Sufixo da URL**|*hbin*| O sufixo é um nome que identifica essa API específica nesta instância do APIM. Ele deve ser exclusivo nesta instância de APIM.|
    |**Produtos**|*Ilimitado*|Publica a API associando-a a um produto. Se você deseja que a API seja publicada e fique disponível para os desenvolvedores, adicione-a a um produto. Você pode fazer isso durante a criação da API ou configurá-lo mais tarde.<br/><br/>Os produtos são associações de uma ou mais APIs. Você pode incluir várias APIs e oferecê-las aos desenvolvedores por meio do portal do desenvolvedor. <br/>Primeiro, os desenvolvedores devem assinar um produto para obter acesso à API. Com a assinatura, eles obtêm uma chave de assinatura que funciona para qualquer API no produto. Se você criou a instância do APIM, já é um administrador e, portanto, está inscrito em cada produto por padrão.<br/><br/> Por padrão, cada instância de Gerenciamento de API é fornecida com dois produtos função Web: **Starter** e **Ilimitado**.| 
5. Selecione **Criar**.

Neste ponto, você não tem nenhuma operação no APIM mapeada para operações em sua API de back-end. Se você chamar uma operação que é exposta por meio de back-end, mas não por meio de APIM, receberá um **404**.

>[!NOTE] 
> Por padrão, quando você adiciona uma API, mesmo se ela estiver conectada a algum serviço de back-end, o APIM não exporá nenhuma operação até que você as coloque na lista de permissões. Para adicionar uma operação do serviço de back-end à lista de permissões, crie uma operação de APIM que mapeia para a operação de back-end.

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
