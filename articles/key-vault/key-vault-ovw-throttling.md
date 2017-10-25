---
ms.assetid: 
title: "Diretrizes de limitação do Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 06/21/2017
ms.openlocfilehash: fe700e22c5323c2a0bdc315e349cd119798bcf40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-throttling-guidance"></a>Diretrizes de limitação do Azure Key Vault

A limitação é o processo que você iniciar para limitar o número de chamadas simultâneas para o serviço do Azure a fim de evitar o uso excessivo de recursos. O AKV (Azure Key Vault) foi projetado para lidar com um grande volume de solicitações. No caso de ocorrer um grande número de solicitações, limitar as solicitações do seu cliente ajuda a manter o desempenho ideal e a confiabilidade do serviço AKV.

Limites de limitação variam de acordo com o cenário. Por exemplo, se você estiver executando um grande volume de gravação, a possibilidade de limitação será maior do que se você estiver apenas executando leituras.

## <a name="how-does-key-vault-handle-its-limits"></a>Como o Key Vault trata os próprios limites?

Os limites de serviço no Key Vault existem para impedir o uso incorreto de recursos e assegurar a qualidade de serviço para todos os clientes do Key Vault. Quando um limite de serviço é excedido, o Key Vault limita quaisquer solicitações adicionais desse cliente por um período de tempo. Quando isso acontece, o Key Vault retorna o código de status HTTP 429 (Número excessivo de solicitações) e as solicitações falham. Além disso, solicitações com falha que retornam um 429 contam para as restrições acompanhadas pelo Key Vault. 

Se você tiver um caso comercial válido para restrições mais altas, entre em contato conosco.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Como restringir o seu aplicativo em resposta aos limites de serviço

A seguir estão as **práticas recomendadas** para limitar seu aplicativo:
- Reduza o número de operações por solicitação.
- Reduza a frequência de solicitações.
- Evite tentativas imediatas. 
    - Todas as solicitações se acumulam em relação a seus limites de uso.

Quando você implementa o tratamento de erro do aplicativo, use o código de erro HTTP 429 para detectar a necessidade de limitação do lado do cliente. Se a solicitação falha novamente com um código de erro HTTP 429, você ainda está encontrando um limite de serviço do Azure. Continue a usar o método de limitação do lado do cliente recomendado, tentando realizar a solicitação novamente até que ela tenha êxito.

### <a name="recommended-client-side-throttling-method"></a>Método recomendado de limitação do lado do cliente

No código de erro HTTP 429, inicie a limitação do cliente usando uma abordagem de retirada exponencial:

1. Aguarde 1 segundo, tente solicitar novamente
2. Se ainda estiver limitado, aguarde 2 segundos e tente a solicitação novamente
3. Se ainda estiver limitado, aguarde 4 segundos e tente a solicitação novamente
4. Se ainda estiver limitado, aguarde 8 segundos e tente a solicitação novamente
5. Se ainda estiver limitado, aguarde 16 segundos e tente a solicitação novamente

Neste ponto, você não deve estar obtendo códigos de resposta HTTP 429.

## <a name="see-also"></a>Consulte também

Para obter uma orientação mais profunda de limitação no Microsoft Cloud, consulte [Padrão de Limitação](https://docs.microsoft.com/azure/architecture/patterns/throttling).

