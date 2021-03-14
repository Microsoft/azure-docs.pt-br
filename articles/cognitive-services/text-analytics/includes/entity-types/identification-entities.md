---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750204"
---
### <a name="financial-account-identification"></a>Identificação da conta financeira

Essa categoria de entidade inclui informações financeiras e formulários oficiais de identificação.

#### <a name="category-aba-routing-number"></a>Categoria: número de roteamento ABA

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Número de roteamento ABA

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de roteamento de trânsito da ABA (Associação Americana do Banker).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Categoria: código SWIFT

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Código SWIFT

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Códigos SWIFT para informações de instrução de pagamento.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Categoria: cartão de crédito

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Cartão de crédito

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de cartão de crédito. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Categoria: número de conta bancária internacional (IBAN) 

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Cartão de crédito

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Códigos de IBAN para informações de instrução de pagamento.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identificação específica do governo e do país/região

> [!NOTE]
> As seguintes entidades financeiras e específicas do país não são retornadas com o `domain=phi` parâmetro:
> * Números de passaporte
> * IDs de imposto

As seguintes entidades são agrupadas e listadas por país:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entidade**

        Número de identidade nacional da Argentina (DNI)

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Áustria

:::row:::
    :::column span="":::
        **Entidade**

        Cartão de identidade da Áustria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto da Áustria

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número de IVA (imposto sobre valor agregado) da Áustria

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Austrália

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária da Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número de negócios australiano

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número da empresa da Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Licença do driver da Austrália  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta médica da Austrália

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte da Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte da Austrália

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número do arquivo de imposto da Austrália

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Bélgica

:::row:::
    :::column span="":::
        **Entidade**

        Número nacional da Bélgica

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de IVA (imposto agregado) do valor belga

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brasil 

:::row:::
    :::column span="":::
        **Entidade**

        CNPJ (número da entidade legal) do Brasil

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do CPF do Brasil

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Cartão de ID nacional do Brasil (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária do Canadá

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver do Canadá

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do serviço de integridade do Canadá

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Canadá

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação de integridade pessoal do Canadá (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro social do Canadá

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de identidade do Chile

    :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entidade**

        Número PRC (cartão de identidade residente) da China

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>União Europeia (UE)

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de débito da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Passport da UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro social da UE (SSN) ou ID equivalente

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Identificação Tributária da UE (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordenadas do GPS da UE

    :::column-end:::
:::row-end:::

#### <a name="france"></a>França

:::row:::
    :::column span="":::
        **Entidade**

        Número da licença do driver da França

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro de saúde da França

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Placa de ID nacional da França (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Passport da França

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        INSEE (número de seguro social da França)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto da França (Numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de IVA (imposto agregado) de valor da França

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Alemanha

:::row:::
    :::column span="":::
        **Entidade**

        Número da licença do driver alemão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do cartão de identidade da Alemanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Passport da Alemanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto da Alemanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do imposto sobre valor agregado da Alemanha

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAE de Hong Kong

:::row:::
    :::column span="":::
        **Entidade**

        Número de HKID (placa de identidade de Hong Kong)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hungria

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação pessoal da Hungria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto Hungria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de imposto de valor agregado Hungria

    :::column-end:::
:::row-end:::

#### <a name="india"></a>Índia

:::row:::
    :::column span="":::
        **Entidade**

        Número de conta permanente da Índia (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da Aadhaar (identificação exclusiva da Índia)

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésia

:::row:::
    :::column span="":::
        **Entidade**

        Número do KTP (cartão de identidade da Indonésia)

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entidade**

        Número do PPS (serviço público pessoal) da Irlanda

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entidade**

        ID nacional de Israel

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta bancária de Israel

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Itália

:::row:::
    :::column span="":::
        **Entidade**

        ID da licença do driver da Itália

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Código fiscal da Itália

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de imposto adicionado ao valor da Itália

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japão

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária do Japão

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver do Japão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "meu número" (pessoal)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "meu número" (corporativo)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de registro residente do Japão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do cartão de residência do Japão

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro social do Japão (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Japão

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburgo

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação nacional de Luxemburgo (pessoas naturais)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional de Luxemburgo (pessoas não naturais)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de identidade de Malta

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto de Malta

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nova Zelândia

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de receita Inland da Nova Zelândia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ministério da Nova Zelândia do número de saúde

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de Welfare social da Nova Zelândia

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipinas

:::row:::
    :::column span="":::
        **Entidade**

        Filipinas número de ID de multipropósito unificado

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão do cidadão de Portugal

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificação do imposto de Portugal

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapura

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de ID de Registro Nacional de Cingapura (NRIC)

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>África do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação da África do Sul

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Coreia do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de registro residente da Coreia do Sul

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Espanha

:::row:::
    :::column span="":::
        **Entidade**

        DNI da Espanha

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro social da Espanha (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Espanha

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suíça

:::row:::
    :::column span="":::
        **Entidade**

        Número do CPF social AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entidade**

        ID nacional de Taiwan

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificado residente em Taiwan (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte de Taiwan

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>United Kingdom

:::row:::
    :::column span="":::
        **Entidade**

        Inglaterra Número da licença do driver

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de reversão do eleitoral

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de Serviço de Integridade nacional (NHS)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de seguro nacional (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra ou número de passaporte dos EUA

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de referência de contribuidor exclusivo

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>Estados Unidos

:::row:::
    :::column span="":::
        **Entidade**

        Número do seguro social (SSN) dos EUA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da licença do driver dos EUA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       EUA ou Reino Unido Número do passaporte

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificação de contribuinte (certa) dos EUA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de DEA (Agência de imposição de medicamentos) dos EUA

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da conta bancária dos EUA

    :::column-end:::
:::row-end:::
