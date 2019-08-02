---
title: Filtragem geográfica em um domínio do Azure Front Door Service | Microsoft Docs
description: Neste artigo, você aprenderá sobre a política de filtragem geográfica do Azure Front Door Service
services: frontdoor
documentationcenter: ''
author: KumudD
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 7ad2d181b6343644205c58ab1d5fe83dc25542d4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846419"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>O que é a filtragem geográfica em um domínio do Azure Front Door?

Por padrão, o Azure Front Door Service responde às solicitações de usuário, independentemente do local do usuário que faz a solicitação. No entanto, em alguns casos, é possível restringir o acesso ao conteúdo dos aplicativos Web por país/região. O serviço WAF (firewall do aplicativo Web) no Front Door permite que você defina uma política usando regras personalizadas de acesso para um caminho específico no seu ponto de extremidade para permitir ou bloquear o acesso de países/regiões específicos. 

Uma política de WAF geralmente inclui um conjunto de regras personalizadas. Uma regra é composta de condições de correspondência, uma ação e uma prioridade. Na condição de correspondência, você define uma variável de correspondência, operador e valor de correspondência.  Para a regra de filtragem geográfica, a variável de correspondência é REMOTE_ADDR, o operador é GeoMatch, o valor é o código de país de duas letras. Você pode combinar uma condição GeoMatch e uma condição de correspondência de cadeia de caracteres REQUEST_URI para criar uma regra de filtragem geográfica com base no caminho.

Você pode configurar uma política de filtragem geográfica para seu Front Door usando o [Azure PowerShell](front-door-tutorial-geo-filtering.md) ou usando nosso [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Referência do código do país

|Código do país | Nome do país |
| ----- | ----- |
| AD | Andorra |
| AE | Emirados Árabes Unidos|
| AF | Afeganistão|
| AG | Antígua e Barbuda|
| AL | Albânia|
| AM | Armênia|
| AO | Angola|
| AR | Argentina|
| AS | Samoa Americana|
| AT | Áustria|
| AU | Austrália|
| AZ | Azerbaijão|
| BA | Bósnia e Herzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Bélgica|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | São Bartolomeu|
| BN | Brunei Darussalam|
| BO | Bolívia|
| BR | Brasil|
| BS | Bahamas|
| BT | Butão|
| BW | Botsuana|
| BY | Belarus|
| BZ | Belize|
| CA | Canadá|
| CD | República Democrática do Congo|
| CF | República Centro-Africana|
| CH | Suíça|
| CI | Costa do Marfim|
| CL | Chile|
| CM | Camarões|
| CN | China|
| CO | Colômbia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Chipre|
| CZ | República Tcheca|
| DE | Alemanha|
| DK | Dinamarca|
| DO | República Dominicana|
| DZ | Argélia|
| EC | Equador|
| EE | Estônia|
| EG | Egito|
| ES | Espanha|
| ET | Etiópia|
| FI | Finlândia|
| FJ | Fiji|
| FM | Micronésia, Estados Federados da|
| FR | França|
| GB | Reino Unido|
| GE | Geórgia|
| GF | Guiana Francesa|
| GH | Gana|
| GN | Guiné|
| GP | Guadalupe|
| GR | Grécia|
| GT | Guatemala|
| GY | Guiana|
| HK | RAE de Hong Kong|
| HN | Honduras|
| HR | Croácia|
| HT | Haiti|
| HU | Hungria|
| ID | Indonésia|
| IE | Irlanda|
| IL | Israel|
| IN | Índia|
| IQ | Iraque|
| IR | Irã, República Islâmica do|
| IS | Islândia|
| IT | Itália|
| JM | Jamaica|
| JO | Jordânia|
| JP | Japão|
| KE | Quênia|
| KG | Quirguistão|
| KH | Camboja|
| KI | Kiribati|
| KN | São Cristóvão e Névis|
| KP | Coreia, República Democrática Popular da|
| KR | Coreia, República da|
| KW | Kuwait|
| KY | Ilhas Cayman|
| KZ | Cazaquistão|
| LA | República Democrática do Laos|
| LB | Líbano|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Libéria|
| LS | Lesoto|
| LT | Lituânia|
| LU | Luxemburgo|
| LV | Letônia|
| LY | Líbia |
| MA | Marrocos|
| MD | Moldávia, República da|
| MG | Madagascar|
| MK | Macedônia do Norte|
| ML | Mali|
| MM | Myanmar|
| MN | Mongólia|
| MO | RAE de Macau|
| MQ | Martinica|
| MR | Mauritânia|
| MT | Malta|
| MV | Maldivas|
| MW | Malaui|
| MX | México|
| MY | Malásia|
| MZ | Moçambique|
| ND | Namíbia|
| NE | Níger|
| NG | Nigéria|
| NI | Nicarágua|
| NL | Países Baixos|
| NÃO | Noruega|
| NP | Nepal|
| NR | Nauru|
| NZ | Nova Zelândia|
| OM | Omã|
| PA | Panamá|
| PE | Peru|
| PH | Filipinas|
| PK | Paquistão|
| PL | Polônia|
| PR | Porto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguai|
| QA | Catar|
| RE | Reunião|
| RO | Romênia|
| RS | Sérvia|
| RU | Federação Russa|
| RW | Ruanda|
| SA | Arábia Saudita|
| SD | Sudão|
| SE | Suécia|
| SG | Singapura|
| SI | Eslovênia|
| SK | Eslováquia|
| SN | Senegal|
| SO | Somália|
| SR | Suriname|
| SS | Sudão do Sul|
| SV | El Salvador|
| SY | República Árabe Síria|
| SZ | Suazilândia|
| TC | Ilhas Turks e Caicos|
| TG | Togo|
| TH | Tailândia|
| TN | Tunísia|
| TR | Turquia|
| TT | Trinidad e Tobago|
| TW | Taiwan|
| TZ | Tanzânia, República Unida da|
| UA | Ucrânia|
| UG | Uganda|
| EUA | Estados Unidos|
| UY | Uruguai|
| UZ | Uzbequistão|
| VC | São Vincent e Granadinas|
| VE | Venezuela|
| VG | Ilhas Virgens Britânicas|
| VI | Ilhas Virgens Americanas|
| VN | Vietnã|
| ZA | África do Sul|
| ZM | Zâmbia|
| ZW | Zimbábue|

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [segurança da camada de aplicativo com o Front Door](front-door-application-security.md).
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
