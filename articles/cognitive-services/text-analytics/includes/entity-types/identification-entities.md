---
title: Entidades de identificação
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599282"
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

        Para obter essa categoria de entidade, adicione `ABARoutingNumber` ao `pii-categories` parâmetro. `ABARoutingNumber` também será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`
      
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

        Para obter essa categoria de entidade, adicione `SWIFTCode` ao `pii-categories` parâmetro. `SWIFTCode` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Para obter essa categoria de entidade, adicione `CreditCardNumber` ao `pii-categories` parâmetro. `CreditCardNumber` será retornado na resposta da API, se detectado.

    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        Para obter essa categoria de entidade, adicione `InternationlBankingAccountNumber` ao `pii-categories` parâmetro. `InternationlBankingAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `ARNationalIdentityNumber` ao `pii-categories` parâmetro. `ARNationalIdentityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Áustria

:::row:::
    :::column span="":::
        **Entidade**

        Cartão de identidade da Áustria

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `ATIdentityCard` ao `pii-categories` parâmetro. `ATIdentityCard` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto da Áustria

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ATTaxIdentificationNumber` ao `pii-categories` parâmetro. `ATTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de IVA (imposto sobre valor agregado) da Áustria

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ATValueAddedTaxNumber` ao `pii-categories` parâmetro. `ATValueAddedTaxNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Austrália

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária da Austrália

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `AUDriversLicenseNumber` ao `pii-categories` parâmetro. `AUDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de negócios australiano

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `AUBusinessNumber` ao `pii-categories` parâmetro. `AUBusinessNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da empresa da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `AUCompanyNumber` ao `pii-categories` parâmetro. `AUCompanyNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licença do driver da Austrália  

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `AUDriversLicense` ao `pii-categories` parâmetro. `AUDriversLicense` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta médica da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `AUMedicalAccountNumber` ao `pii-categories` parâmetro. `AUMedicalAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ATPassportNumber` ao `pii-categories` parâmetro. `ATPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do arquivo de imposto da Austrália

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ATTaxIdentificationNumber` ao `pii-categories` parâmetro. `ATTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Bélgica

:::row:::
    :::column span="":::
        **Entidade**

        Número nacional da Bélgica

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `BENationalNumber` ao `pii-categories` parâmetro. `BENationalNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de IVA (imposto agregado) do valor belga

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `BEValueAddedTaxNumber` ao `pii-categories` parâmetro. `BEValueAddedTaxNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brasil 

:::row:::
    :::column span="":::
        **Entidade**

        CNPJ (número da entidade legal) do Brasil

        

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `BRLegalEntityNumber` ao `pii-categories` parâmetro. `BRLegalEntityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do CPF do Brasil

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `BRCPFNumber` ao `pii-categories` parâmetro. `BRCPFNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cartão de ID nacional do Brasil (RG)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `BRNationalIDRG` ao `pii-categories` parâmetro. `BRNationalIDRG` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canadá

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária do Canadá

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `CABankAccountNumber` ao `pii-categories` parâmetro. `CABankAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver do Canadá

    :::column-end:::

    :::column span="2":::

        Para obter essa categoria de entidade, adicione `CADriversLicenseNumber` ao `pii-categories` parâmetro. `CADriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do serviço de integridade do Canadá

        
    :::column-end:::

    :::column span="2":::

        Para obter essa categoria de entidade, adicione `CAHealthServiceNumber` ao `pii-categories` parâmetro. `CAHealthServiceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Canadá

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `CAPassportNumber` ao `pii-categories` parâmetro. `CAPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação de integridade pessoal do Canadá (PHIN)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `CAPersonalHealthIdentification` ao `pii-categories` parâmetro. `CAPersonalHealthIdentification` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro social do Canadá

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `CASocialInsuranceNumber` ao `pii-categories` parâmetro. `CASocialInsuranceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de identidade do Chile

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `CLIdentityCardNumber` ao `pii-categories` parâmetro. `CLIdentityCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entidade**

        Número PRC (cartão de identidade residente) da China

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `CNResidentIdentityCardNumber` ao `pii-categories` parâmetro. `CNResidentIdentityCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>União Europeia (UE)

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de débito da UE

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `EUDebitCardNumber` ao `pii-categories` parâmetro. `EUDebitCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver da UE

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `EUDriversLicenseNumber` ao `pii-categories` parâmetro. `EUDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordenadas da GPU da UE

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `EUGPSCoordinates` ao `pii-categories` parâmetro. `EUGPSCoordinates` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional da UE

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `EUNationalIdentificationNumber` ao `pii-categories` parâmetro. `EUNationalIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Passport da UE

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `EUPassportNumber` ao `pii-categories` parâmetro. `EUPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro social da UE (SSN) ou ID equivalente

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `EUSocialSecurityNumber` ao `pii-categories` parâmetro. `EUSocialSecurityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de Identificação Tributária da UE (TIN)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `EUTaxIdentificationNumber` ao `pii-categories` parâmetro. `EUTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>França

:::row:::
    :::column span="":::
        **Entidade**

        Número da licença do driver da França

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `FRDriversLicenseNumber` ao `pii-categories` parâmetro. `FRDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro de saúde da França

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `FRHealthInsuranceNumber` ao `pii-categories` parâmetro. `FRHealthInsuranceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Placa de ID nacional da França (CNI)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `FRNationalID` ao `pii-categories` parâmetro. `FRNationalID` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Passport da França

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `FRPassportNumber` ao `pii-categories` parâmetro. `FRPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        INSEE (número de seguro social da França)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `FRSocialSecurityNumber` ao `pii-categories` parâmetro. `FRSocialSecurityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto da França (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `FRTaxIdentificationNumber` ao `pii-categories` parâmetro. `FRTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de IVA (imposto agregado) de valor da França

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `FRValueAddedTaxNumber` ao `pii-categories` parâmetro. `FRValueAddedTaxNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Alemanha

:::row:::
    :::column span="":::
        **Entidade**

        Número da licença do driver alemão

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `DEDriversLicenseNumber` ao `pii-categories` parâmetro. `DEDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do cartão de identidade da Alemanha

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `DEIdentityCardNumber` ao `pii-categories` parâmetro. `DEIdentityCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do Passport da Alemanha

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `DEPassportNumber` ao `pii-categories` parâmetro. `DEPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto da Alemanha

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `DETaxIdentificationNumber` ao `pii-categories` parâmetro. `DETaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do imposto sobre valor agregado da Alemanha

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `DEValueAddedNumber` ao `pii-categories` parâmetro. `DEValueAddedNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAE de Hong Kong

:::row:::
    :::column span="":::
        **Entidade**

        Número de HKID (placa de identidade de Hong Kong)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `HKIdentityCardNumber` ao `pii-categories` parâmetro. `HKIdentityCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Hungria

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação pessoal da Hungria

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `HUPersonalIdentificationNumber` ao `pii-categories` parâmetro. `HUPersonalIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto Hungria

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `HUTaxIdentificationNumber` ao `pii-categories` parâmetro. `HUTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de imposto de valor agregado Hungria

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `HUValueAddedNumber` ao `pii-categories` parâmetro. `HUValueAddedNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>Índia

:::row:::
    :::column span="":::
        **Entidade**

        Número de conta permanente da Índia (PAN)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `INPermanentAccount` ao `pii-categories` parâmetro. `INPermanentAccount` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da Aadhaar (identificação exclusiva da Índia)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `INUniqueIdentificationNumber` ao `pii-categories` parâmetro. `INUniqueIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonésia

:::row:::
    :::column span="":::
        **Entidade**

        Número do KTP (cartão de identidade da Indonésia)

    :::column-end:::
    :::column span="2":::

        **Detalhes**

        Para obter essa categoria de entidade, adicione `IDIdentityCardNumber` ao `pii-categories` parâmetro. `IDIdentityCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entidade**

        Número do PPS (serviço público pessoal) da Irlanda

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `IEPersonalPublicServiceNumber` ao `pii-categories` parâmetro. `IEPersonalPublicServiceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Número de serviço de público pessoal da Irlanda (PPS) V2

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `IEPersonalPublicServiceNumberV2` ao `pii-categories` parâmetro. `IEPersonalPublicServiceNumberV2` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entidade**

        ID nacional de Israel

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `ILNationalID` ao `pii-categories` parâmetro. `ILNationalID` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da conta bancária de Israel

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ILBankAccountNumber` ao `pii-categories` parâmetro. `ILBankAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Itália

:::row:::
    :::column span="":::
        **Entidade**

        ID da licença do driver da Itália

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `ITDriversLicenseNumber` ao `pii-categories` parâmetro. `ITDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Código fiscal da Itália

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ITFiscalCode` ao `pii-categories` parâmetro. `ITFiscalCode` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de imposto adicionado ao valor da Itália

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ITValueAddedTaxNumber` ao `pii-categories` parâmetro. `ITValueAddedTaxNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Japão

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária do Japão

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `JPBankAccountNumber` ao `pii-categories` parâmetro. `JPBankAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver do Japão

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `JPDriversLicenseNumber` ao `pii-categories` parâmetro. `JPDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "meu número" (pessoal)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `JPMyNumberPersonal` ao `pii-categories` parâmetro. `JPMyNumberPersonal` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japão "meu número" (corporativo)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `JPMyNumberCorporate` ao `pii-categories` parâmetro. `JPMyNumberCorporate` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de registro residente do Japão

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ITValueAddedTaxNumber` ao `pii-categories` parâmetro. `ITValueAddedTaxNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do cartão de residência do Japão

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `JPResidenceCardNumber` ao `pii-categories` parâmetro. `JPResidenceCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de seguro social do Japão (SIN)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `JPSocialInsuranceNumber` ao `pii-categories` parâmetro. `JPSocialInsuranceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do passaporte do Japão

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `JPPassportNumber` ao `pii-categories` parâmetro. `JPPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Luxemburgo

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação nacional de Luxemburgo (pessoas naturais)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `LUNationalIdentificationNumberNatural` ao `pii-categories` parâmetro. `LUNationalIdentificationNumberNatural` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação nacional de Luxemburgo (pessoas não naturais)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `LUNationalIdentificationNumberNonNatural` ao `pii-categories` parâmetro. `LUNationalIdentificationNumberNonNatural` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de identidade de Malta

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `MTIdentityCardNumber` ao `pii-categories` parâmetro. `MTIdentityCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação do imposto de Malta

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `MTTaxIDNumber` ao `pii-categories` parâmetro. `MTTaxIDNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nova Zelândia

:::row:::
    :::column span="":::
        **Entidade**

        Número da conta bancária da Nova Zelândia

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `NZBankAccountNumber` ao `pii-categories` parâmetro. `NZBankAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número da licença do driver da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `NZDriversLicenseNumber` ao `pii-categories` parâmetro. `NZDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de receita Inland da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `NZInlandRevenueNumber` ao `pii-categories` parâmetro. `NZInlandRevenueNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ministério da Nova Zelândia do número de saúde

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `NZMinistryOfHealthNumber` ao `pii-categories` parâmetro. `NZMinistryOfHealthNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de Welfare social da Nova Zelândia

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `NZSocialWelfareNumber` ao `pii-categories` parâmetro. `NZSocialWelfareNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filipinas

:::row:::
    :::column span="":::
        **Entidade**

        Filipinas número de ID de multipropósito unificado

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `PHUnifiedMultiPurposeIDNumber` ao `pii-categories` parâmetro. `PHUnifiedMultiPurposeIDNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão do cidadão de Portugal

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `PTCitizenCardNumber` ao `pii-categories` parâmetro. `PTCitizenCardNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificação do imposto de Portugal

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `PTTaxIdentificationNumber` ao `pii-categories` parâmetro. `PTTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapura

:::row:::
    :::column span="":::
        **Entidade**

        Número do cartão de ID de Registro Nacional de Cingapura (NRIC)

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `PTTaxIdentificationNumber` ao `pii-categories` parâmetro. `PTTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>África do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de identificação da África do Sul

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `ZAIdentificationNumber` ao `pii-categories` parâmetro. `ZAIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Coreia do Sul

:::row:::
    :::column span="":::
        **Entidade**

        Número de registro residente da Coreia do Sul

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `KRResidentRegistrationNumber` ao `pii-categories` parâmetro. `KRResidentRegistrationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Espanha

:::row:::
    :::column span="":::
        **Entidade**

        DNI da Espanha

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `ESDNI` ao `pii-categories` parâmetro. `ESDNI` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número do seguro social da Espanha (SSN)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ESSocialSecurityNumber` ao `pii-categories` parâmetro. `ESSocialSecurityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de identificação fiscal da Espanha

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `ESTaxIdentificationNumber` ao `pii-categories` parâmetro. `ESTaxIdentificationNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Suíça

:::row:::
    :::column span="":::
        **Entidade**

        Número do CPF social AHV

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `CHSocialSecurityNumber` ao `pii-categories` parâmetro. `CHSocialSecurityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entidade**

        ID nacional de Taiwan

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `TWNationalID` ao `pii-categories` parâmetro. `TWNationalID` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificado residente em Taiwan (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `TWResidentCertificate` ao `pii-categories` parâmetro. `TWResidentCertificate` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Número de passaporte de Taiwan

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `TWPassportNumber` ao `pii-categories` parâmetro. `TWPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Reino Unido

:::row:::
    :::column span="":::
        **Entidade**

        Inglaterra Número da licença do driver

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `UKDriversLicenseNumber` ao `pii-categories` parâmetro. `UKDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de reversão do eleitoral

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `UKNationalInsuranceNumber` ao `pii-categories` parâmetro. `UKNationalInsuranceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de Serviço de Integridade nacional (NHS)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `UKNationalHealthNumber` ao `pii-categories` parâmetro. `UKNationalHealthNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de seguro nacional (NINO)

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `UKNationalInsuranceNumber` ao `pii-categories` parâmetro. `UKNationalInsuranceNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra ou número de passaporte dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `USUKPassportNumber` ao `pii-categories` parâmetro. `USUKPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Inglaterra Número de referência de contribuidor exclusivo

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `UKUniqueTaxpayerNumber` ao `pii-categories` parâmetro. `UKUniqueTaxpayerNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Estados Unidos

:::row:::
    :::column span="":::
        **Entidade**

        Número do seguro social (SSN) dos EUA

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Para obter essa categoria de entidade, adicione `USSocialSecurityNumber` ao `pii-categories` parâmetro. `USSocialSecurityNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da licença do driver dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `USDriversLicenseNumber` ao `pii-categories` parâmetro. `USDriversLicenseNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       EUA ou Reino Unido Número do passaporte

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `USUKPassportNumber` ao `pii-categories` parâmetro. `USUKPassportNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de identificação de contribuinte (certa) dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `USIndividualTaxpayerIdentification` ao `pii-categories` parâmetro. `USIndividualTaxpayerIdentification` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número de DEA (Agência de imposição de medicamentos) dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `DrugEnforcementAgencyNumber` ao `pii-categories` parâmetro. `DrugEnforcementAgencyNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Número da conta bancária dos EUA

    :::column-end:::
    :::column span="2":::

        Para obter essa categoria de entidade, adicione `USBankAccountNumber` ao `pii-categories` parâmetro. `USBankAccountNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
