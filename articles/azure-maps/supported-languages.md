---
title: Suporte de localização em mapas do Azure | Microsoft Docs
description: Saiba mais sobre idiomas com suporte para os serviços nos Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446183"
---
# <a name="localization-support-in-azure-maps"></a>Suporte de localização em mapas do Azure

Mapas do Azure dá suporte a vários idiomas e modos de exibição com base no país/região. Este artigo fornece as exibições para ajudar a orientar a implementação de mapas do Azure e os idiomas com suporte.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte nos Mapas do Azure

Os Mapas do Azure foram traduzidos para variedade de idiomas nos serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| id         | NOME                   |  Mapas | Search | Roteamento | Incidentes de tráfego | Controle de mapa JS | Fuso horário |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Africâner              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Basco                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | Catalão                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Chinês (Tradicional)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | Croata               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Tcheco                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | Holandês                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estoniano               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Francês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Francês (canadense)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galego               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | Grego                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | Hebraico                 |       |    ✓   |         |         ✓         |                |     ✓     |
| hi-IN      | Híndi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonês               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Cazaque                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Espanhol (América Latina) |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Letão                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norueguês bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutral Ground Truth - idiomas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutro verdade - exônimos latinos. Script latino será usado se disponível |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polonês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ro-RO      | Romeno               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Sérvio (cirílico)     |       |    Sérvio (cirílico) (sr-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | Sérvio (latino)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Eslovaco              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Sueco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ucraniano               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnamita             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Mapas do Azure com suporte a modos de exibição

> [!Note]
> Estamos lançando o mapas do Azure nos seguintes países/regiões em 1 de agosto de 2019:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Após 1 de agosto de 2019 **exibição** a configuração de parâmetro definirá o conteúdo do mapa retornado para novos regiões/países listados acima. Incentivamos você a garantir que você configurou o parâmetro de modo de exibição conforme necessário para as APIs REST e SDKs que seus serviços estão usando.
>  
>
>  **APIs de REST:**
>  
>  Certifique-se de que você configurou o parâmetro de modo de exibição conforme necessário. Parâmetro de modo de exibição especifica qual conjunto de conteúdo geopoliticamente contestado é retornado por meio de serviços de mapas do Azure. 
>
>  Serviços REST afetado mapas do Azure:
>    
>    * Obter a peça de mapa
>    * Obter imagem do mapa 
>    * Obter pesquisa difusa
>    * Obter pesquisa de POI
>    * Obter a categoria de POI de pesquisa
>    * Obter pesquisa próximos
>    * Obter o endereço de pesquisa
>    * Obter o endereço de pesquisa estruturado
>    * Obter o endereço de pesquisa inversa
>    * Obter o endereço de pesquisa inversa Rua de cruzada
>    * Pesquisa de postagem dentro de geometria
>    * Versão prévia do lote endereço de pesquisa de postagem
>    * Lançar a versão prévia do lote inversa de endereço de pesquisa
>    * Pesquisa de POST ao longo da rota
>    * Versão prévia do lote difusa de pesquisa de postagem
>
>    
>  **SDKs:**
>
>  Certifique-se de que você configurou o parâmetro de modo de exibição conforme necessário, e você tem a versão mais recente do SDK para Web e do SDK do Android. SDKs afetados:
>
>    * SDK da Web do Azure Mapas
>    * SDK do Android do Azure Mapas


Mapas do Azure **exibição** parâmetro (também chamado de "parâmetro de região do usuário") é uma letra de dois código do país ISO 3166 que mostrará os mapas corretos para esse país/região especificando qual conjunto de geopoliticamente questionadas conteúdo retornado por meio de serviços de mapas do Azure, incluindo bordas e os rótulos exibidos no mapa. 

Por padrão, o parâmetro de modo de exibição é definido como **unificado**, mesmo se você ainda não tiver definido-lo na solicitação. É sua responsabilidade para determinar o local dos seus usuários e, em seguida, defina o parâmetro View corretamente para esse local. Como alternativa, você tem a opção para definir ' modo de exibição = Auto', que retornará os dados de mapa com base no endereço IP da solicitação.  O parâmetro de modo de exibição em mapas do Azure deve ser usado em conformidade com as leis aplicáveis, incluindo aquelas sobre mapeamento do país em que os mapas, imagens e outros conteúdos de terceiros e dados que você está autorizado a acessar por meio do Azure mapas é disponibilizado.


A tabela a seguir fornece exibições com suporte.

| Visualizar         | DESCRIÇÃO                            |  Mapas | Search | Controle de mapa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (exibição árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (exibição Argentinian)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (exibição árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (exibição Indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (exibição árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (exibição árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (exibição árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (exibição árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (exibição Maroko)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (exibição árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (exibição paquistanesa)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (exibição árabe)    |   ✓   |        |     ✓          |
| QA           | Catar (exibição árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (exibição árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (exibição árabe)                   |   ✓   |        |     ✓          |
| YE           | Iêmen (exibição árabe)                   |   ✓   |        |     ✓          |
| Automático         | Retorne os dados de mapa com base no endereço IP da solicitação.|   ✓   |    ✓   |     ✓          |
| Unificado      | Exibição unificada (outros)                  |   ✓   |   ✓     |     ✓          |
