---
title: Cobertura de geocodificação no serviço de pesquisa do Microsoft Azure Maps
description: Veja quais regiões o Azure Maps Search cobre. As categorias de geocodificação incluem pontos de endereço, números de casa, nível de rua, nível de cidade e pontos de interesse.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/28/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3cf27319fa84ff5d693e74e90664b7a86f4d934a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92897204"
---
# <a name="azure-maps-geocoding-coverage"></a>Cobertura da geocodificação dos Mapas do Azure

O serviço de [pesquisa](/rest/api/maps/search) do Azure Maps dá suporte a geocodificação, o que significa que sua solicitação de API pode ter termos de pesquisa, como um endereço ou o nome de um local, e retorna o resultado como coordenadas de latitude e longitude. Por exemplo, a [API obter endereço de pesquisa](/rest/api/maps/search/getsearchaddress) do Azure Maps recebe consultas que contêm informações de localização e retorna resultados como coordenadas de latitude e longitude.

No entanto, o [serviço de pesquisa](/rest/api/maps/search) do Azure Maps não tem o mesmo nível de informações e precisão para todas as regiões e países. Use este artigo para determinar que tipo de locais você pode procurar de forma confiável em cada região.

A capacidade de geocódigo em um país/região depende da cobertura de dados de estrada e da precisão geocodificação do serviço de geocodificação. As categorizações a seguir são usadas para especificar o nível de suporte de geocodificação em cada país/região.

* **Pontos de endereço** -os dados de endereço podem ser resolvidos para coordenadas de latitude/longitude dentro da remessa de endereço (limite de propriedade). Os pontos de endereço costumam ser conhecidos como "Rooftop" preciso, que é o nível mais alto de precisão disponível para endereços.
* **Números residenciais** - endereços são interpolados para uma coordenada de latitude/longitude na rua.
* **Nível da rua** - endereços são resolvidos para a coordenada de latitude/longitude da rua que contém o endereço. O número da casa não pode ser processado.
* **Nível de cidade** - nomes de locais de cidade que são suportados.

## <a name="americas"></a>Américas

| País/Região                                       | Pontos de endereço | Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antártica                                          |                 |                |              |      ✓     |          ✓         |
| Antígua e Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Santo Eustáquio e Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canadá                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Cayman                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colômbia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Equador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Malvinas                                    |                 |                |              |      ✓     |          ✓         |
| Guiana Francesa                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Granada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guão                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guiana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| México                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicarágua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panamá                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguai                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Porto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| São Bartolomeu                                    |                 |                |       ✓      |      ✓     |          ✓         |
| São Cristóvão e Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Lúcia                                         |                 |                |              |      ✓     |          ✓         |
| São Martinho                                        |                 |                |       ✓      |      ✓     |          ✓         |
| São Pedro e Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| São Vicente e Granadinas                    |                 |                |              |      ✓     |          ✓         |
| São Martinho                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Geórgia do Sul e Sandwich do Sul        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad e Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Insulares dos Estados Unidos                |                 |                |              |      ✓     |          ✓         |
| Estados Unidos da América                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguai                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Virgens Britânicas                              |                 |                |              |      ✓     |          ✓         |
| EUA Virgens Americanas                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Pacífico Asiático

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Americana                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Austrália                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Butão                                              |                 |                |              |      ✓     |          ✓         |
| Território Britânico do Oceano Índico                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Camboja                                            |                 |                |              |      ✓     |          ✓         |
| China                                               |                 |                |              |      ✓     |          ✓         |
| Ilha Christmas                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Ilhas Cocos (Keeling)                             |                 |                |              |      ✓     |          ✓         |
| Comores                                             |                 |                |              |      ✓     |          ✓         |
| Ilhas Cook                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Polinésia Francesa                                    |                 |                |              |      ✓     |          ✓         |
| Ilhas Heard e McDonald                   |                 |                |              |      ✓     |          ✓         |
| RAE de Hong Kong                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Índia                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japão                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Coreia do Sul                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| RAE de Macau                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malásia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésia                                          |                 |                |              |      ✓     |          ✓         |
| Mongólia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nova Caledônia                                       |                 |                |              |      ✓     |          ✓         |
| Nova Zelândia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Ilha Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Coreia do Norte                                         |                 |                |              |      ✓     |          ✓         |
| Ilhas Marianas do Norte                            |                 |                |       ✓      |      ✓     |          ✓         |
| Paquistão                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua Nova Guiné                                    |                 |                |              |      ✓     |          ✓         |
| Filipinas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Senkaku                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapura                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Salomão                                     |                 |                |              |      ✓     |          ✓         |
| Ilhas Curilas do Sul                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tailândia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Toquelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Turcas e Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnã                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis e Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albânia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armênia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Áustria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaijão                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Bélgica                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bósnia e Herzegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgária                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bielorrússia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croácia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chipre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| República Tcheca                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dinamarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estônia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Faroés                                       |                 |                |              |      ✓     |          ✓         |
| Finlândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| França                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Geórgia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Alemanha                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grécia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groelândia                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hungria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilha de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Itália                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Cazaquistão                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Quirguistão                                          |                 |                |              |      ✓     |          ✓         |
| Letônia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituânia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedônia do Norte                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mônaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Países Baixos                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noruega                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polônia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Açores e Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Romênia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federação Russa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sérvia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslováquia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslovênia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espanha                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suécia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suíça                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadjiquistão                                          |                 |                |              |      ✓     |          ✓         |
| Turquia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turcomenistão                                        |                 |                |              |      ✓     |          ✓         |
| Ucrânia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Reino Unido                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbequistão                                          |                 |                |              |      ✓     |          ✓         |
| Cidade do Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="middle-east-and-africa"></a>Oriente Médio e África

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afeganistão                                         |                 |                |              |      ✓     |          ✓         |
| Argélia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrein                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsuana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Ilha Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camarões                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| República Centro-Africana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Chade                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| República Democrática do Congo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egito                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| República da Guiné Equatorial                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritreia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiópia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Austrais Franceses|                        |                |              |      ✓     |          ✓         |
| Gabão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gâmbia                                              |                 |                |              |      ✓     |          ✓         |
| Gana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irã                                                |                 |                |              |      ✓     |          ✓         |
| Iraque                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordânia                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Quênia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Líbano                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesoto                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Líbia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malaui                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldivas                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Marshall                                    |                 |                |              |      ✓     |          ✓         |
| Mauritânia                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurício                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marrocos                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Moçambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namíbia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Níger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Omã                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Catar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Reunião                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Helena                                        |                 |                |              |      ✓     |          ✓         |
| Arábia Saudita                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seicheles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Serra Leoa                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somália                                             |                 |                |              |      ✓     |          ✓         |
| África do Sul                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudão do Sul                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazilândia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Síria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé and Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzânia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunísia                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emirados Árabes Unidos                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Iêmen                                               |                 |                |              |      ✓     |          ✓         |
| Zâmbia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbábue                                            |                 |                |       ✓      |      ✓     |          ✓         |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a geocodificação do Azure Maps:
> [!div class="nextstepaction"]
> [Serviço Pesquisa do Azure Mapas](/rest/api/maps/search)