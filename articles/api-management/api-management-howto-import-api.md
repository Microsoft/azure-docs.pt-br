---
title: Importar uma API para o Gerenciamento de API do Azure | Microsoft Docs
description: "Saiba como importar uma API e suas operações para o Gerenciamento de API do Azure."
services: api-management
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: c851b88fc1067e65044266d07775717c028e75d9
ms.contentlocale: pt-br
ms.lasthandoff: 01/31/2017

---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Como importar a definição de uma API com operações no Gerenciamento da API do Azure
No Gerenciamento de API, novas APIs podem ser criadas e as operações podem ser adicionadas manualmente, ou a API pode ser importada em conjunto com as operações em uma etapa.

As APIs e suas operações podem ser importadas usando os formatos a seguir.

* WADL
* Swagger

Este guia mostra como criar uma nova API e importar suas operações em uma etapa. Para obter mais informações sobre como criar uma API e adicionar operações manualmente, consulte [Como criar APIs][How to create APIs] e [Como adicionar operações a uma API][How to add operations to an API].

## <a name="import-api"> </a>Importar uma API
APIs são criadas e configuradas no Portal do editor. Para acessar o portal do editor, clique em **Portal do editor** no Portal do Azure para acessar o serviço Gerenciamento de API. Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].

![Portal do editor][api-management-management-console]

Clique em **APIs** no menu **Gerenciamento de API** à esquerda e depois clique em **Importar API**.

![Importar API][api-management-import-apis]

A janela **Importar API** possui três guias que correspondem às três maneiras de fornecer a especificação da API.

* **A partir da área de transferência** permite que você cole a especificação da API na caixa de texto designada.
* **A partir do arquivo** permite que você navegue até e selecione o arquivo que contém a especificação da API.
* **A partir da URL** permite que você forneça a URL até a especificação para a API.

![Importar formato de API][api-management-import-api-clipboard]

Após fornecer a especificação da API, use os botões de opção à direita para indicar o formato da especificação. Os formatos a seguir são suportados.

* WADL
* Swagger

Em seguida, insira um **sufixo de URL de API Web**. Ele está anexado à URL base do serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas em cada instância de um serviço de Gerenciamento da API. O Gerenciamento da API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API em uma instância de serviço de gerenciamento de API específica.

Após inserir todos os valores, clique em **Salvar** para criar a API e as operações associadas. 

> [!NOTE]
> Para obter um tutorial de importação de uma API básica de calculadora no formato Swagger, consulte [Gerenciar sua primeira API no Gerenciamento de API do Azure](api-management-get-started.md).
> 
> 

## <a name="export-api"> </a> Exportar uma API
Além de importar novas APIs, você pode exportar as definições de suas APIs no Portal do editor. Para fazer isso, clique em **Exportar API** na **guia de Resumo** de sua **API**.

![Exportar API][api-management-export-api]

As APIs podem ser exportadas usando WADL ou Swagger. Selecione o formato desejado, clique em **Salvar**e escolha o local onde deseja salvar o arquivo.

![Exportar formato de API][api-management-export-api-format]

## <a name="next-steps"> </a>Próximas etapas
Após criar a API e importar as operações, você pode revisar e definir quaisquer configurações adicionais, adicionar a API a um produto e publicá-la para que fique disponível para desenvolvedores. Para obter mais informações, consulte os guias a seguir.

* [Como definir configurações de API][How to configure API settings]
* [Como criar e publicar um produto][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings

