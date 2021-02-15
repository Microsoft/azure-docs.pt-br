---
title: Cobertura de renderização | Mapas do Microsoft Azure
description: Saiba se o Azure Maps renderiza várias regiões com dados detalhados ou simplificados. Veja o nível que ele usa para mapas de bloco de rasterização e bloco de vetor nessas regiões.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 69b122196cbeb7508cddf3a890768f83fdcd4727
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88030921"
---
# <a name="azure-maps-render-coverage"></a>Cobertura de renderização do Mapas do Azure

O Mapas do Azure usa blocos de varredura e blocos de vetor para criar mapas. Na resolução mais baixa, o mundo inteiro se encaixa em um único bloco. Na resolução mais alta, um único bloco representa 38 metros quadrados. Você verá mais detalhes sobre continentes, regiões, cidades e ruas individuais à medida que aplicar zoom no mapa. Para obter mais informações sobre blocos, consulte [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md).

No entanto, o Maps não tem o mesmo nível de informações e precisão para todas as regiões. As tabelas a seguir detalham o nível de informações que você pode renderizar para cada região.

## <a name="legend"></a>Legenda

| Símbolo | Significado |
|--------|---------|
| ✓ | A região é representada com dados detalhados.   |
| Ø | A região é representada com dados simplificados. |


## <a name="africa"></a>África 


| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Argélia                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botsuana                         | ✓ | ✓ |
| Burkina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| Camarões                         | ✓ | ✓ |
| República Centro-Africana         | ✓ | Ø |
| Chade                             | ✓ | Ø |
| Comores                          | ✓ | Ø |
| República Democrática do Congo | ✓ | ✓ |
| Côte d’Ivoire                    | ✓ | Ø |
| Djibuti                         | ✓ | Ø |
| Egito                            | ✓ | ✓ |
| Guiné Equatorial                | ✓ | Ø |
| Eritreia                          | ✓ | Ø |
| Etiópia                         | ✓ | Ø |
| Gabão                            | ✓ | ✓ |
| Gâmbia                           | ✓ | Ø |
| Gana                            | ✓ | ✓ |
| Guiné                           | ✓ | Ø |
| Guiné Bissau                    | ✓ | Ø |
| Quênia                            | ✓ | ✓ |
| Lesoto                          | ✓ | ✓ |
| Libéria                          | ✓ | Ø |
| Líbia                            | ✓ | Ø |
| Madagascar                       | ✓ | Ø |
| Malaui                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritânia                       | ✓ | ✓ |
| Maurício                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marrocos                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namíbia                          | ✓ | ✓ |
| Níger                            | ✓ | ✓ |
| Nigéria                          | ✓ | ✓ |
| Reunião                          | ✓ | ✓ |
| Ruanda                           | ✓ | ✓ |
| Santa Helena, Ascensão e Tristão da Cunha | ✓ | Ø |
| São Tomé and Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Serra Leoa                     | ✓ | ✓ |
| Somália                          | ✓ | ✓ |
| África do Sul                     | ✓ | ✓ |
| Sudão do Sul                      | ✓ | ✓ |
| Sudão                            | ✓ | ✓ |
| Suazilândia                        | ✓ | ✓ |
| República Unida da Tanzânia      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunísia                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zâmbia                           | ✓ | ✓ |
| Zimbábue                         | ✓ | ✓ |

## <a name="americas"></a>Américas

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Anguilla                  | ✓ | ✓ |
| Antígua e Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   | ✓ | ✓ |
| Estado Plurinacional da Bolívia | ✓ | ✓ |
| Bonaire, Santo Eustáquio e Saba | ✓ | ✓ |
| Brasil                    | ✓ | ✓ |
| Canada                    | ✓ | ✓ |
| Ilhas Cayman            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Colômbia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Cuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| República Dominicana        | ✓ | ✓ |
| Equador                   | ✓ | ✓ |
| Ilhas Falkland (Malvinas) | ✓ | ✓ |
| Guiana Francesa             | ✓ | ✓ |
| Groelândia                 | ✓ | Ø |
| Granada                   | ✓ | ✓ |
| Guadalupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guiana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinica                | ✓ | ✓ |
| México                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicarágua                 | ✓ | ✓ |
| Ilhas Marianas do Norte  | ✓ | ✓ |
| Panamá                    | ✓ | ✓ | 
| Paraguai                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Porto Rico               | ✓ | ✓ |
| Quebec (Canadá)           | ✓ | ✓ |
| São Bartolomeu          | ✓ | ✓ |
| São Cristóvão e Nevis     | ✓ | ✓ |
| Santa Lúcia               | ✓ | ✓ |
| Saint Martin (francesa)     | ✓ | ✓ |
| São Pedro e Miquelon | ✓ | ✓ |
| São Vicente e Granadinas | ✓ | ✓ |
| Sint Maarten (holandesa)      | ✓ | ✓ |
| Ilhas Geórgia do Sul e Sandwich do Sul | ✓ | ✓ |
| Suriname                  | ✓ | ✓ |
| Trinidad e Tobago       | ✓ | ✓ |
| Ilhas Turcas e Caicos  | ✓ | ✓ |
| Estados Unidos             | ✓ | ✓ |
| Uruguai                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Ilhas Virgens Britânicas   | ✓ | ✓ |
| Ilhas Virgens Americanas      | ✓ | ✓ |

## <a name="asia"></a>Ásia 

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Afeganistão               |   | Ø |
| Bahrein                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Butão                    |   | Ø |
| Território Britânico do Oceano Índico |   | Ø |
| Brunei                    | ✓ | ✓ |
| Camboja                  |   | Ø |
| China                     |   | Ø |
| Ilhas Cocos (Keeling)   |   | Ø |
| República Democrática Popular da Coreia |   | Ø |
| RAE de Hong Kong             | ✓ | ✓ |
| Índia                     | Ø | ✓ | 
| Indonésia                 | ✓ | ✓ |
| Irã                      |   | Ø |
| Iraque                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japão                     |   | Ø |
| Jordânia                    | ✓ | ✓ |
| Cazaquistão                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Quirguistão                |   | Ø |
| República Democrática do Laos |   | Ø |
| Líbano                   | ✓ | ✓ |
| RAE de Macau                 | ✓ | ✓ |
| Malásia                  | ✓ | ✓ |
| Maldivas                  |   | Ø |
| Mongólia                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Omã                      | ✓ | ✓ |
| Paquistão                  |   | Ø |
| Filipinas               | ✓ | ✓ |
| Catar                     | ✓ | ✓ |
| República da Coreia         | ✓ | Ø |
| Arábia Saudita              | ✓ | ✓ |
| Ilhas Senkaku           |   | ✓ |
| Singapura                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| República Árabe Síria      |   | Ø |
| Taiwan                    | ✓ | ✓ |
| Tadjiquistão                |   | Ø |
| Tailândia                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turcomenistão              |   | Ø |
| Emirados Árabes Unidos      | ✓ | ✓ |
| Territórios Insulares dos Estados Unidos |   | Ø |
| Uzbequistão                |   | Ø |
| Vietnã                   | ✓ | ✓ |
| Iêmen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceania

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Samoa Americana            |   | ✓ |
| Austrália                 | ✓ | ✓ |
| Ilhas Cook              |   | Ø |
| Fiji                      |   | Ø |
| Polinésia Francesa          |   | Ø |
| Guão                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Ilhas Marshall          |   | Ø |
| Micronésia                |   | Ø |
| Nauru                     |   | Ø |
| Nova Caledônia             |   | Ø |
| Nova Zelândia               | ✓ | ✓ |
| Niue                      |   | Ø |
| Ilha Norfolk            |   | Ø |
| Palau                     |   | Ø |
| Papua Nova Guiné          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Ilhas Salomão           |   | Ø|
| Toquelau                   |   | Ø |
| Tonga                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis e Futuna         |   | Ø |


## <a name="europe"></a>Europa

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Albânia                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armênia                   | ✓ | Ø |
| Áustria                   | ✓ | ✓ |
| Azerbaijão                | ✓ | Ø |
| Bielorrússia                   | Ø | ✓ |
| Bélgica                   | ✓ | ✓ |
| Bósnia e Herzegovina        | ✓ | ✓ |
| Bulgária                  | ✓ | ✓ |
| Croácia                   | ✓ | ✓ |
| Chipre                    | ✓ | ✓ |
| República Tcheca            | ✓ | ✓ |
| Dinamarca                   | ✓ | ✓ |
| Estônia                   | ✓ | ✓ |
| Ilhas Faroés             | ✓ | Ø |
| Finlândia                   | ✓ | ✓ |
| França                    | ✓ | ✓ |
| Geórgia                   | ✓ | Ø |
| Alemanha                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grécia                    | ✓ | ✓ |
| Guernsey                  | ✓ | ✓ |
| Hungria                   | ✓ | ✓ |
| Islândia                   | ✓ | ✓ |
| Irlanda                   | ✓ | ✓ |
| Ilha de Man               | ✓ | ✓ |
| Itália                     | ✓ | ✓ |
| Jan Mayen                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Letônia                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Lituânia                 | ✓ | ✓ |
| Luxemburgo                | ✓ | ✓ |
| Macedônia do Norte           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldova                   | ✓ | ✓ |
| Mônaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Países Baixos               | ✓ | ✓ |
| Noruega                    | ✓ | ✓ |
| Polônia                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Romênia                   | ✓ | ✓ |
| Federação Russa        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Sérvia                    | ✓ | ✓ |
| Eslováquia                  | ✓ | ✓ |
| Eslovênia                  | ✓ | ✓ |
| Ilhas Curilas do Sul           | ✓ | ✓ |
| Espanha                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Suécia                    | ✓ | ✓ |
| Suíça               | ✓ | ✓ |
| Turquia                    | ✓ | ✓ |
| Ucrânia                   | ✓ | ✓ |
| United Kingdom            | ✓ | ✓ |
| Cidade do Vaticano              | ✓ | ✓ |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a renderização de Mapas do Azure, consulte [Níveis de Zoom e grade de blocos](zoom-levels-and-tile-grid.md).

Saiba mais sobre [áreas de cobertura para serviço de tráfego do Mapas](routing-coverage.md). 
