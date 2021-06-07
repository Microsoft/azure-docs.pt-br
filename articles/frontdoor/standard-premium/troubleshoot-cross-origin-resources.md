---
title: Usando o Azure front door Standard/Premium com compartilhamento de recursos entre origens
description: Saiba como usar a porta frontal do Azure (AFD) com o compartilhamento de recursos entre origens (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101098315"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Usando o Azure front door Standard/Premium com o CORS (compartilhamento de recursos entre origens)

> [!Note]
> Esta documentação é específica para o Azure Front Door Standard/Premium (Versão Prévia). Está procurando informações sobre o Azure Front Door? Veja [aqui](../front-door-overview.md).

## <a name="what-is-cors"></a>O que é o CORS?

O CORS (Compartilhamento de Recursos entre Origens) é um recurso HTTP que permite que um aplicativo web em execução em um domínio acesse recursos em outro domínio. Para reduzir a possibilidade de ataques de script entre sites, todos os navegadores da Web modernos implementam uma restrição de segurança conhecida como [política de mesma origem](https://www.w3.org/Security/wiki/Same_Origin_Policy). Isso impede que uma página da Web chame APIs em um domínio diferente.  O CORS fornece uma maneira segura para permitir que uma origem (o domínio de origem) chame APIs em outra origem.

> [!IMPORTANT]
> O Azure Front Door Standard/Premium (versão prévia) está na fase de versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Como ele funciona

Há dois tipos de solicitações CORS, *solicitações simples* e *solicitações complexas.*

### <a name="for-simple-requests"></a>Para solicitações simples:

1. O navegador envia a solicitação de CORS com outro cabeçalho de solicitação HTTP de **origem** . O valor desse cabeçalho é a origem que serviu a página pai, que é definida como a combinação de *protocolo,* *domínio,* e *porta.*  Quando uma página de HTTPS \: //www.contoso.com tenta acessar os dados de um usuário na origem fabrikam.com, o seguinte cabeçalho de solicitação seria enviado para fabrikam.com:

   `Origin: https://www.contoso.com`

2. O servidor pode responder com um dos seguintes:

   * Um cabeçalho **Access-Control-Allow-Origin** em sua resposta indicando qual site de origem é permitido. Por exemplo:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Um código de erro HTTP, como 403, se o servidor não permitir a solicitação entre origens depois de verificar o cabeçalho de origem

   * Um cabeçalho **Access-Control-Allow-Origin** com um caractere curinga que permite todas as origens:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Para solicitações complexas:

Uma solicitação complexa é uma solicitação CORS onde o navegador é necessário para enviar um *solicitação de simulação* (ou seja, uma investigação preliminar) antes de enviar a solicitação CORS real. A solicitação de simulação perguntará a permissão do servidor se a solicitação CORS original puder continuar e for uma `OPTIONS` solicitação para a mesma URL.

> [!TIP]
> Para obter mais detalhes sobre fluxos CORS e armadilhas comuns, consulte o [guia CORS para APIs REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de origem única ou curinga
O CORS na porta de recepção do Azure funcionará automaticamente sem nenhuma configuração extra quando o cabeçalho **Access-Control-permitir-Origin** for definido como curinga (*) ou uma única origem.  A porta frontal do Azure armazenará em cache a primeira resposta e solicitações informativas usarão o mesmo cabeçalho.

Se já tiverem sido feitas solicitações na porta frontal do Azure antes de o CORS ser definido em sua origem, você precisará limpar o conteúdo do seu conteúdo do ponto de extremidade para recarregar o conteúdo com o cabeçalho **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Cenários de várias origens
Se você precisar permitir que uma lista específica de origens seja permitida para o CORS, isso será um pouco mais complicado. O problema ocorre quando a CDN armazena o cabeçalho **Access-Control-Allow-Origin** em cache para a primeira origem do CORS.  Quando uma origem de CORS diferente fizer outra solicitação, a CDN servirá para o cabeçalho **Access-Control-Allow-Origin** armazenado em cache, que não corresponderá. Há várias maneiras de corrigir esse problema.

### <a name="azure-front-door-rule-set"></a>Conjunto de regras de porta frontal do Azure

Na porta frontal do Azure, você pode criar uma regra no [conjunto de regras](concept-rule-set.md) de porta frontal do Azure para verificar o cabeçalho de **origem** na solicitação. Se for uma origem válida, sua regra definirá o cabeçalho **Access-Control-Allow-Origin** com o valor correto. Nesse caso, o cabeçalho **acesso-controle-permitir-Origin** do servidor de origem do arquivo é ignorado e o mecanismo de regras do AFD gerencia completamente as origens CORS permitidas.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Captura de tela de exemplo de regras com conjunto de regras.":::

> [!TIP]
> Você pode adicionar ações adicionais à sua regra para modificar cabeçalhos de resposta adicionais, como **Access-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar um Front Door](create-front-door-portal.md).
