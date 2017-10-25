---
title: "Referência de políticas do Gerenciamento de API do Azure"
description: "Saiba mais sobre as políticas disponíveis para configurar o Gerenciamento de API."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Referência de políticas do Gerenciamento de API do Azure
Esta seção fornece um índice para as políticas na [referência de política de Gerenciamento de API][API Management policy reference]. Para obter informações sobre como adicionar e configurar políticas, consulte [Políticas no Gerenciamento de API do Azure][Policies in API Management].

Expressões de política podem ser usadas como valores de atributo ou texto em qualquer uma das políticas de Gerenciamento de API, a menos que a política especifique o contrário. Algumas políticas como [Controlar fluxo][Control flow] e [Definir variável][Set variable] baseiam-se em expressões de políticas. Para obter mais informações, consulte [Advanced policies][Advanced policies] (Políticas avançadas) e [Policy expressions][Policy expressions] (Expressões de política)

## <a name="policy-reference-index"></a>Índice de referência de política
* [Access restriction policies][Access restriction policies] (Políticas de restrição de acesso)
  * [Verificar cabeçalho HTTP][Check HTTP header] – impõe a existência e/ou o valor de um Cabeçalho HTTP.
  * [Limitar a taxa de chamada por assinatura][Limit call rate by subscription] – previne picos de uso da API limitando a taxa de chamada, baseado em assinatura.
  * [Limitar a taxa de chamada por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - Previne picos de uso da API limitando a taxa de chamada, baseado em chave.
  * [Restringir IPs do chamador][Restrict caller IPs] – filtra (permite/nega) chamadas de endereços IP e/ou intervalos de endereços específicos.
  * [Definir cota de uso por assinatura][Set usage quota by subscription] – permite impor uma cota renovável ou de tempo de vida de volume de chamadas e/ou largura de banda, baseado em assinatura.
  * [Definir a cota de uso por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - Permite que você aplique uma cota renovável ou permanente de volume de chamada e/ou largura de banda, baseado em chave.
  * [Validar JWT][Validate JWT] – impõe a existência e a validade de um JWT extraído de um Cabeçalho HTTP ou um parâmetro de consulta especificado.
* [Advanced policies][Advanced policies] (Políticas avançadas)
  * [Controlar fluxo][Control flow] – aplica condicionalmente instruções de política com base nos resultados da avaliação de [expressões][expressions] boolianas.
  * [Encaminhar solicitação][Forward request] – encaminha a solicitação ao serviço de back-end.
  * [Registrar no Hub de Eventos][Log to Event Hub] – envia mensagens no formato especificado para um destino de mensagem definido por uma entidade [Agente](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
  * [Repetir](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - repete a execução das instruções de política, se e até que a condição seja atendida. A execução será repetida em intervalos de tempo especificados até e a contagem de repetições especificada.
  * [Retornar resposta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - Anula a execução de pipeline e retorna a resposta especificada diretamente para o autor da chamada.
  * [Enviar solicitação unidirecional](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - Envia uma solicitação para a URL especificada sem aguardar uma resposta.
  * [Enviar solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - Envia uma solicitação para a URL especificada.
  * [Definir método de solicitação](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - Permite alterar o método HTTP de uma solicitação.
  * [Definir status](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - Altera o código de status de HTTP para o valor especificado.
  * [Definir variável][Set variable] – persiste um valor em uma variável [context][context] nomeada para acesso posterior.
  * [Rastreamento](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - adiciona uma cadeia de caracteres para a saída do [Inspetor de API](api-management-howto-api-inspector.md).
  * [Aguardar](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - Aguarda a conclusão da solicitação de Envio embutida, Obtenção do valor do cache ou Controle de políticas de fluxo antes de continuar.
* [Authentication policies][Authentication policies] (Políticas de autenticação)
  * [Autenticar com o Básico][Authenticate with Basic] – autenticar com um serviço de back-end usando a autenticação Básica.
  * [Autenticar com o certificado do cliente][Authenticate with client certificate] – autenticar com um serviço de back-end usando certificados do cliente.
* [Caching policies][Caching policies] (Políticas de caching) 
  * [Obter do cache][Get from cache] – executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando disponível.
  * [Armazenar em cache][Store to cache] – armazena a resposta em cache de acordo com a configuração de controle de cache especificada.
  * [Obter valor do cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - Recupere um item em cache por chave.
  * [Armazenar valor em cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -Armazene um item no cache por chave.
  * [Remover o valor do cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - remove um item no cache por chave.
* [Cross domain policies][Cross domain policies] (Políticas entre domínios) 
  * [Permitir chamadas entre domínios][Allow cross-domain calls] – torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegador.
  * [CORS][CORS] – adiciona suporte do CORS (compartilhamento de recurso entre origens) a uma operação ou API para permitir chamadas entre domínios de clientes baseados em navegador.
  * [JSONP][JSONP] – adiciona suporte do JSON com preenchimento (JSONP) a uma operação ou API para permitir chamadas entre domínios de clientes JavaScript baseados em navegador.
* [Transformation policies][Transformation policies] (Políticas de transformação) 
  * [Converter JSON para XML][Convert JSON to XML] – converte o corpo da solicitação ou da resposta de JSON para XML.
  * [Converter XML para JSON][Convert XML to JSON] – converte o corpo da solicitação ou da resposta de XML para JSON.
  * [Localizar e substituir cadeia de caracteres no corpo][Find and replace string in body] – localiza uma subcadeia de caracteres de solicitação ou de resposta e a substitui por outra subcadeia de caracteres.
  * [Mascarar URLs no conteúdo][Mask URLs in content] – reescreve (mascara) os links no corpo da resposta para que eles apontem para o link equivalente por meio do gateway.
  * [Definir serviço de back-end][Set backend service] – altera o serviço de back-end de uma solicitação de entrada.
  * [Definir corpo][Set body] – define o corpo da mensagem de solicitações de entrada e saída.
  * [Definir cabeçalho HTTP][Set HTTP header] – atribui um valor a um cabeçalho de resposta e/ou solicitação existente ou adiciona um novo cabeçalho de resposta e/ou solicitação.
  * [Definir parâmetro de cadeia de consulta][Set query string parameter] – adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.
  * [Reescrever URL][Rewrite URL] – converte a URL de uma solicitação de sua forma pública para a forma esperada pelo serviço Web.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre expressões de política, consulte o vídeo a seguir.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx


