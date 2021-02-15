---
title: CDN do Azure nas condições de correspondência do mecanismo de regras da Verizon Premium
description: Documentação de referência para a Rede de Distribuição de Conteúdo do Azure nas condições de correspondência do mecanismo de regras da Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323307"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>CDN do Azure nas condições de correspondência do mecanismo de regras da Verizon Premium

Este artigo lista descrições detalhadas das condições de correspondência disponíveis para a CDN (Rede de Distribuição de Conteúdo) do Azure no [mecanismo de regras](cdn-verizon-premium-rules-engine.md) da Verizon Premium.

A segunda parte de uma regra é a condição de correspondência. Uma condição de correspondência identifica tipos específicos de solicitações para os quais um conjunto de recursos será executado.

Por exemplo, você pode usar uma condição de correspondência para:

- Filtrar solicitações para conteúdo em um local específico.
- Filtrar solicitações geradas de um endereço IP ou país/região específica.
- Filtrar solicitações por informações de cabeçalho.

## <a name="match-conditions"></a><a name="top"></a>Condições de correspondência

* [Always](#always)
* [Dispositivo](#device)
* [Localidade](#location)
* [Origem](#origin)
* [Solicitação](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Constante

[A condição de correspondência sempre](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) é projetada para aplicar um conjunto padrão de recursos a todas as solicitações.

### <a name="device"></a><a name="device"></a>Vice

Essas condições de correspondência são projetadas para identificar solicitações baseadas no agente do usuário do cliente.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| Nome da marca | Identifica solicitações por se o nome da marca do dispositivo corresponder a: <br> **-** Valor específico ([literal de nome da marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** Expressão regular ([Regex de nome da marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** Padrão específico ([caractere curinga de nome de marca](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| SO do dispositivo | Identifica solicitações por se o sistema operacional do dispositivo corresponder a: <br> **-** Valor específico ([literal do so do dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** Expressão regular ([Regex do sistema operacional do dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** Padrão específico ([curinga do sistema operacional do dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Versão do SO do dispositivo | Identifica solicitações por se a versão do sistema operacional do dispositivo corresponder a: <br> **-** Valor específico ([literal de versão do so do dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** Expressão regular ([Regex de versão do so do dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** Padrão específico ([curinga de versão do so do dispositivo](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Orientação dupla?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identifica solicitações por se o dispositivo dá suporte à orientação dupla. |
| DTD preferencial HTML | Identifica solicitações por se o DTD de HTML preferencial do dispositivo corresponde a: <br> **-** Valor específico ([HTML preferencial DTD literal](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** Expressão regular ([Regex HTML preferencial DTD](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** Padrão específico ([curinga de DTD HTML preferencial](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Imagem inalinhada?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identifica solicitações por se o dispositivo dá suporte a imagens codificadas em base64. |
| [É Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identifica as solicitações por se o dispositivo usa o sistema operacional Android. |
| [É aplicativo?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identifica solicitações por um aplicativo nativo solicitou conteúdo. |
| [A área de trabalho é completa?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identifica solicitações por se o dispositivo fornece uma experiência de área de trabalho completa. |
| [É iOS?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identifica as solicitações por se o dispositivo usa o iOS. |
| [É robô?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identifica solicitações por se o dispositivo é considerado como um cliente HTTP automatizado (por exemplo, um rastreador de robô). |
| [É Smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identifica solicitações por se o dispositivo é uma TV inteligente. |
| [É smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identifica solicitações por se o dispositivo é um smartphone.
| [É Tablet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identifica solicitações por se o dispositivo é um Tablet. |
| [É tela de toque?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identifica solicitações por se o dispositivo apontador principal do dispositivo é uma tela sensível ao toque. |
| [Está Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identifica solicitações por se o dispositivo é um Windows Mobile 6.5/Windows Phone 7 ou superior. |
| [É um dispositivo sem fio?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identifica solicitações por se o dispositivo é sem fio. 
| Nome de marketing | Identifica solicitações por se o nome de marketing do dispositivo corresponder a: <br> **-** Valor específico ([nome de marketing literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** Expressão regular ([Regex de nome de marketing](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** Padrão específico ([nome de marketing-curinga](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Navegador de dispositivo móvel | Identifica solicitações por se o navegador do dispositivo corresponder a: <br> **-** Valor específico ([literal do Mobile Browser](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** Expressão regular ([Regex de navegadores móveis](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** Padrão específico ([curinga do navegador móvel](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Versão do navegador móvel | Identifica solicitações por se a versão do navegador do dispositivo corresponder a: <br> **-** Valor específico ([literal de versão do navegador móvel](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** Expressão regular ([Regex de versão do navegador móvel](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** Padrão específico ([curinga de versão do navegador móvel](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Nome do modelo | Identifica solicitações por se o nome do modelo do dispositivo corresponder a: <br> **-** Valor específico ([nome do modelo literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** Expressão regular ([nome do modelo Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** Padrão específico ([nome do modelo curinga](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Download progressivo?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identifica solicitações por se o dispositivo dá suporte ao download progressivo. |
| Data de lançamento | Identifica solicitações por se a data de lançamento do dispositivo corresponder a: <br> **-** Valor específico ([data de lançamento literal](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** Expressão regular ([Regex de data de lançamento](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** Padrão específico ([curinga de data de lançamento](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Altura de resolução](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identifica solicitações pela altura do dispositivo. |
| [Largura de resolução](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identifica solicitações pela largura do dispositivo. |

**[Voltar ao início](#top)**

### <a name="location"></a><a name="location"></a>Local

Essas condições de correspondência são projetadas para identificar solicitações baseadas na localização do solicitante.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Número AS](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identifica solicitações originadas de uma rede específica. |
| Nome da cidade | Identifica solicitações por se originarem de uma cidade cujo nome corresponde a: <br> **-** Valor específico ([nome da cidade literal](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** Expressão regular ([Regex de nome de cidade](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Continente](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identifica solicitações originadas de continentes especificados. |
| [País](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identifica solicitações originadas de países especificados. |
| [Código DMA](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identifica solicitações originadas de metrôs especificados (áreas de mercado designadas). |
| [Latitude](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identifica solicitações originadas de latitudes especificadas. |
| [Longitude](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identifica solicitações originadas das longitudes especificadas. |
| [Código metro](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identifica solicitações originadas de metrôs especificados (áreas de mercado designadas). |
| [Código postal](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identifica solicitações originadas dos códigos postais especificados. |
| [Código de região](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identifica solicitações originadas de regiões especificadas. |

> [!NOTE]
> **Devo usar o código metro ou o código DMA?** <br>
Ambas as condições de correspondência fornecem o mesmo recurso. No entanto, recomendamos o uso da condição de correspondência de código metro para identificar solicitações por DMA.

**[Voltar ao início](#top)**

### <a name="origin"></a><a name="origin"></a>Origem

Essas condições de correspondência são projetadas para identificar solicitações que apontem para o armazenamento CDN ou para um servidor de origem do cliente.

| Nome       | Finalidade                                                           |
|------------|-------------------------------------------------------------------|
| [Origem CDN](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identifica solicitações de conteúdo armazenado no armazenamento CDN. |
| [Origem do Cliente](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identifica solicitações de conteúdo armazenado em um servidor de origem do cliente específico. |

**[Voltar ao início](#top)**

### <a name="request"></a><a name="request"></a>Solicitação

Essas condições de correspondência são projetadas para identificar solicitações baseadas em suas propriedades.

| Nome              | Finalidade                                                                |
|-------------------|------------------------------------------------------------------------|
| [Endereço IP do Cliente](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identifica solicitações originadas de um endereço IP específico. |
| Parâmetro de Cookie  | Identifica uma solicitação pelo fato de ela conter um cookie que corresponda a: <br> **-** Valor específico ([literal de parâmetro de cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** Expressão regular ([Regex de parâmetro de cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Padrão específico ([curinga de parâmetro de cookie](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [CNAME de borda](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identifica solicitações que apontam para um CNAME de borda específico. |
| Domínio de Referência | Identifica uma solicitação por se ela foi referenciada por um nome de host que corresponde a: <br> **-** Valor específico ([literal de domínio de referência](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** Padrão específico ([referência de domínio curinga](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| Cabeçalho da solicitação | Identifica uma solicitação por se ela contiver um cabeçalho que corresponda a: <br> **-** Valor específico ([literal de cabeçalho de solicitação](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** Expressão regular ([Regex de cabeçalho de solicitação](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** Padrão específico ([caractere curinga de cabeçalho de solicitação](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Método de solicitação](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identifica solicitações pelo método HTTP. |
| [Esquema de Solicitação](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identifica solicitações pelo protocolo HTTP. |

**[Voltar ao início](#top)**

### <a name="url"></a><a name="url"></a>URL

| Nome              | Finalidade                                                                |
|-------------------|------------------------------------------------------------------------|
| Caminho da URL | Identifica as solicitações por se seu caminho relativo, incluindo FileName, corresponde a: <br> **-** Valor específico ([caminho da URL literal](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** Expressão regular ([Regex de caminho de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** Padrão específico ([curinga de caminho de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| Diretório do Caminho da URL | Identifica as solicitações por se o caminho relativo corresponde a: <br> **-** Valor específico ([literal de diretório de caminho de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** Padrão específico ([curinga de diretório de caminho de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| Extensão do Caminho da URL | Identifica solicitações pelo fato de sua extensão de arquivo corresponder a: <br> **-** Valor específico ([literal de extensão de caminho de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** Padrão específico ([curinga de extensão de caminho de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| Nome do Arquivo do Caminho da URL | Identifica solicitações por se seu nome de arquivo corresponde a: <br> **-** Valor específico ([caminho da URL literal de nome de arquivo](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** Padrão específico ([URL do caminho do nome do arquivo curinga](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| Consulta de URL | Identifica as solicitações por se a cadeia de caracteres de consulta corresponde a: <br> **-** Valor específico ([literal de consulta de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** Expressão regular ([Regex de consulta de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** Padrão específico ([curinga de consulta de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| Parâmetro da Consulta da URL | Identifica solicitações pelo fato de elas conterem um parâmetro de cadeia de caracteres de consulta definido como um valor que corresponde a: <br> **-** Valor específico ([literal de parâmetro de consulta de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** Padrão específico ([curinga de parâmetro de consulta de URL](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[Voltar ao início](#top)**

Para obter as condições de correspondência mais recentes, confira a [Documentação do mecanismo de regras da Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da Rede de Distribuição de Conteúdo do Azure](cdn-overview.md)
- [Referência do mecanismo de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Recursos do mecanismo de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Substituindo o comportamento HTTP padrão usando o mecanismo de regras](cdn-verizon-premium-rules-engine.md)
