---
title: Back-ends de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre back-ends personalizados no gerenciamento de API
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500611"
---
# <a name="backends-in-api-management"></a>Back-ends no gerenciamento de API

Um *back-end* (ou *back-end de API*) no gerenciamento de API é um serviço http que implementa sua API de front-end e suas operações.

Ao importar determinadas APIs, o gerenciamento de API configura o back-end de API automaticamente. Por exemplo, o gerenciamento de API configura o back-end ao importar uma [especificação openapi](import-api-from-oas.md), [API SOAP](import-soap-api.md)ou recursos do Azure, como um [aplicativo lógico](import-logic-app-as-api.md)ou [aplicativo de funções do Azure](import-function-app-as-api.md) disparado por http.

O gerenciamento de API também dá suporte ao uso de outros recursos do Azure, como um [cluster Service Fabric](how-to-configure-service-fabric-backend.md) ou um serviço personalizado como um back-end de API. O uso desses back-ends personalizados requer configuração extra, por exemplo, para autorizar credenciais de solicitações ao serviço de back-end e definir operações de API. Você configura e gerencia esses back-ends no portal do Azure ou usando APIs ou ferramentas do Azure.

Depois de criar um back-end, você pode fazer referência à URL de back-end em suas APIs. Use a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) política para redirecionar uma solicitação de API de entrada para o back-end personalizado em vez do back-end padrão para essa API.

## <a name="benefits-of-backends"></a>Benefícios de back-ends

Um back-end personalizado tem vários benefícios, incluindo:

* Abstrai informações sobre o serviço de back-end, promovendo a reutilização entre APIs e governança aprimorada  
* Facilmente usado com a configuração de uma política de transformação em uma API existente
* Aproveita a funcionalidade de gerenciamento de API para manter os segredos no Azure Key Vault se [os valores nomeados](api-management-howto-properties.md) estiverem configurados para autenticação de parâmetros de cabeçalho ou consulta

## <a name="next-steps"></a>Próximas etapas

* Configure um [back-end Service Fabric](how-to-configure-service-fabric-backend.md) usando o portal do Azure.
* Os back-ends também podem ser configurados usando a [API REST](/rest/api/apimanagement)de gerenciamento de api, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)ou [modelos de Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

