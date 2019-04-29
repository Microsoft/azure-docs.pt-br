---
title: Políticas no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as políticas disponíveis para uso no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 711f6395959b3718ca0a094a03c07c4381892104
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762849"
---
# <a name="api-management-policies"></a>Políticas de Gerenciamento de API
Esta seção fornece uma referência para as políticas de Gerenciamento de API a seguir. Para obter mais informações sobre como adicionar e configurar políticas, consulte [Políticas de Gerenciamento de API](api-management-howto-policies.md).  
  
 As políticas são um recurso poderoso do sistema que permitem ao editor alterar o comportamento da API por meio de configuração. As políticas são um conjunto de instruções executadas em sequência, no momento da solicitação ou da resposta de uma API. Instruções populares incluem a conversão do formato de XML para JSON e limite de taxa de chamada para restringir a quantidade de chamadas recebidas de um desenvolvedor. Muitas políticas estão disponíveis pré-configuradas.  
  
 Expressões de política podem ser usadas como valores de atributo ou texto em qualquer uma das políticas de Gerenciamento de API, a menos que a política especifique o contrário. Algumas políticas, como [Controlar fluxo](api-management-advanced-policies.md#choose) e [Definir variável](api-management-advanced-policies.md#set-variable) se baseiam em expressões de políticas. Para obter mais informações, confira [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies) e [Expressões de política](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Políticas  
  
-   [Políticas de restrição de acesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Verificar cabeçalho HTTP](api-management-access-restriction-policies.md#CheckHTTPHeader) - Impõe a existência e/ou valor de um cabeçalho HTTP.  
    -   [Limitar a taxa de chamada por assinatura](api-management-access-restriction-policies.md#LimitCallRate) - Previne picos de uso da API limitando a taxa de chamada, baseado em assinatura.  
    -   [Limitar a taxa de chamada por chave](api-management-access-restriction-policies.md#LimitCallRateByKey) - Previne picos de uso da API limitando a taxa de chamada, baseado em chave.  
    -   [Restringir IP do autor da chamada](api-management-access-restriction-policies.md#RestrictCallerIPs) - Filtra (permite/recusa) chamadas de endereços IP específicos e/ou intervalos de endereços.  
    -   [Definir a cota de uso por assinatura](api-management-access-restriction-policies.md#SetUsageQuota) - Permite que você aplique uma cota renovável ou permanente de volume de chamada e/ou largura de banda, baseado em assinatura.  
    -   [Definir a cota de uso por chave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) - Permite que você aplique uma cota renovável ou permanente de volume de chamada e/ou largura de banda, baseado em chave.  
    -   [Validar JWT](api-management-access-restriction-policies.md#ValidateJWT) - Impõe a existência e a validade de JWT extraída de um cabeçalho HTTP especificado ou um parâmetro de consulta especificado.  
-   [Políticas avançadas](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Fluxo de controle](api-management-advanced-policies.md#choose): aplica condicionalmente declarações de política com base na avaliação de expressões boolianas.  
    -   [Encaminhar solicitação](api-management-advanced-policies.md#ForwardRequest) -Encaminha a solicitação ao serviço de back-end.  
    -   [Registrar em log no Hub de Eventos](api-management-advanced-policies.md#log-to-eventhub): envia mensagens no formato especificado para um destino de mensagem definido por uma entidade Agente.  
    -   [Repetir](api-management-advanced-policies.md#Retry) - repete a execução das instruções de política, se e até que a condição seja atendida. A execução será repetida em intervalos de tempo especificados até e a contagem de repetições especificada.  
    -   [Retornar resposta](api-management-advanced-policies.md#ReturnResponse) - Anula a execução de pipeline e retorna a resposta especificada diretamente para o autor da chamada.  
    -   [Enviar solicitação unidirecional](api-management-advanced-policies.md#SendOneWayRequest) - Envia uma solicitação para a URL especificada sem aguardar uma resposta.  
    -   [Enviar solicitação](api-management-advanced-policies.md#SendRequest) - Envia uma solicitação para a URL especificada.  
    -   [Definir variável](api-management-advanced-policies.md#set-variable): persiste um valor em uma variável de contexto nomeada para acesso posterior.  
    -   [Definir método de solicitação](api-management-advanced-policies.md#SetRequestMethod) - Permite alterar o método HTTP de uma solicitação.  
    -   [Definir código de status](api-management-advanced-policies.md#SetStatus) – altera o código de status de HTTP para o valor especificado.  
    -   [Rastreamento](api-management-advanced-policies.md#Trace) - adiciona uma cadeia de caracteres para a saída do [Inspetor de API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/).  
    -   [Aguarde](api-management-advanced-policies.md#Wait): espera pelas políticas [Enviar solicitação](api-management-advanced-policies.md#SendRequest), [Obter valor do cache](api-management-caching-policies.md#GetFromCacheByKey) ou [Fluxo de controle](api-management-advanced-policies.md#choose) incorporadas serem concluídas antes de prosseguir.  
-   [Políticas de autenticação](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Autenticar com o Basic](api-management-authentication-policies.md#Basic) - Autenticar com um serviço de back-end usando a autenticação Básica.  
    -   [Autenticar com o certificado de cliente](api-management-authentication-policies.md#ClientCertificate) - Autenticar com um serviço de back-end usando certificados de cliente.  
    -   [Autenticar com identidade gerenciada](api-management-authentication-policies.md#ManagedIdentity) -autenticar com um serviço de back-end usando um [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Políticas de cache](api-management-caching-policies.md#CachingPolicies)  
    -   [Obter do cache](api-management-caching-policies.md#GetFromCache) - Executa a pesquisa em cache e retorna uma resposta válida armazenada em cache quando uma estiver disponível.  
    -   [Armazenar em cache](api-management-caching-policies.md#StoreToCache) - Armazena a resposta em cache de acordo com a configuração de controle de cache especificada.  
    -   [Obter valor do cache](api-management-caching-policies.md#GetFromCacheByKey) - Recupere um item em cache por chave.  
    -   [Armazenar valor em cache](api-management-caching-policies.md#StoreToCacheByKey) -Armazene um item no cache por chave.  
    -   [Remover o valor do cache](api-management-caching-policies.md#RemoveCacheByKey) - remove um item no cache por chave.  
-   [Políticas entre domínios](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Permitir chamadas entre domínios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) - Torna a API acessível por meio de clientes Adobe Flash e Microsoft Silverlight baseados em navegadores.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) - Adicionar suporte de compartilhamento de recursos entre origens (CORS) a uma operação ou a uma API para permitir chamadas entre domínios de clientes baseados em navegadores.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) - Adiciona suporte JSON com preenchimento (JSONP) a uma operação ou a uma API para permitir chamadas entre domínios de clientes JavaScript baseados em navegadores.  
-   [Políticas de transformação](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Converter JSON para XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Converte o corpo da solicitação ou da resposta de JSON para XML.  
    -   [Converter XML para JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Converte o corpo da solicitação ou da resposta de XML para JSON.  
    -   [Localizar e substituir cadeia no corpo](api-management-transformation-policies.md#Findandreplacestringinbody) - Encontra uma subcadeia de uma solicitação ou resposta e a substitui por outra subcadeia.  
    -   [Mascarar URLs no conteúdo](api-management-transformation-policies.md#MaskURLSContent) – Reescreve (mascara) os links no corpo da resposta, para que eles apontem para o link equivalente por meio do gateway.  
    -   [Definir o serviço de back-end](api-management-transformation-policies.md#SetBackendService) - Altera o serviço de back-end para uma solicitação de entrada.  
    -   [Definir corpo](api-management-transformation-policies.md#SetBody) - Define o corpo da mensagem para solicitações de entrada e saída.  
    -   [Definir cabeçalho HTTP](api-management-transformation-policies.md#SetHTTPheader) - Atribui um valor a uma resposta e/ou cabeçalho de resposta existente ou adiciona uma nova resposta e/ou cabeçalho de resposta.  
    -   [Definir parâmetro de cadeia de consulta](api-management-transformation-policies.md#SetQueryStringParameter) - Adiciona, substitui o valor ou exclui parâmetros de cadeias de consulta de solicitação.  
    -   [Reescrever URL](api-management-transformation-policies.md#RewriteURL) - Converte a URL de uma solicitação de sua forma pública em sua forma esperada pelo serviço Web.  
    -   [Transformar XML usando um XSLT](api-management-transformation-policies.md#XSLTransform): aplica uma transformação XSL para XML no corpo da solicitação ou da resposta.  



## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como trabalhar com políticas, consulte:

+ [Políticas no Gerenciamento de API](api-management-howto-policies.md)
+ [Transformar APIs](transform-api.md)
+ [Referência de Política](api-management-policy-reference.md) para uma lista completa das instruções de política e suas configurações
+ [Exemplos de política](policy-samples.md)   
