---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779231"
---
Essa categoria de entidade inclui informações financeiras e formulários oficiais de identificação. Disponível a partir da versão do modelo `2019-10-01` . Os subtipos são listados abaixo. 

### <a name="financial-account-identification"></a>Identificação da conta financeira

| Nome do subtipo               | Descrição                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Número do banco ABA        | Números de roteamento de trânsito da ABA (Associação Americana do Banker).                  |
| Código SWIFT                 | Códigos SWIFT para informações de instrução de pagamento.                           |
| Cartão de crédito                | Números de cartão de crédito.                                                       |
| IBAN (Número de Conta Bancária Internacional)                  | Códigos de IBAN para informações de instrução de pagamento.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identificação específica do governo e do país/região

> [!NOTE]
> As seguintes entidades financeiras e específicas do país não são retornadas com o `domain=phi` parâmetro:
> * Números de passaporte
> * IDs de imposto

As seguintes entidades são agrupadas e listadas por país:

Argentina
* Número de identidade nacional da Argentina (DNI)

Áustria
* Cartão de identidade da Áustria
* Número de identificação do imposto da Áustria
* Número de IVA (imposto sobre valor agregado) da Áustria

Austrália
* Número da conta bancária da Austrália
* Número de negócios australiano
* Número da empresa australiana
* Número da licença do driver da Austrália
* Número da conta médica da Austrália
* Número do passaporte da Austrália
* Número do arquivo de imposto da Austrália

Bélgica
* Número nacional da Bélgica
* Número de imposto de valor agregado belga

Brasil 
* CNPJ (número da entidade legal) do Brasil
* Número do CPF do Brasil
* Cartão de ID nacional do Brasil (RG)

Bulgária
* Número civil uniforme da Bulgária

Canadá
* Número da conta bancária do Canadá
* Número da licença do driver do Canadá
* Número de Serviço de Integridade do Canadá
* Número do passaporte do Canadá
* Número de identificação de integridade pessoal do Canadá (PHIN)
* Número de seguro social do Canadá

Chile
* Número do cartão de identidade 

China
* Número PRC (cartão de identidade residente) da China

Croácia
* Número do cartão de identidade da Croácia
* Número do cartão da ID nacional da Croácia
* Número da OIB (identificação pessoal da Croácia)

Chipre
* Número do cartão de identidade do Chipre
* Número de identificação do imposto do Chipre

República Tcheca
* Número de identidade pessoal tcheco

Dinamarca
* Número de identificação pessoal da Dinamarca

Estônia
* Estônia código de identificação pessoal

União Europeia (UE)
* Número do cartão de débito da UE
* Número de Carteira de Habilitação da UE
* Número de Identificação Nacional da UE
* Número de Passaporte da UE
* Número do Seguro Social (SSN) ou ID Equivalente da UE
* Número de Identificação Tributária da UE (TIN)

Finlândia
* Número de seguro de saúde da Finlândia Europeu
* ID nacional da Finlândia
* Número do passaporte da Finlândia

França
* Número da licença do driver da França
* Número de seguro de saúde da França
* Placa de ID nacional da França (CNI)
* Número do Passport da França
* INSEE (número de seguro social da França)
* Número de identificação do imposto da França (Numéro SPI)
* Número de imposto agregado do valor da França

Alemanha
* Número da licença do driver alemão
* Número do cartão de identidade da Alemanha
* Número do Passport alemão
* Número de identificação do imposto da Alemanha
* Número do imposto sobre valor agregado da Alemanha

Grécia 
* Número do cartão da ID nacional da Grécia
* Número de identificação do imposto da Grécia

RAE de Hong Kong
* Número de HKID (placa de identidade de Hong Kong)

Hungria
* Número de identificação nacional da Hungria
* Número de identificação do imposto Hungria
* Número de imposto de valor agregado Hungria

Índia
* Número de conta permanente da Índia (PAN)
* Número da Aadhaar (identificação exclusiva da Índia)

Indonésia
* Número do KTP (cartão de identidade da Indonésia)

Irlanda
* Número do PPS (serviço público pessoal) da Irlanda

Israel
* ID nacional de Israel
* Número da conta bancária de Israel

Itália
* ID da licença do driver da Itália
* Código fiscal da Itália
* Número de imposto adicionado ao valor da Itália

Japão
* Número da conta bancária do Japão
* Número da licença do driver do Japão
* Meu número pessoal do japonês
* Japonês meu número corporativo
* Número de registro residente do Japão
* Número de Cartão de Residência Japonês
* Número de seguro social do Japão (SIN)
* Número do passaporte do Japão

Letônia
* Código pessoal da Letônia

Lituânia
* Código pessoal da Lituânia

Luxemburg
* Número de identificação nacional do Luxemburg (pessoas naturais)
* Número de identificação nacional do Luxemburg (pessoas não naturais)

Malásia
* Número do cartão de identidade da Malásia

Malta
* Número do cartão de identidade de Malta
* Número de identificação do imposto de Malta

Países Baixos
* Número do serviço do cidadão (BSN) dos Países Baixos
* Número de identificação do imposto Holanda
* Número de imposto adicionado valor de países baixos

Nova Zelândia
* Número da conta bancária da Nova Zelândia
* Número da licença do driver da Nova Zelândia
* Número de receita Inland da Nova Zelândia
* Ministério da Nova Zelândia do número de saúde
* Número de Welfare social da Nova Zelândia

Noruega
* Número de identidade Noruega

Filipinas
* Filipinas número de ID de multipropósito unificado

Polônia
* Cartão de identidade da Polônia
* ID nacional da Polônia (PESEL)
* Número do Passport da Polônia
* Número de REGON da Polônia
* Número de identificação do imposto da Polônia

Portugal 
* Número do cartão do cidadão de Portugal
* Número de identificação do imposto de Portugal

Romênia
* CNP (código numérico pessoal) da Romênia

Rússia
* Número do Passport russo (doméstico)
* Número do Passport russo (internacional)

Arábia Saudita
* ID nacional da Arábia Saudita

Singapura
* Número do cartão de ID de Registro Nacional de Cingapura (NRIC)

Eslováquia 
* Número pessoal da Eslováquia

Eslovênia
* Número de identificação do imposto da Eslovênia
* Número de cidadão mestre exclusivo da Eslovênia

África do Sul
* Número de identificação da África do Sul

Coreia do Sul
* Número de registro residente da Coreia do Sul

Espanha 
* DNI da Espanha
* Número do seguro social da Espanha (SSN)
* Número de identificação fiscal da Espanha

Suécia
* ID nacional da Suécia
* Número do passaporte da Suécia
* Número de identificação do imposto Suécia

Suíça
* Número do CPF social AHV

Taiwan 
* ID nacional de Taiwan
* Certificado residente em Taiwan (ARC/TARC)
* Número de passaporte de Taiwan

Tailândia
* Código de Identificação de População Tailandês

Turquia
* Número de identificação nacional turco

Ucrânia
* Número do Passport da Ucrânia (doméstico)
* Número do Passport da Ucrânia (internacional)

United Kingdom
* Inglaterra Número da licença do driver
* Inglaterra Número de reversão do eleitoral
* Inglaterra Número de Serviço de Integridade nacional (NHS)
* Inglaterra Número de seguro nacional (NINO)
* Inglaterra Número do passaporte
* Inglaterra Número de referência de contribuidor exclusivo

Estados Unidos
* Número do seguro social (SSN) dos EUA
* Número da licença do driver dos EUA
* Número do passaporte dos EUA
* Número de identificação de contribuinte (certa) dos EUA
* Número de DEA (Agência de imposição de medicamentos) dos EUA
* Número da conta bancária dos EUA
