---
title: Porta frontal do Azure-suporte para domínios curinga
description: Este artigo ajuda você a entender como a Azure front door dá suporte ao mapeamento e ao gerenciamento de domínios curinga na lista de domínios personalizados.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425755"
---
# <a name="wildcard-domains"></a>Domínios curinga

Além dos domínios e subdomínios do Apex, você também pode mapear um domínio curinga para seus hosts de front-end ou domínios personalizados para seu perfil de porta frontal do Azure. Ter domínios curinga na sua configuração de porta frontal do Azure simplifica o comportamento de roteamento de tráfego para vários subdomínios para uma API, um aplicativo ou um site da mesma regra de roteamento. Você não precisa modificar a configuração para adicionar ou especificar cada subdomínio separadamente. Por exemplo, você pode definir o roteamento para o `customer1.contoso.com` , o `customer2.contoso.com` e `customerN.contoso.com` o usando a mesma regra de roteamento e adicionando o domínio curinga `*.contoso.com` .

Os principais cenários que são aprimorados com suporte para domínios curinga incluem:

- Você não precisa carregar cada subdomínio em seu perfil de porta de entrada do Azure e, em seguida, habilitar HTTPS para associar um certificado a cada subdomínio.
- Não é mais necessário alterar sua configuração de porta de frente do Azure de produção se um aplicativo Adicionar um novo subdomínio. Anteriormente, era necessário adicionar o subdomínio, associar um certificado a ele, anexar uma política de WAF (firewall do aplicativo Web) e, em seguida, adicionar o domínio a diferentes regras de roteamento.

> [!NOTE]
> Atualmente, a adição de domínios curinga por meio do DNS do Azure só tem suporte por meio da API, do PowerShell e do CLI do Azure. O suporte para adicionar e gerenciar domínios curinga no portal do Azure não está disponível.

## <a name="adding-wildcard-domains"></a>Adicionando domínios curinga

Você pode adicionar um domínio curinga na seção para hosts ou domínios de front-end. Semelhante aos subdomínios, a porta frontal do Azure valida que há um mapeamento de registro CNAME para seu domínio curinga. Esse mapeamento de DNS pode ser um mapeamento de registro CNAME direto como `*.contoso.com` mapeado para `contoso.azurefd.net` . Ou você pode usar o mapeamento temporário afdverify. Por exemplo, `afdverify.contoso.com` mapeado para `afdverify.contoso.azurefd.net` valida o mapa de registros CNAME para o curinga.

> [!NOTE]
> O DNS do Azure dá suporte a registros curinga.

Você pode adicionar quantos subdomínios de nível único do domínio curinga em hosts front-end, até o limite dos hosts front-end. Essa funcionalidade pode ser necessária para:

- Definir uma rota diferente para um subdomínio do que o restante dos domínios (do domínio curinga).

- Ter uma política de WAF diferente para um subdomínio específico. Por exemplo, `*.contoso.com` permite adicionar `foo.contoso.com` sem ter que provar novamente a propriedade do domínio. Mas isso não permite `foo.bar.contoso.com` porque não é um subdomínio de nível único do `*.contoso.com` . Para adicionar `foo.bar.contoso.com` sem validação de propriedade de domínio adicional, `*.bar.contosonews.com` é necessário adicionar.

Você pode adicionar domínios curinga e seus subdomínios com determinadas limitações:

- Se um domínio curinga for adicionado a um perfil de porta de recepção do Azure:
  - O domínio curinga não pode ser adicionado a nenhum outro perfil de porta frontal do Azure.
  - Subdomínios de primeiro nível do domínio curinga não podem ser adicionados a outro perfil de porta frontal do Azure ou a um perfil de rede de distribuição de conteúdo do Azure.
- Se um subdomínio de um domínio curinga já estiver adicionado a um perfil de porta de entrega do Azure ou a um perfil de rede de distribuição de conteúdo do Azure, o domínio curinga não poderá ser usado para outro perfil de porta frontal do Azure.
- Se dois perfis (Azure front door ou rede de distribuição de conteúdo do Azure) tiverem vários subdomínios de um domínio raiz, os domínios curinga não poderão ser adicionados a nenhum dos perfis.

## <a name="certificate-binding"></a>Associação de certificado

Para aceitar o tráfego HTTPS em seu domínio curinga, você deve habilitar o HTTPS no domínio curinga. A associação de certificado para um domínio curinga requer um certificado curinga. Ou seja, o nome da entidade do certificado também deve ter o domínio curinga.

> [!NOTE]
> No momento, apenas usar sua própria opção de certificado SSL personalizado está disponível para habilitar HTTPS para domínios curinga. Os certificados gerenciados da porta frontal do Azure não podem ser usados para domínios curinga.

Você pode optar por usar o mesmo certificado curinga de Azure Key Vault ou de certificados gerenciados de porta frontal do Azure para subdomínios.

Se um subdomínio for adicionado a um domínio curinga que já tenha um certificado associado a ele, você não poderá desabilitar o HTTPS para o subdomínio. O subdomínio usa a associação de certificado para o domínio curinga, a menos que uma Key Vault diferente ou o certificado gerenciado da porta de recepção do Azure a substitua.

## <a name="waf-policies"></a>Políticas de WAF

As políticas de WAF podem ser anexadas a domínios curinga, semelhante a outros domínios. Uma política WAF diferente pode ser aplicada a um subdomínio de um domínio curinga. Para os subdomínios, você deve especificar a política WAF a ser usada mesmo que seja a mesma política que o domínio curinga. Os subdomínios *não herdam automaticamente a* política WAF do domínio curinga.

Se não quiser que uma política de WAF seja executada para um subdomínio, você poderá criar uma política de WAF vazia sem RuleSets gerenciados ou personalizados.

## <a name="routing-rules"></a>Regras de roteamento

Ao configurar uma regra de roteamento, você pode selecionar um domínio curinga como um host de front-end. Você também pode ter um comportamento de rota diferente para domínios e subdomínios curinga. Conforme descrito em [como a porta frontal do Azure faz a correspondência de rota](front-door-route-matching.md), a correspondência mais específica para o domínio em diferentes regras de roteamento é escolhida em tempo de execução.

> [!IMPORTANT]
> Você deve ter padrões de caminho correspondentes em suas regras de roteamento ou seus clientes verão falhas. Por exemplo, você tem duas regras de roteamento como A rota 1 ( `*.foo.com/*` mapeadas para o pool de back-end a) e a rota 2 ( `/bar.foo.com/somePath/*` mapeadas para o pool de back-ends B). Em seguida, chega uma solicitação para `bar.foo.com/anotherPath/*` . A porta frontal do Azure seleciona a rota 2 com base em uma correspondência de domínio mais específica, apenas para não encontrar nenhum padrão de caminho correspondente entre as rotas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um perfil de porta de recepção do Azure](quickstart-create-front-door.md).
- Saiba como [Adicionar um domínio personalizado na porta frontal do Azure](front-door-custom-domain.md).
- Saiba como [habilitar o HTTPS em um domínio personalizado](front-door-custom-domain-https.md).
