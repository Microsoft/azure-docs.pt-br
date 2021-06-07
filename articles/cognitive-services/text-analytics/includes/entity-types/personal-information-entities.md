---
title: Entidades nomeadas para informações pessoais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 19586c09cca9a0dc74ba9ee4ef9da459964f9b7e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599284"
---
> [!NOTE]
> Para detectar informações de integridade protegidas (PHI), use o `domain=phi` parâmetro e a versão do modelo `2020-04-01` ou posterior.
>
> Por exemplo: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
As categorias de entidade a seguir são retornadas quando você está enviando solicitações para o `/v3.1-preview.3/entities/recognition/pii` ponto de extremidade.


| Categoria   |  Descrição                          |
|------------|-------------|
| [Person](#category-person)      |  Nomes de pessoas.  |
| [Persontype](#category-persontype) | Tipos de trabalho ou funções mantidas por uma pessoa. |
| [Número do telefone](#category-phonenumber) |Números de telefone (somente números de telefone dos EUA e da UE). |
| [Organização](#category-organization) |  Empresas, grupos, órgãos governamentais e outras organizações.  |
| [Endereço](#category-address) | Endereços de endereçamento completos.  |
| [Email](#category-email) | Endereços de email.   |
| [URL](#category-url) | URLs para sites.  |
| [IP](#category-ip) | Endereços IP de rede.  |
| [DateTime](#category-datetime) | Datas e horas do dia. | 
| [Quantidade](#category-quantity) | Números e quantidades numéricas.  |
| [Informações do Azure](#azure-information) | Informações identificáveis do Azure, como informações de autenticação.  |
| [Identificação](#identification) | Identificação específica financeira e de país.  |

### <a name="category-person"></a>Categoria: pessoa

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Pessoa

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Nomes de pessoas. 

        Para obter essa categoria de entidade, adicione `Person` ao `pii-categories` parâmetro. `Person` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Categoria: Persontype

Esta categoria contém a seguinte entidade:


:::row:::
    :::column span="":::
        **Entidade**

        Persontype

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Tipos de trabalho ou funções mantidas por uma pessoa.

        Para obter essa categoria de entidade, adicione `PersonType` ao `pii-categories` parâmetro. `PersonType` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Categoria: PhoneNumber

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números de telefone (somente números de telefone dos EUA e da UE). Também retornado com `domain=phi` .

        Para obter essa categoria de entidade, adicione `PhoneNumber` ao `pii-categories` parâmetro. `PhoneNumber` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>Categoria: organização

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Organização

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas. As nacionalidades e Religions não são incluídas nesse tipo de entidade.

        Para obter essa categoria de entidade, adicione `Organization` ao `pii-categories` parâmetro. `Organization` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Médicos    

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Empresas e grupos médicos.

        Para obter essa categoria de entidade, adicione `OrganizationMedical` ao `pii-categories` parâmetro. `OrganizationMedical` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Troca de estoque

    :::column-end:::
    :::column span="2":::

        Grupos de troca de estoque. 

        Para obter essa categoria de entidade, adicione `OrganizationStockExchange` ao `pii-categories` parâmetro. `OrganizationStockExchange` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Esportes

    :::column-end:::
    :::column span="2":::

        Organizações relacionadas a esportes.

        Para obter essa categoria de entidade, adicione `OrganizationSports` ao `pii-categories` parâmetro. `OrganizationSports` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


### <a name="category-address"></a>Categoria: endereço

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Endereço

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Endereço de endereçamento completo.

        Para obter essa categoria de entidade, adicione `Address` ao `pii-categories` parâmetro. `Address` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>Categoria: email

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        Email

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Endereços de email.
      
        Para obter essa categoria de entidade, adicione `Email` ao `pii-categories` parâmetro. `Email` será retornado na resposta da API, se detectado.

    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>Categoria: URL

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        URL

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        URLs para sites. 

        Para obter essa categoria de entidade, adicione `URL` ao `pii-categories` parâmetro. `URL` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ip"></a>Categoria: IP

Esta categoria contém a seguinte entidade:

:::row:::
    :::column span="":::
        **Entidade**

        IP

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        endereços IP de rede. 

        Para obter essa categoria de entidade, adicione `IP` ao `pii-categories` parâmetro. `IP` será retornado na resposta da API, se detectado.
      
    :::column-end:::

    :::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Categoria: DateTime

Essa categoria contém as seguintes entidades:

:::row:::
    :::column span="":::
        **Entidade**

        DateTime

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Datas e horas do dia. 

        Para obter essa categoria de entidade, adicione `DateTime` ao `pii-categories` parâmetro. `DateTime` será retornado na resposta da API, se detectado.
      
    :::column-end:::
:::column span="":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Data

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Datas do calendário.

        Para obter essa categoria de entidade, adicione `Date` ao `pii-categories` parâmetro. `Date` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Categoria: quantidade

Essa categoria contém as seguintes entidades:

:::row:::
    :::column span="":::
        **Entidade**

        Quantidade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Números e quantidades numéricas.

        Para obter essa categoria de entidade, adicione `Quantity` ao `pii-categories` parâmetro. `Quantity` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="2":::
      **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Subcategorias

A entidade nessa categoria pode ter as subcategorias a seguir.

:::row:::
    :::column span="":::
        **Subcategoria da entidade**

        Idade

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Paralisa.

        Para obter essa categoria de entidade, adicione `Age` ao `pii-categories` parâmetro. `Age` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="2":::
        **Idiomas de documento com suporte**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informações do Azure

Essas categorias de entidade incluem informações identificáveis do Azure, incluindo informações de autenticação e cadeias de conexão. Não retornado com o `domain=phi` parâmetro.

:::row:::
    :::column span="":::
        **Entidade**

        Chave de autenticação DocumentDB do Azure 

    :::column-end:::
    :::column span="2":::
        **Detalhes**

        Chave de autorização para um servidor de Azure Cosmos DB.   

        Para obter essa categoria de entidade, adicione `AzureDocumentDBAuthKey` ao `pii-categories` parâmetro. `AzureDocumentDBAuthKey` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::
      **Idiomas de documento com suporte**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão de banco de dados IAAS do Azure e cadeia de conexão SQL do Azure.
        

    :::column-end:::
    :::column span="2":::

        Cadeia de conexão para um banco de dados IaaS (infraestrutura como serviço) do Azure e uma cadeia de conexão SQL.

        Para obter essa categoria de entidade, adicione `AzureIAASDatabaseConnectionAndSQLString` ao `pii-categories` parâmetro. `AzureIAASDatabaseConnectionAndSQLString` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão do IoT do Azure  

    :::column-end:::
    :::column span="2":::

        Cadeia de conexão para IoT do Azure. 
      
        Para obter essa categoria de entidade, adicione `AzureIoTConnectionString` ao `pii-categories` parâmetro. `AzureIoTConnectionString` será retornado na resposta da API, se detectado.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Senha das configurações de publicação do Azure  

    :::column-end:::
    :::column span="2":::

        Senha para as configurações de publicação do Azure.

        Para obter essa categoria de entidade, adicione `AzurePublishSettingPassword` ao `pii-categories` parâmetro. `AzurePublishSettingPassword` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão do Cache Redis do Azure 

    :::column-end:::
    :::column span="2":::

        Cadeia de conexão para um cache Redis.

        Para obter essa categoria de entidade, adicione `AzureRedisCacheString` ao `pii-categories` parâmetro. `AzureRedisCacheString` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Cadeia de conexão para SaaS (software como serviço) do Azure.

        Para obter essa categoria de entidade, adicione `AzureSAS` ao `pii-categories` parâmetro. `AzureSAS` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão do Barramento de Serviço do Azure

    :::column-end:::
    :::column span="2":::

        Cadeia de conexão para um barramento de serviço do Azure.

        Para obter essa categoria de entidade, adicione `AzureServiceBusString` ao `pii-categories` parâmetro. `AzureServiceBusString` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chave de conta de armazenamento do Azure 

    :::column-end:::
    :::column span="2":::

        Chave de conta para uma conta de armazenamento do Azure. 

        Para obter essa categoria de entidade, adicione `AzureStorageAccountKey` ao `pii-categories` parâmetro. `AzureStorageAccountKey` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chave da conta de armazenamento do Azure (genérica)

    :::column-end:::
    :::column span="2":::

        Chave de conta genérica para uma conta de armazenamento do Azure.

        Para obter essa categoria de entidade, adicione `AzureStorageAccountGeneric` ao `pii-categories` parâmetro. `AzureStorageAccountGeneric` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Cadeia de conexão do SQL Server 

    :::column-end:::
    :::column span="2":::

        Cadeia de conexão para um computador executando SQL Server.

        Para obter essa categoria de entidade, adicione `SQLServerConnectionString` ao `pii-categories` parâmetro. `SQLServerConnectionString` será retornado na resposta da API, se detectado.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identificação

[!INCLUDE [supported identification entities](./identification-entities.md)]
