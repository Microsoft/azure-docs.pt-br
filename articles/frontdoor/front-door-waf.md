---
title: 'Tutorial: Escalar e proteger um aplicativo Web usando o Azure Front Door e o WAF (Firewall de Aplicativo Web) do Azure'
description: Este tutorial mostrará como usar o Firewall de Aplicativo Web do Azure com o Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: d315fa5b588c6e5f2e4643ca18626e400e6ca01b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785641"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Tutorial: Escalar e proteger rapidamente um aplicativo Web usando o Azure Front Door e o WAF (Firewall de Aplicativo Web do Azure)

Nas últimas semanas, muitos aplicativos Web experimentaram um rápido aumento de tráfego por conta da COVID-19. Esses aplicativos Web também estão enfrentando um aumento no tráfego mal-intencionado, incluindo ataques de negação de serviço. Existe uma forma eficaz de escalar horizontalmente seu aplicativo para picos de tráfego e se proteger contra ataques: configure o Azure Front Door com o WAF do Azure como uma camada de aceleração, armazenamento em cache e segurança na frente do seu aplicativo Web. Este artigo mostra as diretrizes para configurar o Azure Front Door com o WAF do Azure para qualquer aplicativo Web em execução dentro ou fora do Azure. 

Use a CLI do Azure para configurar o WAF neste tutorial. Faça a mesma coisa usando o portal do Azure, o Azure PowerShell, o Azure Resource Manager ou as APIs REST do Azure. 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar um Front Door.
> - Criar uma política de WAF do Azure.
> - Configurar conjuntos de regras para uma política de WAF.
> - Associar uma política de WAF ao Front Door.
> - Configurar um domínio personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- As instruções deste tutorial usam a CLI do Azure. [Veja este guia](/cli/azure/get-started-with-azure-cli) para obter uma introdução à CLI do Azure.

  > [!TIP] 
  > Uma forma fácil e rápida de começar a usar a CLI do Azure é com o [Bash no Azure Cloud Shell](../cloud-shell/quickstart.md).

- Adicione a extensão `front-door` à CLI do Azure:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Para obter mais informações sobre os comandos usados neste tutorial, confira a [referência da CLI do Azure para o Front Door](/cli/azure/ext/front-door).

## <a name="create-an-azure-front-door-resource"></a>Criar um recurso do Azure Front Door

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: o FQDN (nome de domínio totalmente qualificado) do aplicativo que você deseja proteger. Por exemplo, `myapplication.contoso.com`.

`--accepted-protocols`: especifica os protocolos para os quais você deseja que o Azure Front Door dê suporte no seu aplicativo Web. Por exemplo, `--accepted-protocols Http Https`.

`--name`: o nome do recurso do Azure Front Door.

`--resource-group`: o grupo de recursos no qual você deseja colocar este recurso do Azure Front Door. Para saber mais sobre grupos de recursos, confira [Gerenciar grupos de recursos no Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

Na resposta obtida ao executar esse comando, procure a chave `hostName`. Você precisará desse valor em uma etapa posterior. O `hostName` é o nome DNS do recurso do Azure Front Door criado.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Criar um perfil de WAF do Azure para usar com os recursos do Azure Front Door

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: o nome da nova política de WAF do Azure.

`--resource-group`: o grupo de recursos no qual você deseja colocar este recurso do WAF. 

O código anterior da CLI criará uma política de WAF habilitada e que está no modo de prevenção. 

> [!NOTE] 
> O ideal é criar a política de WAF no modo de detecção e observar como ela detecta e registra em log as solicitações mal-intencionadas (sem bloqueá-las) antes de optar por usar o modo de proteção.

Na resposta obtida ao executar esse comando, procure a chave `ID`. Você precisará desse valor em uma etapa posterior. 

O campo `ID` deverá estar neste formato:

/subscriptions/**subscription id**/resourcegroups/**resource group name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Adicionar conjuntos de regras gerenciados à política de WAF

Adicione conjuntos de regras gerenciados a uma política de WAF. Um conjunto de regras gerenciado é um conjunto de regras criado e gerenciado pela Microsoft que ajudam a proteger você contra uma classe de ameaças. Neste exemplo, adicionamos dois conjuntos de regras:
- O conjunto de regras padrão, que ajuda a proteger você contra ameaças comuns da Web. 
- O conjunto de regras de proteção contra bots, que ajuda a proteger você contra bots mal-intencionados.

Adicione o conjunto de regras padrão:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Adicione o conjunto de regras de proteção contra bots:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: o nome especificado para o recurso do WAF do Azure.

`--resource-group`: o grupo de recursos no qual você colocou o recurso do WAF.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Associar a política de WAF ao recurso do Azure Front Door

Nesta etapa, associaremos a política de WAF que criamos com o recurso do Azure Front Door que está na frente do seu aplicativo Web:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: o nome especificado para o recurso do Azure Front Door.

`--resource-group`: o grupo de recursos no qual você colocou o recurso do Azure Front Door.

`--set`: é aqui que você atualiza o atributo `WebApplicationFirewallPolicyLink` do `frontendEndpoint` associado ao recurso do Azure Front Door com a nova política de WAF. Você deverá ter a ID da política de WAF da resposta que obteve ao criar o perfil de WAF anteriormente neste tutorial.

 > [!NOTE] 
> O exemplo anterior é aplicável quando você não está usando um domínio personalizado. Se você não está usando nenhum domínio personalizado para acessar seus aplicativos Web, ignore a próxima seção. Nesse caso, você dará aos seus clientes o `hostName` obtido quando criou o recurso do Azure Front Door. Eles usarão esse `hostName` para acessar seu aplicativo Web.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Configurar o domínio personalizado para seu aplicativo Web

O nome de domínio personalizado do seu aplicativo Web é aquele que os clientes usam para se referirem ao seu aplicativo. Por exemplo, www.contoso.com. Inicialmente, esse nome de domínio personalizado apontava para a localização em que ele estava em execução antes da introdução do Azure Front Door. Depois que você adicionar o Azure Front Door e o WAF à frente do aplicativo, a entrada DNS que corresponde a esse domínio personalizado deverá apontar para o recurso do Azure Front Door. Faça essa alteração remapeando a entrada no servidor DNS para o `hostName` do Azure Front Door que você anotou ao criar o recurso do Azure Front Door.

As etapas específicas necessárias para atualizar os registros DNS dependerão do provedor de serviços DNS. Se você usar o DNS do Azure para hospedar seu nome DNS, veja a documentação para obter as [etapas necessárias para atualizar um registro DNS](../dns/dns-operations-recordsets-cli.md) e apontá-lo para o `hostName` do Azure Front Door. 

Há uma coisa importante a ser observada se você precisar que os clientes acessem o seu site usando o ápice da zona (por exemplo, contoso.com). Nesse caso, você precisará usar o DNS do Azure e o [tipo de registro de alias](../dns/dns-alias.md) para hospedar seu nome DNS. 

Você também precisará atualizar a configuração do Azure Front Door para [adicionar o domínio personalizado](./front-door-custom-domain.md) a ele para que ele esteja ciente desse mapeamento.

Por fim, se você estiver usando um domínio personalizado para acessar seu aplicativo Web e quiser habilitar o protocolo HTTPS. Você precisa [configurar os certificados para o seu domínio personalizado no Azure Front Door](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Bloquear seu aplicativo Web

É recomendável verificar se apenas as bordas do Azure Front Door podem se comunicar com seu aplicativo Web. Isso garantirá que nenhuma delas ignore a proteção do Azure Front Door e acesse seu aplicativo diretamente. Para realizar esse bloqueio, confira [Como fazer para bloquear o acesso ao meu back-end para apenas o Azure Front Door?](./front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos usados neste tutorial, use o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos, o Front Door e a política de WAF:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: o nome do grupo de recursos de todos os recursos usados neste tutorial.

## <a name="next-steps"></a>Próximas etapas

Para saber como solucionar problemas do Front Door, confira os guias de solução de problemas:

> [!div class="nextstepaction"]
> [Solução de problemas comuns de roteamentos](front-door-troubleshoot-routing.md)