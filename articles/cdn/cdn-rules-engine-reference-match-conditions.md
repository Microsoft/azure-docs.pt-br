---
title: "Condições de correspondência do mecanismo de regras da CDN do Azure | Microsoft Docs"
description: "Documentação de referência para regras e recursos de condições de correspondência do mecanismo da CDN do Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Condições de correspondência do mecanismo de regras da CDN do Azure
Este tópico lista descrições detalhadas das Condições de correspondência disponíveis para o [Mecanismo de regras](cdn-rules-engine.md)da CDN (Rede de Distribuição de Conteúdo) do Azure.

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para os quais um conjunto de recursos será executado.

Por exemplo, ela pode ser usada para filtrar solicitações de conteúdo em um local específico, solicitações geradas de um endereço IP ou país específico ou por informações de cabeçalho.

## <a name="always"></a>Sempre

A condição de correspondência Sempre foi projetada para aplicar um conjunto de recursos padrão a todas as solicitações.

## <a name="device"></a>Dispositivo

A condição de correspondência Dispositivo identifica solicitações feitas de um dispositivo móvel baseadas em suas propriedades.  A detecção de dispositivos móveis é alcançada por meio de [WURFL](http://wurfl.sourceforge.net/).  Os recursos do WURFL e suas variáveis de mecanismo de regras de CDN estão listados abaixo.

> [!NOTE] 
> As variáveis a seguir têm suporte nos recursos **Modificar Cabeçalho de Solicitação do Cliente** e **Modificar Cabeçalho de Resposta do Cliente**.

Recurso | Variável | Descrição | Valor(es) de exemplo
-----------|----------|-------------|----------------
Nome da marca | %{wurfl_cap_brand_name} | Uma cadeia de caracteres que indica o nome da marca do dispositivo. | Samsung
SO do dispositivo | %{wurfl_cap_device_os} | Uma cadeia de caracteres que indica o sistema operacional instalado no dispositivo. | IOS
Versão do SO do dispositivo | %{wurfl_cap_device_os_version} | Uma cadeia de caracteres que indica o número de versão do sistema operacional instalado no dispositivo. | 1.0.1
Orientação dupla | %{wurfl_cap_dual_orientation} | Um booliano que indica se o dispositivo dá suporte a orientação dupla. | verdadeiro
DTD preferencial HTML | %{wurfl_cap_html_preferred_dtd} | Uma cadeia de caracteres que indica a DTD (definição de tipo de documento) do dispositivo móvel para conteúdo HTML. | nenhum<br/>xhtml_basic<br/>html5
Inlining de Imagem | %{wurfl_cap_image_inlining} | Um booliano que indica se o dispositivo dá suporte a imagens codificadas de Base64. | false
É Android | %{wurfl_vcap_is_android} | Um booliano que indica se o dispositivo usa o sistema operacional Android. | verdadeiro
É IOS | %{wurfl_vcap_is_ios} | Um booliano que indica se o dispositivo usa iOS. | false
É Smart TV | %{wurfl_cap_is_smarttv} | Um booliano que indica se o dispositivo é uma Smart TV. | false
É Smartphone | %{wurfl_vcap_is_smartphone} | Um booliano que indica se o dispositivo é um smartphone. | verdadeiro
É Tablet | %{wurfl_cap_is_tablet} | Um booliano que indica se o dispositivo é um tablet. Esta é uma descrição independente de sistema operacional. | verdadeiro
É dispositivo sem fio | %{wurfl_cap_is_wireless_device} | Um booliano que indica se o dispositivo é considerado um dispositivo sem fio. | verdadeiro
Nome de marketing | %{wurfl_cap_marketing_name} | Uma cadeia de caracteres que indica o nome de marketing do dispositivo. | BlackBerry 8100 Pearl
Navegador de dispositivo móvel | %{wurfl_cap_mobile_browser} | Uma cadeia de caracteres que indica o navegador usado para solicitar o conteúdo do dispositivo. | Chrome
Versão do navegador móvel | %{wurfl_cap_mobile_browser_version} | Uma cadeia de caracteres que indica a versão do navegador usado para solicitar conteúdo do dispositivo. | 31
Nome do modelo | %{wurfl_cap_model_name} | Uma cadeia de caracteres que indica o nome do modelo do dispositivo. | s3
Download progressivo | %{wurfl_cap_progressive_download} | Um booliano que indica se o dispositivo dá suporte a reprodução de áudio/vídeo enquanto ele ainda está sendo baixado. | verdadeiro
Data do lançamento | %{wurfl_cap_release_date} | Uma cadeia de caracteres que indica o ano e o mês em que o dispositivo foi adicionado ao banco de dados WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura de resolução | %{wurfl_cap_resolution_height} | Um inteiro que indica a altura do dispositivo em pixels. | 768
Largura de resolução | %{wurfl_cap_resolution_width} | Um inteiro que indica a largura do dispositivo em pixels. | 1024


## <a name="location"></a>Local

Essas condições de correspondência são projetadas para identificar solicitações baseadas na localização do solicitante.

Nome | Finalidade
-----|--------
Número AS | Identifica solicitações originadas de uma rede específica.
País | Identifica solicitações originadas de países especificados.


## <a name="origin"></a>Origem

Essas condições de correspondência são projetadas para identificar solicitações que apontem para o armazenamento CDN ou para um servidor de origem do cliente.

Nome | Finalidade
-----|--------
Origem CDN | Identifica solicitações de conteúdo armazenado no armazenamento CDN.
Origem do Cliente | Identifica solicitações de conteúdo armazenado em um servidor de origem do cliente específico.


## <a name="request"></a>Solicitação

Essas condições de correspondência são projetadas para identificar solicitações baseadas em suas propriedades.

Nome | Finalidade
-----|--------
Endereço IP do Cliente | Identifica solicitações originadas de um endereço IP específico.
Parâmetro de Cookie | Verifica o valor especificado nos cookies associados a cada solicitação.
Regex de Parâmetro de Cookie | Verifica a expressão regular especificada nos cookies associados a cada solicitação.
Cname de Borda | Identifica solicitações que apontam para um CNAME de borda específico.
Domínio de Referência | Identifica solicitações referenciadas dos nomes do host especificados.
Literal de Cabeçalho de Solicitação | Identifica solicitações que contêm o cabeçalho especificado definido como valores especificados.
Regex do Cabeçalho da Solicitação | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponde à expressão regular especificada.
Curinga de Cabeçalho de Solicitação | Identifica solicitações que contêm o cabeçalho especificado definido como um valor que corresponda ao padrão especificado.
Método de Solicitação | Identifica solicitações pelo método HTTP.
Esquema de Solicitação | Identifica solicitações pelo protocolo HTTP.

## <a name="url"></a>URL

Essas condições de correspondência são projetadas para identificar solicitações baseadas em suas URLs.

Nome | Finalidade
-----|--------
Diretório do Caminho da URL | Identifica solicitações pelo caminho relativo.
Extensão do Caminho da URL | Identifica solicitações pela extensão de nome do arquivo.
Nome do Arquivo do Caminho da URL | Identifica solicitações pelo nome do arquivo
Literal do Caminho da URL | Compara a caminho relativo da solicitação com o valor especificado.
Regex do Caminho da URL | Compara o caminho relativo da solicitação com a expressão regular especificada.
Curinga do Caminho da URL | Compara o caminho relativo da solicitação com o padrão especificado.
Literal da Consulta da URL | Compara a sequência da cadeia de caracteres de consulta da solicitação com o valor especificado.
Parâmetro da Consulta da URL | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponda a um padrão especificado.
Regex da consulta da URL | Identifica solicitações que contêm o parâmetro de cadeia de caracteres de consulta especificado definido como um valor que corresponda a uma expressão regular especificada.
Curinga da consulta da URL | Compara os valores especificados com a cadeia de caracteres de consulta da solicitação.


## <a name="next-steps"></a>Próximas etapas
* [Visão geral da CDN do Azure](cdn-overview.md)
* [Referência do Mecanismo de Regras](cdn-rules-engine-reference.md)
* [Expressões condicionais do Mecanismo de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Recursos do Mecanismo de Regras](cdn-rules-engine-reference-features.md)
* [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-rules-engine.md)

