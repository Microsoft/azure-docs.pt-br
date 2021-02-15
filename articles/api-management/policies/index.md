---
title: Políticas do gerenciamento de API do Azure de exemplo | Microsoft Docs
description: Saiba mais sobre as políticas disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b5b8c82aa420b62e3b6e68ee53352eb9f77988f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506682"
---
# <a name="api-management-policy-samples"></a>Políticas de gerenciamento de API de exemplo

As [políticas](../api-management-howto-policies.md) são funcionalidades poderosas do sistema que permitem que o editor altere o comportamento da API por meio de configuração. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. A tabela a seguir contém links para exemplos e fornece uma breve descrição de cada exemplo.

| Políticas de entrada | Descrição |
| ---------------- | ----------- |
| [Adicionar um cabeçalho Forwarded para permitir que a API de back-end crie URLs adequadas](./set-header-to-enable-backend-to-construct-urls.md) | Demonstra como adicionar um cabeçalho Forwarded na solicitação de entrada para permitir que a API de back-end crie URLs adequadas.                                                                                                        |
| [Adicionar um cabeçalho que contém uma ID de correlação](./add-correlation-id.md)                                                             | Demonstra como adicionar um cabeçalho que contém uma ID de correlação na solicitação de entrada.                                                                                                                                        |
| [Adicionar recursos a um serviço de back-end e armazenar a resposta em cache](./cache-response.md)                                             | Mostra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo.                                                                    |
| [Autorizar o acesso com base em declarações JWT](./authorize-request-based-on-jwt-claims.md)                                              | Mostra como autorizar o acesso a métodos HTTP específicos em uma API com base em declarações de JWT.                                                                                                                                       |
| [Autorizar solicitações usando um autorizador externo](./authorize-request-using-external-authorizer.md)                                                   | Mostra como usar um autorizador externo para proteger o acesso à API.                                                                                                                                                               |
| [Autorizar o acesso usando o token OAuth do Google](./use-google-as-oauth-token-provider.md)                                            | Mostra como autorizar o acesso aos pontos de extremidade usando o Google como um provedor de token OAuth.                                                                                                                                    |
| [Filtrar endereços IP ao usar um Gateway de Aplicativo](./filter-ip-addresses-when-using-appgw.md) | Mostra como filtrar por IP em políticas quando a instância de Gerenciamento de API é acessada por meio de um Gateway de Aplicativo
| [Gerar Assinatura de Acesso Compartilhado e encaminhar solicitação para o armazenamento do Azure](./generate-shared-access-signature.md)                  | Mostra como gerar uma [Assinatura de Acesso Compartilhado](../../storage/common/storage-sas-overview.md) usando expressões e encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri. |
| [Obter token de acesso OAuth2 do AAD e encaminhá-lo para o back-end](./use-oauth2-for-authorization.md)                             | Fornece um exemplo de como usar o OAuth2 para autorização entre o gateway e um back-end. Ele mostra como obter um token de acesso do AAD e encaminhá-lo para o back-end.                                                    |
| [Obter token X-CSRF do SAP Gateway usando a política de solicitação de envio](./get-x-csrf-token-from-sap-gateway.md)                           | Mostra como implementar o padrão X-CSRF usado por várias APIs. Este exemplo é específico do SAP Gateway.                                                                                                                           |
| [Rotear a solicitação com base no tamanho do corpo](./route-requests-based-on-size.md)                                            | Demonstra como rotear solicitações com base no tamanho do corpo.                                                                                                                                                       |
| [Enviar informações de contexto de solicitação para o serviço de back-end](./send-request-context-info-to-backend-service.md)                    | Mostra como enviar algumas informações de contexto para o serviço de back-end para registro em log ou processamento.                                                                                                                                |
| [Definir duração do cache de resposta](./set-cache-duration.md)                                                                          | Demonstra como definir a duração do cache de resposta usando o valor de maxAge no cabeçalho Cache-Control enviado pelo back-end.                                                                                                             |
| **Políticas de saída** | **Descrição** |
| [Filtrar o conteúdo da resposta](./filter-response-content.md)                                                                         | Demonstra como filtrar os elementos de dados do conteúdo da resposta com base no produto associado à solicitação.                                                                                                        |
| **Políticas em caso de erro** | **Descrição** |
| [Registrar erros no Stackify](./log-errors-to-stackify.md)                                                                           | Mostra como adicionar uma política de registro de erro em log para enviar erros ao Stackify para que eles sejam registrados em log.                                                                                                                                            |
