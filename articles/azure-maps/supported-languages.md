---
title: Suporte à localização com mapas de Microsoft Azure
description: Veja quais regiões o Azure Maps dá suporte com serviços como mapas, pesquisa, roteamento, clima e incidentes de tráfego. Saiba como configurar o parâmetro de exibição.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 50e5d0721eb14d1fcdfad26aaf081bfa370e954e
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904509"
---
# <a name="localization-support-in-azure-maps"></a>Suporte à localização no Azure Mapas

O Azure Mapas dá suporte a vários idiomas e exibições com base no país/região. Este artigo fornece os idiomas e as exibições com suporte para ajudar a orientar sua implementação do Azure Mapas.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte nos Mapas do Azure

O Azure Mapas foi traduzido para uma variedade de idiomas em seus serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| ID         | Nome                   |  Mapas | Search | Roteamento | Clima | Incidentes de tráfego | Controle de mapa JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Africâner              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Índia)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bósnio                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Basco                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Catalão                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinês (RAE de Hong Kong)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinês (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Croata               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tcheco                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Holandês (Países Baixos)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pt-BR      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estoniano               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francês                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francês (Canadá)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galego               |       |    ✓   |         |         |                   |                |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grego                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Guzerate                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebraico                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Híndi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| iS-IS      | Islandês              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonês               |       |        |         |     ✓    |                   |                |
| kn-IN      | canarim                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Cazaque                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Espanhol (América Latina) |       |    ✓   |         |         |                   |                |
| lv-LV      | Letão                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedônio             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marati                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norueguês bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth - idiomas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutro verdade - exônimos latinos. Script latino será usado se disponível |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polonês                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Panjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Romeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Sérvio (cirílico)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Sérvio (latino)        |       |       |         |     sr-Latn    |                   |                |
| sk-SK      | Eslovaco             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Sueco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tâmil (Índia)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Télugo (Índia)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ucraniano               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbek                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Exibições com suporte do Azure Mapas

> [!Note]
> Em 1º de agosto de 2019, o Azure Mapas foi lançado nos seguintes países/regiões:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> A partir de 1º de agosto de 2019, o parâmetro **Exibição** define o conteúdo do mapa retornado para as novas regiões/países listados acima. O parâmetro **Exibição** do Azure Mapas (também conhecido como "parâmetro de região do usuário") é um código de país ISO-3166 de duas letras que mostrará os mapas corretos para esse país/região, especificando qual conjunto de conteúdo contestado geopoliticamente é retornado por meio dos serviços do Azure Mapas, incluindo bordas e rótulos exibidos no mapa. 

Certifique-se de configurar o parâmetro **Exibição** conforme necessário para as APIs REST e os SDKs que os serviços estão usando.
  

### <a name="rest-apis"></a>APIs REST
  
Verifique se você configurou o parâmetro Exibição conforme necessário. O parâmetro Exibição especifica qual conjunto de conteúdo contestado geopoliticamente é retornado por meio dos serviços do Azure Mapas. 

Serviços REST do Azure Mapas afetados:
    
 * Obter Peça de Mapa
 * Obter Imagem do Mapa 
 * Obter Pesquisa Difusa
 * Obter POI de Pesquisa
 * Obter Categoria de POI de Pesquisa
 * Obter Pesquisa Próxima
 * Obter Endereço de Pesquisa
 * Obter Endereço de Pesquisa Estruturado
 * Obter Reversão do Endereço de Pesquisa
 * Obter Reversão de Endereço de Pesquisa Entre Ruas
 * Postar Pesquisa Dentro da Geometria
 * Postar lote de endereços de pesquisa
 * Postar lote reverso de endereço de pesquisa
 * Postar Pesquisa ao Longo da Rota
 * Postar lote difuso de pesquisa

 
### <a name="sdks"></a>SDKs

Verifique se você configurou o parâmetro **Exibição** conforme necessário e se tem a versão mais recente do SDK Web e do SDK do Android. SDKs afetados:

 * SDK da Web do Azure Mapas
 * SDK do Android do Azure Mapas

Por padrão, o parâmetro Exibição é definido como **Unificado**, mesmo que você não o tenha definido na solicitação. Determine o local dos usuários. Em seguida, defina o parâmetro **Exibição** corretamente para esse local. Como alternativa, você pode definir "Exibição = Automática", que retornará os dados do mapa com base no endereço IP da solicitação.  O parâmetro **Exibição**  no Azure Mapas deve ser usado em conformidade com as leis aplicáveis, incluindo as leis sobre o mapeamento do país/região em que mapas, imagens e outros dados e conteúdos de terceiros que você está autorizado a acessar por meio do Azure Mapas são disponibilizados.


A tabela a seguir fornece as exibições com suporte.

| Visualizar         | Descrição                            |  Mapas | Search | Controle de mapa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (Exibição Árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (Exibição Argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (Exibição Árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (Exibição Indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (Exibição Árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (Exibição Árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (Exibição Árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (Exibição Árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (Exibição Marroquina)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (Exibição Árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (Exibição Paquistanesa)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (Exibição Árabe)    |   ✓   |        |     ✓          |
| QA           | Qatar (Exibição Árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (Exibição Árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (Exibição Árabe)                   |   ✓   |        |     ✓          |
| YE           | Iêmen (Exibição Árabe)                   |   ✓   |        |     ✓          |
| Auto         | Retornar os dados do mapa com base no endereço IP da solicitação.|   ✓   |    ✓   |     ✓          |
| Unificada      | Exibição Unificada (Outras)                  |   ✓   |   ✓     |     ✓          |
