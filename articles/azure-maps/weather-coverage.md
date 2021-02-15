---
title: Microsoft Azure mapeia a cobertura dos serviços meteorológicos (versão prévia)
description: Saiba mais sobre a cobertura de serviços meteorológicos do Microsoft Azure Maps (versão prévia)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905478"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Cobertura dos serviços meteorológicos do Azure Maps (versão prévia)

> [!IMPORTANT]
> Os serviços meteorológicos do Azure Maps estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Este artigo fornece informações de cobertura para os [serviços meteorológicos](/rest/api/maps/weather)do Azure Maps. Os serviços de clima do Azure Maps retorna detalhes como blocos de radar, condições do clima atual, previsões do tempo e o clima ao longo de uma rota.

O mapas do Azure não tem o mesmo nível de informações e precisão para todos os países e regiões.

A tabela a seguir fornece informações sobre o tipo de informações de clima que você pode solicitar de cada país/região.

| Símbolo | Significado |
|--------|---------|
|*       |Cobre as condições atuais, a previsão por hora, a previsão de trimestre, a previsão diária, o clima ao longo dos índices diários e de rota. |


## <a name="americas"></a>Américas

| País/Região              |  Blocos satélite | Previsão de minutos, blocos de radar | Alertas de clima severos | Outros |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antártica                               | ✓ |   | |✓|
| Antígua e Barbuda                      | ✓ |   | |✓| 
| Argentina                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahamas                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermuda                                  | ✓ |   | |✓| 
| Bolívia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brasil                                   | ✓ |   | ✓ |✓| 
| Ilhas Virgens Britânicas                   | ✓ |   | |✓| 
| Canada                                   | ✓ | ✓ | ✓ | ✓| 
| Ilhas Cayman                           | ✓ |   | |✓| 
| Chile                                    | ✓ |   | |✓| 
| Colômbia                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Cuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominica                                 | ✓ |   | |✓| 
| República Dominicana                       | ✓ |   | |✓| 
| Equador                                  | ✓ |   | |✓| 
| El Salvador                              | ✓ |   | |✓| 
| Ilhas Malvinas                         | ✓ |   | |✓| 
| Guiana Francesa                            | ✓ |   | |✓| 
| Groelândia                                | ✓ |   | |✓| 
| Granada                                  | ✓ |   | |✓| 
| Guadalupe                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guiana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamaica                                  | ✓ |   | |✓| 
| Martinica                               | ✓ |   | |✓| 
| México                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nicarágua                                | ✓ |   | |✓| 
| Panamá                                   | ✓ |   | |✓| 
| Paraguai                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Porto Rico                              | ✓ |   | ✓ |✓| 
| São Bartolomeu                         | ✓ |   | |✓| 
| São Cristóvão e Nevis                    | ✓ |   | |✓| 
| Santa Lúcia                              | ✓ |   | |✓| 
| São Martinho                             | ✓ |   | |✓| 
| São Pedro e Miquelon                | ✓ |   | |✓| 
| São Vicente e Granadinas         | ✓ |   | |✓| 
| Santo Eustáquio                           | ✓ |   | |✓|  
| São Martinho                             | ✓ |   | |✓| 
| Ilhas Geórgia do Sul e Sandwich do Sul | ✓ |   | |✓| 
| Suriname                                 | ✓ |   | |✓| 
| Trinidad e Tobago                      | ✓ |   | |✓| 
| Ilhas Turcas e Caicos                 | ✓ |   | |✓| 
| Territórios Insulares Ilhas Menores Distantes dos EUA                    | ✓ |   | |✓| 
| EUA Virgens Americanas                      | ✓ |   | ✓|✓| 
| Estados Unidos                            | ✓ | ✓ | ✓| ✓| 
| Uruguai                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Oriente Médio e África

| País/Região              |  Blocos satélite | Previsão de minutos, blocos de radar | Alertas de clima severos | Outros | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Argélia                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrein                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botsuana                    | ✓               |                              |     |  ✓| 
| Ilha Bouvet               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Camarões                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| República Centro-Africana    | ✓               |                              |     |  ✓| 
| Chade                        | ✓               |                              |     |  ✓| 
| Comores                     | ✓               |                              |     |  ✓| 
| Congo (RDC)                 | ✓               |                              |     |  ✓|
| Côte d’Ivoire               | ✓               |                              |     |  ✓| 
| Djibuti                    | ✓               |                              |     |  ✓| 
| Egito                       | ✓               |                              |     |  ✓| 
| Guiné Equatorial           | ✓               |                              |     |  ✓| 
| Eritreia                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Etiópia                    | ✓               |                              |     |  ✓| 
| Territórios Austrais Franceses | ✓               |                              |     |  ✓| 
| Gabão                       | ✓               |                              |     |  ✓| 
| Gâmbia                      | ✓               |                              |     |  ✓| 
| Gana                       | ✓               |                              |     |  ✓| 
| Guiné                      | ✓               |                              |     |  ✓| 
| Guiné Bissau               | ✓               |                              |     |  ✓| 
| Irã                        | ✓               |                              |     |  ✓| 
| Iraque                        | ✓               |                              |     |  ✓| 
| Israel                      | ✓               |                              |   ✓   |  ✓| 
| Jordânia                      | ✓               |                              |     |  ✓| 
| Quênia                       | ✓               |                              |     |  ✓| 
| Kuwait                      | ✓               |                              |     |  ✓| 
| Líbano                     | ✓               |                              |     |  ✓| 
| Lesoto                     | ✓               |                              |     |  ✓| 
| Libéria                     | ✓               |                              |     |  ✓| 
| Líbia                       | ✓               |                              |     |  ✓| 
| Madagascar                  | ✓               |                              |     |  ✓| 
| Malaui                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauritânia                  | ✓               |                              |     |  ✓| 
| Maurício                   | ✓               |                              |     |  ✓| 
| Mayotte                     | ✓               |                              |     |  ✓| 
| Marrocos                     | ✓               |                              |     |  ✓| 
| Moçambique                  | ✓               |                              |     |  ✓| 
| Namíbia                     | ✓               |                              |     |  ✓| 
| Níger                       | ✓               |                              |     |  ✓| 
| Nigéria                     | ✓               |                              |     |  ✓| 
| Omã                        | ✓               |                              |     |  ✓| 
| Autoridade Palestina       | ✓               |                              |     |  ✓| 
| Catar                       | ✓               |                              |     |  ✓| 
| Reunião                     | ✓               |                              |     |  ✓| 
| Ruanda                      | ✓               |                              |     |  ✓| 
| Santa Helena, Ascensão e Tristão da Cunha        | ✓               |            |     |  ✓| 
| São Tomé and Príncipe       | ✓               |                              |     |  ✓| 
| Arábia Saudita                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seicheles                  | ✓               |                              |     |  ✓| 
| Serra Leoa                | ✓               |                              |     |  ✓| 
| Somália                     | ✓               |                              |     |  ✓| 
| África do Sul                | ✓               |                              |     |  ✓| 
| Sudão do Sul                 | ✓               |                              |     |  ✓| 
| Sudão                       | ✓               |                              |     |  ✓| 
| Síria                       | ✓               |                              |     |  ✓| 
| Tanzânia                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunísia                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Emirados Árabes Unidos        | ✓               |                              |     |  ✓| 
| Iêmen                       | ✓               |                              |     |  ✓| 
| Zâmbia                      | ✓               |                              |     |  ✓| 
| Zimbábue                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Pacífico Asiático

| País/Região              |  Blocos satélite | Previsão de minutos, blocos de radar | Alertas de clima severos | Outros |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afeganistão                       | ✓ |   | | ✓| 
| Samoa Americana                    | ✓ |   |  ✓| ✓| 
| Austrália                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesh                        | ✓ |   | | ✓| 
| Butão                            | ✓ |   | | ✓| 
| Território Britânico do Oceano Índico    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Camboja                          | ✓ |   | | ✓| 
| China                             | ✓ | ✓ |  ✓ | ✓| 
| Ilha Christmas                  | ✓ |   | | ✓| 
| Ilhas Cocos (Keeling)           | ✓ |   | | ✓| 
| Ilhas Cook                      | ✓ |   | | ✓| 
| Fiji                              | ✓ |   | | ✓| 
| Polinésia Francesa                  | ✓ |   | | ✓| 
| Guão                              | ✓ |   |  ✓| ✓| 
| Ilhas Heard e McDonald | ✓ |   | | ✓| 
| RAE de Hong Kong                     | ✓ |   | | ✓| 
| Índia                             | ✓ |   | | ✓| 
| Indonésia                         | ✓ |   | | ✓| 
| Japão                             | ✓ | ✓ |  ✓| ✓| 
| Cazaquistão                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Coreia do Sul                             | ✓ | ✓ | ✓ |  ✓| 
| Quirguistão                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| RAE de Macau                         | ✓ |   | | ✓| 
| Malásia                          | ✓ |   | | ✓| 
| Maldivas                          | ✓ |   | | ✓| 
| Ilhas Marshall                  | ✓ |   | ✓ | ✓| 
| Micronésia                        | ✓ |   | ✓ | ✓| 
| Mongólia                          | ✓ |   | | ✓| 
| Myanmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Nova Caledônia                     | ✓ |   | | ✓| 
| Nova Zelândia                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Ilha Norfolk                    | ✓ |   | | ✓| 
| Coreia do Norte                       | ✓ |   | | ✓| 
| Ilhas Marianas do Norte          | ✓ |   | ✓ | ✓| 
| Paquistão                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papua Nova Guiné                  | ✓ |   | | ✓| 
| Filipinas                       | ✓ |   | ✓ | ✓| 
| Ilhas Pitcairn                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapura                         | ✓ |   | | ✓| 
| Ilhas Salomão                   | ✓ |   | | ✓| 
| Sri Lanka                         | ✓ |   | | ✓| 
| Taiwan                            | ✓ |   | | ✓| 
| Tadjiquistão                        | ✓ |   | | ✓| 
| Tailândia                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Toquelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turcomenistão                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Uzbequistão                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietnã                           | ✓ |   | | ✓| 
| Wallis e Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| País/Região              |  Blocos satélite | Previsão de minutos, blocos de radar | Alertas de clima severos | Outros | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albânia                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Armênia                | ✓ |   | | ✓| 
| Áustria                | ✓ | ✓ | ✓ | ✓|
| Azerbaijão             | ✓ |   | | ✓| 
| Bielorrússia                | ✓ |   | | ✓| 
| Bélgica                | ✓ | ✓ |  ✓| ✓| 
| Bósnia e Herzegovina | ✓ | ✓ | ✓ | ✓| 
| Bulgária               | ✓ |   |  ✓| ✓| 
| Croácia                | ✓ | ✓ |  ✓| ✓| 
| Chipre                 | ✓ |   | ✓ | ✓| 
| Tchéquia                | ✓ | ✓ | ✓ | ✓| 
| Dinamarca                | ✓ | ✓ | ✓ | ✓| 
| Estônia                | ✓ | ✓ |  ✓| ✓| 
| Ilhas Faroés          | ✓ |   | | ✓| 
| Finlândia                | ✓ | ✓ | ✓ | ✓| 
| França                 | ✓ | ✓ | ✓ | ✓| 
| Geórgia                | ✓ |   | | ✓| 
| Alemanha                | ✓ | ✓ | ✓ | ✓| 
| Gibraltar              | ✓ | ✓ | | ✓| 
| Grécia                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Hungria                | ✓ | ✓ |  ✓| ✓| 
| Islândia                | ✓ |   | ✓ | ✓| 
| Irlanda                | ✓ | ✓ |  ✓| ✓| 
| Itália                  | ✓ |   |  ✓| ✓|
| Ilha de Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosovo                 | ✓ |   |  ✓| ✓| 
| Letônia                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Lituânia              | ✓ |   | ✓ | ✓| 
| Luxemburgo             | ✓ | ✓ |  ✓| ✓| 
| Macedônia do Norte        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Moldova                | ✓ | ✓ | ✓ | ✓| 
| Mônaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegro             | ✓ | ✓ |  ✓| ✓| 
| Países Baixos            | ✓ | ✓ |  ✓| ✓| 
| Noruega                 | ✓ | ✓ |  ✓| ✓| 
| Polônia                 | ✓ | ✓ |  ✓| ✓| 
| Portugal               | ✓ | ✓ |  ✓| ✓| 
| Romênia                | ✓ | ✓ |  ✓| ✓| 
| Rússia                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Sérvia                 | ✓ | ✓ |  ✓| ✓| 
| Eslováquia               | ✓ | ✓ |  ✓| ✓| 
| Eslovênia               | ✓ | ✓ |  ✓| ✓| 
| Espanha                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Suécia                 | ✓ | ✓ |  ✓| ✓| 
| Suíça            | ✓ | ✓ | ✓| ✓| 
| Turquia                 | ✓ |   | | ✓| 
| Ucrânia                | ✓ |   | | ✓| 
| Reino Unido         | ✓ | ✓ | ✓| ✓| 
| Cidade do Vaticano           | ✓ |   |✓ | ✓|