---
title: Como identificar endereços IP públicos de saída no Azure Spring Cloud
description: Como exibir os endereços IP públicos de saída estáticos para se comunicar com recursos externos, como banco de dados, armazenamento, Key Vault, etc.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877707"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Como identificar endereços IP públicos de saída no Azure Spring Cloud

Esta página explica como exibir endereços IP públicos de saída estáticos de aplicativos do Azure Spring Cloud.  Os IPs públicos são usados para se comunicar com recursos externos, como bancos de dados, armazenamento e cofres de chaves.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Como os endereços IP funcionam no Azure Spring Cloud

Um serviço de nuvem do Azure Spring tem um ou mais endereços IP públicos de saída. O número de endereços IP públicos de saída pode variar de acordo com as camadas e outros fatores. 

Os endereços IP públicos de saída geralmente são constantes e permanecem os mesmos, mas há exceções.

## <a name="when-outbound-ips-change"></a>Quando ocorrem alterações dos IPs de saída

Cada instância do Azure Spring Cloud tem um número definido de endereços IP públicos de saída em um determinado momento. Qualquer conexão de saída dos aplicativos, como um banco de dados back-end, usa um dos endereços IP públicos de saída como o endereço IP de origem. O endereço IP é selecionado aleatoriamente em tempo de execução, de modo que o serviço de back-end deve abrir seu firewall para todos os endereços IP de saída.

O número de IPs públicos de saída é alterado quando você executa uma das seguintes ações:

- Atualize sua instância do Azure Spring Cloud entre camadas.
- Gere um tíquete de suporte para mais IPs públicos de saída para necessidades comerciais.

## <a name="find-outbound-ips"></a>Localizar IPs de saída

Para localizar os endereços IP públicos de saída usados atualmente por sua instância de serviço no portal do Azure, clique em **rede** no painel de navegação esquerdo da instância. Eles são listados no campo **endereços IP de saída** .

Você pode encontrar as mesmas informações executando o seguinte comando no Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
* [Saiba mais sobre identidades gerenciadas para recursos do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Saiba mais sobre o Key Vault no Azure Spring Cloud](spring-cloud-tutorial-managed-identities-key-vault.md)
