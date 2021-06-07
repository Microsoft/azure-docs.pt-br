---
title: Configurar ambientes de preparo
description: Saiba como implantar aplicativos em um slot que não seja de produção e o AutoSwap para produção. Aumente a confiabilidade e elimine o tempo de inatividade do aplicativo de implantações.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: c779e95b790d91b801d5d35b4702191f5e7986d5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802957"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de preparo no Serviço de Aplicativo do Azure
<a name="Overview"></a>

Ao implantar seu aplicativo Web, aplicativo Web no Linux, back-end móvel ou aplicativo de API para [Azure app serviço](./overview.md), você pode usar um slot de implantação separado em vez do slot de produção padrão quando estiver executando na camada de plano do serviço de aplicativo **padrão**, **Premium** ou **isolado** . Os slots de implantação são aplicativos ao vivo com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo podem ser trocados entre dois slots de implantação, incluindo o slot de produção. 

A implantação do aplicativo em um slot de não produção traz os seguintes benefícios:

* É possível validar as alterações no aplicativo em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.
* Implantar um aplicativo em um slot primeiro e alterná-lo para produção garantem que todas as instâncias do slot estejam aquecidas antes de alterná-lo para produção. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo. O redirecionamento de tráfego é contínuo, e nenhuma solicitação é removida devido a operações de alternância. Você pode automatizar todo esse fluxo de trabalho configurando a [troca automática](#Auto-Swap) quando a validação de pré-atualização não for necessária.
* Após a troca, o slot com o aplicativo de preparo anterior terá o aplicativo de produção anterior. Se as alterações alternadas para o slot de produção não correspondem às suas expectativas, você pode realizar a mesma alternância imediatamente para ter o "último site válido conhecido" de volta.

Cada tipo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Não há nenhum custo adicional para usar os slots de implantação. Para descobrir o número de Slots com suporte na camada do aplicativo, consulte [limites do serviço de aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Para dimensionar seu aplicativo para uma camada diferente, verifique se a camada de destino dá suporte ao número de slots que seu aplicativo já usa. Por exemplo, se seu aplicativo tiver mais de cinco slots, você não poderá dimensioná-lo para a camada **Standard** , pois a camada **Standard** oferece suporte a apenas cinco slots de implantação. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um slot
O aplicativo deve estar em execução na camada **Standard**, **Premium** ou **Isolated** para que você habilite vários slots de implantação.


1. na [portal do Azure](https://portal.azure.com/), procure e selecione serviços de **aplicativos** e selecione seu aplicativo. 
   
    ![Pesquisar serviços de aplicativos](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. No painel esquerdo, selecione **Slots de implantação**  >  **Adicionar slot**.
   
    ![Adicionar um novo slot de implantação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se o aplicativo ainda não estiver na camada **Standard**, **Premium** ou **Isolated** , você receberá uma mensagem que indica as camadas com suporte para habilitar a publicação em etapas. Neste ponto, você tem a opção de selecionar **Atualizar** e ir para a guia **escala** do seu aplicativo antes de continuar.
   > 

3. Na caixa de diálogo **Adicionar um slot** , dê um nome ao slot e selecione se deseja clonar uma configuração de aplicativo de outro slot de implantação. Selecione **Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Você pode clonar uma configuração de qualquer slot existente. As configurações que podem ser clonadas incluem configurações de aplicativo, cadeias de conexão, versões da estrutura de linguagem, soquetes da Web, versão HTTP e número de bits da plataforma.

4. Depois que o slot for adicionado, selecione **fechar** para fechar a caixa de diálogo. O novo slot agora é mostrado na página **Slots de implantação** . Por padrão, o **tráfego%** é definido como 0 para o novo slot, com todo o tráfego do cliente roteado para o slot de produção.

5. Selecione o novo slot de implantação para abrir a página de recursos do slot.
   
    ![Título do slot de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    O slot de preparo tem uma página de gerenciamento como qualquer outro aplicativo do Serviço de Aplicativo. É possível alterar a configuração do slot. Para lembrá-lo de que você está exibindo o slot de implantação, o nome do aplicativo é mostrado como **\<app-name>/\<slot-name>** e o tipo de aplicativo é **serviço de aplicativo (slot)**. Você também pode ver o slot como um aplicativo separado em seu grupo de recursos, com as mesmas designações.

6. Selecione a URL do aplicativo na página de recursos do slot. O slot de implantação tem seu próprio nome de host e também é um aplicativo ativo. Para limitar o acesso público ao slot de implantação, consulte [Azure app restrições de IP do serviço](app-service-ip-restrictions.md).

O novo slot de implantação não tem nenhum conteúdo, mesmo se as configurações são clonadas de outro slot. Por exemplo, você pode [publicar neste slot com o Git](./deploy-local-git.md). É possível fazer uma implantação no slot de outro branch do repositório ou de outro repositório.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante uma permuta

### <a name="swap-operation-steps"></a>Etapas da operação de permuta

Quando você troca dois slots (geralmente de um slot de preparo no slot de produção), o serviço de aplicativo faz o seguinte para garantir que o slot de destino não experimente tempo de inatividade:

1. Aplique as seguintes configurações do slot de destino (por exemplo, o slot de produção) a todas as instâncias do slot de origem: 
    - Configurações do aplicativo e cadeias [de conexão específicas do slot](#which-settings-are-swapped) , se aplicável.
    - Configurações de [implantação contínua](deploy-continuous-deployment.md) , se habilitadas.
    - Configurações de [autenticação do serviço de aplicativo](overview-authentication-authorization.md) , se habilitadas.
    
    Qualquer um desses casos disparará todas as instâncias no slot de origem para reiniciar. Durante a [troca com visualização](#Multi-Phase), isso marca o final da primeira fase. A operação de permuta está pausada e você pode validar que o slot de origem funciona corretamente com as configurações do slot de destino.

1. Aguarde até que cada instância no slot de origem conclua sua reinicialização. Se alguma instância não for reiniciada, a operação de permuta reverterá todas as alterações no slot de origem e interromperá a operação.

1. Se o [cache local](overview-local-cache.md) estiver habilitado, dispare a inicialização do cache local fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem. Aguarde até que cada instância retorne qualquer resposta HTTP. A inicialização do cache local causa outra reinicialização em cada instância.

1. Se a [troca automática](#Auto-Swap) estiver habilitada com a inicialização [personalizada](#Warm-up)do [aplicativo](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) de disparo, fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem.

    Se `applicationInitialization` não for especificado, dispare uma solicitação HTTP para a raiz do aplicativo do slot de origem em cada instância. 
    
    Se uma instância retornar qualquer resposta HTTP, ela será considerada como ativada.

1. Se todas as instâncias no slot de origem forem ativadas com êxito, troque os dois slots alternando as regras de roteamento para os dois slots. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que foi anteriormente ativado no slot de origem.

1. Agora que o slot de origem tem o aplicativo de pré-permuta anteriormente no slot de destino, execute a mesma operação aplicando todas as configurações e reiniciando as instâncias.

Em qualquer ponto da operação de permuta, todo o trabalho de inicializar os aplicativos trocados ocorre no slot de origem. O slot de destino permanece online enquanto o slot de origem está sendo preparado e ativado, independentemente de onde a troca tenha êxito ou falhe. Para trocar um slot de preparo pelo slot de produção, verifique se o slot de produção é sempre o slot de destino. Dessa forma, a operação de permuta não afeta seu aplicativo de produção.

### <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Para configurar uma cadeia de conexão ou configuração de aplicativo para se conectar a um slot específico (não trocado), vá para a página de **configuração** desse slot. Adicione ou edite uma configuração e, em seguida, selecione **configuração do slot de implantação**. Marcar essa caixa de seleção informa ao serviço de aplicativo que a configuração não é intercambiável. 

![Configuração do slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Alternar dois slots 
Você pode alternar os slots de implantação na página de **Slots de implantação** do aplicativo e na página **visão geral** . Para obter detalhes técnicos sobre a troca de slot, consulte [o que acontece durante a permuta](#AboutConfiguration).

> [!IMPORTANT]
> Antes de trocar um aplicativo de um slot de implantação em produção, verifique se a produção é o slot de destino e se todas as configurações no slot de origem estão configuradas exatamente como você deseja tê-las em produção.
> 
> 

Para permutar slots de implantação:

1. Vá para a página **Slots de implantação** do aplicativo e selecione **alternar**.
   
    ![Botão Alternar](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **alternar** mostra as configurações nos slots de origem e destino selecionados que serão alterados.

2. Selecione os slots de **Origem** e de **Destino** desejados. Geralmente, o destino é o slot de produção. Além disso, selecione as guias **alterações de origem** e **alterações de destino** e verifique se as alterações de configuração são esperadas. Quando tiver terminado, você poderá trocar os slots imediatamente selecionando **alternar**.

    ![Troca completa](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o slot de destino seria executado com as novas configurações antes que a permuta realmente aconteça, não selecione **alternar**, mas siga as instruções em [alternar com visualização](#Multi-Phase).

3. Quando tiver terminado, feche a caixa de diálogo selecionando **fechar**.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troca com visualização (troca de várias fases)

Antes de trocar para produção como o slot de destino, valide se o aplicativo é executado com as configurações trocadas. O slot de origem também é ativado antes da conclusão da permuta, o que é desejável para aplicativos de missão crítica.

Quando você executa uma troca com visualização, o serviço de aplicativo executa a mesma [operação de permuta](#AboutConfiguration) , mas pausa após a primeira etapa. Em seguida, você pode verificar o resultado no slot de preparo antes de concluir a permuta. 

Se você cancelar a troca, o serviço de aplicativo reaplicará os elementos de configuração ao slot de origem.

Para alternar com a visualização:

1. Siga as etapas em [trocar slots de implantação](#Swap) , mas selecione **executar permuta com visualização**.

    ![Alternância com visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra como a configuração no slot de origem muda na fase 1 e como a origem e o slot de destino são alterados na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **Iniciar permuta**.

    Quando a fase 1 for concluída, você será notificado na caixa de diálogo. Visualize a troca no slot de origem acessando `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Quando estiver pronto para concluir a permuta pendente, selecione **concluir permuta** na **ação de permuta** e selecione **concluir permuta**.

    Para cancelar uma permuta pendente, selecione **Cancelar permuta** em vez disso.

4. Quando tiver terminado, feche a caixa de diálogo selecionando **fechar**.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

Para automatizar uma troca de várias fases, consulte [automatizar com o PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Reverter uma permuta
Se ocorrerem erros no slot de destino (por exemplo, o slot de produção) após uma alternância de slot, restaure os slots para seus estados de pré-alternância alternando os mesmos dois slots imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática

> [!NOTE]
> A troca automática não tem suporte em aplicativos Web no Linux.

A troca automática simplifica os cenários de DevOps do Azure em que você deseja implantar seu aplicativo continuamente com inícios inativos sem interrupção e sem tempo de inatividade para clientes do aplicativo. Quando a troca automática está habilitada de um slot para produção, sempre que você envia por push as alterações de código para esse slot, [o serviço de aplicativo troca automaticamente o aplicativo em produção](#swap-operation-steps) depois que ele é ativado no slot de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para o slot de produção, considere testar a troca automática em um slot de destino de não produção.
   > 

Para configurar a troca automática:

1. Vá para a página de recursos do aplicativo. Selecione **Slots de implantação**  >  *\<desired source slot>*  >  **configuração**  >  **geral configurações**.
   
2. Para a **troca automática habilitada**, selecione **ativado**. Em seguida, selecione o slot de destino desejado para o **slot de implantação de permuta automática** e selecione **salvar** na barra de comandos. 
   
    ![Seleções para configurar a troca automática](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para o slot de origem. A troca automática ocorre após um curto período de tempo e a atualização é refletida na URL do slot de destino.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especificar aquecimento personalizado

Alguns aplicativos podem exigir ações de aquecimento personalizadas antes da troca. O `applicationInitialization` elemento de configuração no web.config permite que você especifique ações de inicialização personalizadas. A [operação de permuta](#AboutConfiguration) aguarda que esse aquecimento personalizado seja concluído antes de alternar com o slot de destino. Aqui está um fragmento de web.config de exemplo.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Para obter mais informações sobre como personalizar o `applicationInitialization` elemento, consulte [falhas de permuta de slot de implantação mais comuns e como corrigi-los](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Você também pode personalizar o comportamento de aquecimento com uma ou ambas as seguintes configurações de [aplicativo](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para o ping para aquecimento do seu site. Adicione essa configuração de aplicativo especificando um caminho personalizado que começa com uma barra (“/”) como o valor. Um exemplo é `/statuscheck`. O valor padrão é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para a operação de aquecimento. Adicione essa configuração de aplicativo com uma lista separada por vírgulas dos códigos HTTP. Um exemplo é `200,202` . Se o código de status retornado não estiver na lista, as operações aquecimento e swap serão interrompidas. Por padrão, todos os códigos de resposta são válidos.
- `WEBSITE_WARMUP_PATH`: Um caminho relativo no site que deve ser pingado sempre que o site for reiniciado (não apenas durante trocas de slot). Os valores de exemplo incluem `/statuscheck` ou o caminho raiz, `/` .

> [!NOTE]
> O `<applicationInitialization>` elemento de configuração faz parte de cada inicialização de aplicativo, enquanto as duas configurações de aplicativo de comportamento de aquecimento se aplicam somente a trocas de slot.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorar uma troca

Se a [operação de permuta](#AboutConfiguration) levar muito tempo para ser concluída, você poderá obter informações sobre a operação de permuta no [log de atividades](../azure-monitor/essentials/platform-logs-overview.md).

Na página de recursos do aplicativo no portal, no painel esquerdo, selecione log de **atividades**.

Uma operação de troca é exibida na consulta de log como `Swap Web App Slots`. Você pode expandi-lo e selecionar uma das suboperações ou um dos erros para ver os detalhes.

## <a name="route-traffic"></a>Rotear o tráfego

Por padrão, todas as solicitações de cliente para a URL de produção do aplicativo (`http://<app_name>.azurewebsites.net`) são roteadas para o slot de produção. É possível rotear uma parte do tráfego para outro slot. Esse recurso é útil se você precisa de comentários do usuário para uma nova atualização, mas não está pronto para liberá-la para produção.

### <a name="route-production-traffic-automatically"></a>Rotear o tráfego de produção automaticamente

Para rotear o tráfego de produção automaticamente:

1. Vá para a página de recursos do aplicativo e selecione **Slots de implantação**.

2. Na coluna **% do Tráfego** do slot para o qual você deseja rotear, especifique um percentual (entre 0 e 100) para representar a quantidade de tráfego total que deseja rotear. Selecione **Salvar**.

    ![Definindo um percentual de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois que a configuração é salva, a porcentagem especificada de clientes é roteada aleatoriamente para o slot de não produção. 

Depois que um cliente é roteado automaticamente para um slot específico, ele é "fixado" a esse slot durante a vida útil dessa sessão de cliente. No navegador do cliente, você pode ver em qual slot a sessão está fixada observando o cookie `x-ms-routing-name` nos cabeçalhos HTTP. Uma solicitação roteada para o slot "de preparo" tem o cookie `x-ms-routing-name=staging`. Uma solicitação roteada para o slot de produção tem o cookie `x-ms-routing-name=self`.

   > [!NOTE]
   > Ao lado do portal do Azure, você também pode usar o [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) comando na CLI do Azure para definir as porcentagens de roteamento de ferramentas de CI/CD, como pipelines DevOps ou outros sistemas de automação.
   > 

### <a name="route-production-traffic-manually"></a>Rotear o tráfego de produção manualmente

Além do roteamento de tráfego automático, o Serviço de Aplicativo pode rotear solicitações para um slot específico. Isso é útil quando você deseja que os usuários possam aceitar ou recusar seu aplicativo beta. Para rotear o tráfego de produção manualmente, use o parâmetro de consulta `x-ms-routing-name`.

Para permitir que os usuários recusem seu aplicativo beta, por exemplo, você pode colocar esse link em sua página da Web:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia de caracteres `x-ms-routing-name=self` especifica o local de produção. Depois que o navegador do cliente acessa o link, ele é redirecionado para o slot de produção. Cada solicitação subsequente tem o `x-ms-routing-name=self` cookie que fixa a sessão ao slot de produção.

Para permitir que os usuários aceitem seu aplicativo beta, defina o mesmo parâmetro de consulta como o nome do slot de não produção. Veja um exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por padrão, novos slots recebem uma regra de roteamento `0%` , mostrada em cinza. Quando você define esse valor explicitamente como `0%` (mostrado em texto preto), os usuários podem acessar o slot de preparo manualmente usando o `x-ms-routing-name` parâmetro de consulta. Mas eles não serão roteados para o slot automaticamente porque a porcentagem de roteamento é definida como 0. Esse é um cenário avançado em que você pode "Ocultar" o slot de preparo do público, permitindo que as equipes internas testem as alterações no slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Excluir um slot

Pesquise e selecione seu aplicativo. Selecione   >  *\<slot to delete>*  >  **visão geral dos** slots de implantação. O tipo de aplicativo é mostrado como **serviço de aplicativo (slot)** para lembrá-lo de que você está exibindo um slot de implantação. Selecione **excluir** na barra de comandos.  

![Excluir um slot de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação no Serviço de Aplicativo do Azure.

Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](/powershell/azure/).  

---
### <a name="create-a-web-app"></a>Criar um aplicativo Web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Criar um slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Iniciar uma troca com uma visualização (troca de várias fases) e aplicar a configuração do slot de destino ao slot de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancelar uma permuta pendente (trocar com revisão) e restaurar a configuração do slot de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Permute slots de implantação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorar eventos de permuta no log de atividades
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Excluir um slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizar com modelos do Resource Manager

Os [modelos de Azure Resource Manager](../azure-resource-manager/templates/overview.md) são arquivos JSON declarativos usados para automatizar a implantação e a configuração de recursos do Azure. Para trocar os slots usando modelos do Resource Manager, você definirá duas propriedades nos recursos *Microsoft. Web/sites/Slots* e *Microsoft. Web/sites* :

- `buildVersion`: essa é uma propriedade de cadeia de caracteres que representa a versão atual do aplicativo implantado no slot. Por exemplo: "v1", "1.0.0.1" ou "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: essa é uma propriedade de cadeia de caracteres que especifica o que `buildVersion` o slot deve ter. Se o targetBuildVersion não for igual ao atual `buildVersion` , isso disparará a operação de permuta localizando o slot que tem o especificado `buildVersion` .

### <a name="example-resource-manager-template"></a>Exemplo de modelo do Resource Manager

O modelo do Resource Manager a seguir atualizará o `buildVersion` slot de preparo e definirá o `targetBuildVersion` no slot de produção. Isso mudará os dois slots. O modelo pressupõe que você já tenha um webapp criado com um slot chamado "preparo".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Esse modelo do Resource Manager é idempotente, o que significa que ele pode ser executado repetidamente e produzir o mesmo estado dos slots. Após a primeira execução, o `targetBuildVersion` corresponderá ao atual `buildVersion` , de modo que uma troca não será disparada.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizar com a CLI

Para obter os comandos da [CLI do Azure](https://github.com/Azure/azure-cli) para slots de implantação, confira [Slot de implantação do az webapp](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Solucionar problemas de trocas

Se ocorrer algum erro durante uma [troca de slot](#AboutConfiguration), ele será registrado *D:\home\LogFiles\eventlog.xml*. Ele também é registrado no log de erros específico do aplicativo.

Aqui estão alguns erros de permuta comuns:

- Uma solicitação HTTP para a raiz do aplicativo é cronometrada. A operação de permuta aguarda por 90 segundos para cada solicitação HTTP e tenta novamente até 5 vezes. Se todas as novas tentativas atingirem o tempo limite, a operação de permuta será interrompida.

- A inicialização do cache local poderá falhar quando o conteúdo do aplicativo exceder a cota de disco local especificada para o cache local. Para obter mais informações, consulte [visão geral do cache local](overview-local-cache.md).

- Durante o [aquecimento personalizado](#Warm-up), as solicitações HTTP são feitas internamente (sem passar pela URL externa). Eles podem falhar com determinadas regras de regravação de URL no *Web.config*. Por exemplo, regras para redirecionar nomes de domínio ou impor HTTPS podem impedir que solicitações de aquecimento atinjam o código do aplicativo. Para contornar esse problema, modifique suas regras de reescrita adicionando as duas condições a seguir:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de regravação de URL ainda podem bloquear solicitações HTTP. Para contornar esse problema, modifique suas regras de reescrita adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Após trocas de slot, o aplicativo pode experimentar reinicializações inesperadas. Isso ocorre porque, após uma troca, a configuração de associação de nome de host fica fora de sincronia, o que por si só não causa reinicializações. No entanto, determinados eventos de armazenamento subjacentes (como failovers de volume de armazenamento) podem detectar essas discrepâncias e forçar a reinicialização de todos os processos de trabalho. Para minimizar esses tipos de reinicializações, defina a [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` configuração do aplicativo](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) em *todos os slots*. No entanto, essa configuração de aplicativo *não funciona com* aplicativos Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Próximas etapas
[Bloquear o acesso aos slots de não produção](app-service-ip-restrictions.md)
