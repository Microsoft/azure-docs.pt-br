---
title: Preços para ofertas de máquina virtual | Azure Marketplace
description: Explica os três métodos para especificar o preço das ofertas de máquina virtual.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: e398b43e679fb6420c2256e77d34359ae537ac1c
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868753"
---
<a name="pricing-for-virtual-machine-offers"></a>Preços das ofertas de máquina virtual
==================================

Há três maneiras de especificar preços para as ofertas de máquina virtual: preço de núcleo personalizado, preço por núcleo e preço de planilha.


<a name="customized-core-pricing"></a>Preço de núcleo personalizado
-----------------------

O preço é específico para cada combinação de núcleo e região. Cada região na lista de vendas deve ser especificada na seção **virtualMachinePricing**/**regionPrices** da definição.  Use os códigos de moeda corretos de cada [região](#regions) na solicitação.  O exemplo a seguir demonstra esses requisitos:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Preço por núcleo
----------------

Nesse caso, os editores especificam um preço em USD para a SKU e todos os outros preços são gerados automaticamente. O preço por núcleo é especificado no parâmetro **único** na solicitação.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Preço de planilha
-------------------

O editor também pode carregar a planilha de preços em um local de armazenamento temporário e incluir o URI na solicitação como outros artefatos de arquivo. A planilha é carregada, movida para avaliar o cronograma de preços especificado e, por fim, atualiza a oferta com as informações sobre preços. As solicitações subsequentes de GET para a oferta retornarão o URI da planilha e os preços avaliados para a região.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>Novos tamanhos de núcleo adicionados em 7/2/2019
---------------------------

Os editores de VM foram notificados em 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquina virtual do Azure (com base no número de núcleos).  Os novos preços são para os tamanhos de núcleos 10, 44, 48, 60, 120, 208 e 416.  Para a VM existente oferece novos preços para esses tamanhos de núcleos calculados automaticamente com base nos preços atuais.  Os editores têm até 1º de agosto de 2019 para examinar os preços adicionais e fazer as alterações desejadas.  Após essa data, se ainda não tiver sido publicado novamente pelo Publicador, os preços calculados automaticamente para esses novos tamanhos de núcleo entrarão em vigor.


<a name="regions"></a>Regions
-------

A tabela a seguir mostra as diferentes regiões que você pode especificar para preço de núcleo personalizado e os códigos de moeda correspondentes.

| **Região** | **Nome**             | **Código de moeda** |
|------------|----------------------|-------------------|
| DZ         | Argélia              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Austrália            | AUD               |
| AT         | Áustria              | EUR               |
| BH         | Bahrein              | BHD               |
| BY         | Belarus              | RUB               |
| BE         | Bélgica              | EUR               |
| BR         | Brasil               | USD               |
| BG         | Bulgária             | BGN               |
| CA         | Canadá               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Colômbia             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Croácia              | HRK               |
| CY         | Chipre               | EUR               |
| CZ         | República Tcheca       | CZK               |
| DK         | Dinamarca              | DKK               |
| DO         | República Dominicana   | USD               |
| EC         | Equador              | USD               |
| EG         | Egito                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estônia              | EUR               |
| FI         | Finlândia              | EUR               |
| FR         | França               | EUR               |
| DE         | Alemanha              | EUR               |
| GR         | Grécia               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | RAE de Hong Kong        | HKD               |
| HU         | Hungria              | HUF               |
| IS         | Islândia              | ISK               |
| IN         | Índia                | INR               |
| ID         | Indonésia            | IDR               |
| IE         | Irlanda              | EUR               |
| IL         | Israel               | ILS               |
| IT         | Itália                | EUR               |
| JP         | Japão                | JPY               |
| JO         | Jordânia               | JOD               |
| KZ         | Cazaquistão           | KZT               |
| KE         | Quênia                | KES               |
| KR         | Coreia do Sul                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Letônia               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Lituânia            | EUR               |
| LU         | Luxemburgo           | EUR               |
| MK         | Macedônia do Norte      | MKD               |
| MY         | Malásia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | México               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marrocos              | MAD               |
| NL         | Países Baixos          | EUR               |
| NZ         | Nova Zelândia          | NZD               |
| NG         | Nigéria              | NGN               |
| NÃO         | Noruega               | NOK               |
| OM         | Omã                 | OMR               |
| PK         | Paquistão             | PKR               |
| PA         | Panamá               | USD               |
| PY         | Paraguai             | PYG               |
| PE         | Peru                 | PEN               |
| PH         | Filipinas          | PHP               |
| PL         | Polônia               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Porto Rico          | USD               |
| QA         | Catar                | QAR               |
| RO         | Romênia              | RON               |
| RU         | Rússia               | RUB               |
| SA         | Arábia Saudita         | SAR               |
| RS         | Sérvia               | RSD               |
| SG         | Singapura            | SGD               |
| SK         | Eslováquia             | EUR               |
| SI         | Eslovênia             | EUR               |
| ZA         | África do Sul         | ZAR               |
| ES         | Espanha                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Suécia               | SEK               |
| CH         | Suíça          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Tailândia             | THB               |
| TT         | Trinidad e Tobago  | TTD               |
| TN         | Tunísia              | TND               |
| TR         | Turquia               | TRY               |
| UA         | Ucrânia              | UAH               |
| AE         | Emirados Árabes Unidos | EUR               |
| GB         | Reino Unido       | GBP               |
| EUA         | Estados Unidos        | USD               |
| UY         | Uruguai              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
