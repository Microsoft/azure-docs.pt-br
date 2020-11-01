---
title: Configurar ambientes de staging
description: Saiba como implantar aplicativos em um slot não produtivo e fazer o auto swap para produção. Aumente a confiabilidade e acabe com o downtime ao fazer deploys.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: ef90603e8c8cdd66d43b9f88f6d128d8a472fd8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150331"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de staging no Azure App Service
<a name="Overview"></a>

Ao implantar seu aplicativo web, aplicativo web no Linux, back-end para mobile ou aplicativo de API no [Azure App Service](./overview.md), você pode usar um slot de implantação apartado, em vez do slot padrão de produção ao utilizar o plano **Standard**, **Premium** ou **Isolado** do Serviço de Aplicativo. Os slots de implantação são aplicativos funcionais que possuem seus próprios host names. Os elementos de configurações e o conteúdo dos aplicativos podem ser alternados entre dois slots de implantação, incluindo o slot de produção.

A implantação do aplicativo em um slot não produtivo traz os seguintes benefícios:

* É possível validar as alterações no aplicativo em um slot de implantação de staging antes de fazer o swapp com o slot de produção.
* Implantar um aplicativo primeiro em um slot e depois promovê-lo para produção garante que todas as instâncias do slot estejam aquecidas antes de promovê-lo para produção. Isso elimina o downtime quando você for fazer deploy do seu aplicativo. O redirecionamento de tráfego é ininterrupto, e nenhuma request é perdida devido a operação de swap. Você pode automatizar todo esse fluxo configurando o [auto swap](#Auto-Swap) quando uma validação antes de realizar o swap não for necessária.
* Após o swap, o slot que anteriormente continha o aplicativo de staging passará então a conter o aplicativo de produção. Se as alterações promovidas para o slot de produção não corresponderem às suas expectativas, você poderá realizar a mesma troca imediatamente para obter a "última versão funcional do site" de volta.

Cada tipo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Não há nenhum custo adicional para usar os slots de implantação. Para descobrir a quantidade de slots que a camada do seu aplicativo suporta, consulte [limites do Serviço de Aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

Para escalar seu aplicativo para uma camada diferente, verifique se a camada desejada dá suporte ao número de slots que seu aplicativo já utiliza. Por exemplo, se seu aplicativo tiver mais de cinco slots, você não poderá dimensioná-lo para a camada **Standard**, pois a camada **Standard** oferece suporte a apenas cinco slots de implantação.

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um slot
O aplicativo deve estar em execução na camada **Standard**, **Premium** ou **Isolado** para que você possa habilitar multiplos slots de implantação.


1. no [Portal do Azure](https://portal.azure.com/), pesquise e selecione **Serviços de Aplicativos** e selecione o seu aplicativo.
   
    ![Pesquisar no Serviço de Aplicativo](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. No painel do lado esquerdo, selecione **Slots de implantação**  >  **Adicionar Slot**.
   
    ![Adicionar um novo slot de implantação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se o aplicativo ainda não estiver na camada **Standard**, **Premium** ou **Isolado**, você receberá uma mensagem indicando quais camadas dão suporte para habilitar a publicação em stage. Nesse momento, você terá a opção de selecionar **Atualizar** e ir para a guia **Escalar** do seu aplicativo, antes de continuar.
   > 

3. Na caixa de diálogo **Adicionar um slot**, dê um nome ao slot e selecione se deseja clonar uma configuração de aplicativo de outro slot de implantação. Selecione **Adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Você pode clonar uma configuração de qualquer outro slot existente. As configurações que podem ser clonadas incluem configurações de aplicativo, strings de conexão, versões do framework da linguagem, web sockets, versão do HTTP e número de bits da plataforma.

4. Depois que o slot for adicionado, selecione **Fechar** para fechar a caixa de diálogo. O novo slot agora é exibido na página **Slots de implantação**. Por padrão, o **Tráfego %** é definido como 0 para o novo slot, com todo o tráfego do cliente sendo roteado para o slot de produção.

5. Selecione o novo slot de implantação para abrir a página de recursos do slot.
   
    ![Título do slot de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    O slot de staging tem uma página de gerenciamento como qualquer outro aplicativo do Serviço de Aplicativo. É possível alterar a configuração do slot. Para lembrá-lo de que você está visualizando o slot de implantação, o nome do aplicativo é exibido como **\<nome-do-aplicativo>/\<nome-do-slot>** e o tipo do aplicativo é **Serviço de Aplicativo (Slot)**. Você também pode ver o slot como um aplicativo apartado em seu grupo de recursos, com as mesmas designações.

6. Selecione a URL do aplicativo na página de recursos do slot. O slot de implantação possui seu próprio host name e também é um aplicativo funcional. Para limitar acesso público ao slot de implantação, consulte [Restrições de IP no Serviço de Aplicativo do Azure](app-service-ip-restrictions.md).

O novo slot de implantação não possui nenhum conteúdo, mesmo que você clone as configurações de outro slot. Por exemplo, você pode [publicar nesse slot a partir do Git](./deploy-local-git.md). Você pode fazer deploy no slot a partir de outra branch ou a partir de outro repositório.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante um swap

### <a name="swap-operation-steps"></a>Etapas da operação de swap

Quando você faz o swap entre dois slots (geralmente de um slot de staging para o slot de produção), o Serviço de Aplicativo faz o seguinte para garantir que o slot de destino não fique indisponível:

1. Aplica as seguintes configurações do slot de destino (por exemplo, o slot de produção) em todas as instâncias do slot de origem:
    - [Configurações de aplicativo específicas do slot](#which-settings-are-swapped) e strings de conexão, se aplicável.
    - Configurações de [continuous deployment](deploy-continuous-deployment.md), se habilitado.
    - Configurações de [autenticação do Serviço de Aplicativo](overview-authentication-authorization.md), se habilitado.
    
    Em qualquer dos casos todas as instâncias no slot de origem serão reiniciadas. Durante a [troca com pré-visualização](#Multi-Phase), isso marca o final da primeira fase. A operação de swap será pausada e você poderá validar se o slot de origem funciona corretamente com as configurações do slot de destino.

1. Aguarda até que cada uma das instâncias no slot de origem conclua a sua reinicialização. Se alguma instância falhar ao reiniciar, a operação de swap reverterá todas as alterações no slot de origem e interromperá a operação.

1. Se o [cache local](overview-local-cache.md) estiver habilitado, dispara a inicialização do cache local fazendo uma requisição HTTP na raiz da aplicação ("/") em cada uma das instâncias do slot de origem. Aguarda até que cada uma das instâncias retorne alguma resposta HTTP. A inicialização do cache local causa outra reinicialização em cada uma das instâncias.

1. Se o [auto swap](#Auto-Swap) estiver habilitado com o [aquecimento personalizado](#Warm-up), dispara a [Inicialização do Aplicativo](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) fazendo uma requisição HTTP na raiz da aplicação ("/") em cada uma das instâncias do slot de origem.

    Se `applicationInitialization` não for especificado, dispara uma requisição HTTP na raiz da aplicação do slot de origem em cada uma das instâncias.
    
    Se uma instância retornar alguma resposta HTTP, ela será considerada como estando aquecida.

1. Se todas as instâncias no slot de origem forem aquecidas com sucesso, troca os dois slots alternando as regras de roteamento para os dois slots. Após essa etapa, o slot de destino (por exemplo, o slot de produção) irá conter o aplicativo que foi anteriormente aquecido no slot de origem.

1. Agora que o slot de origem contém o aplicativo pré-swap que estava anteriormente no slot de destino, executa a mesma operação aplicando todas as configurações e reiniciando as instâncias.

Em qualquer ponto da operação de swap, todo o trabalho de inicializar os aplicativos trocados ocorre no slot de origem. O slot de destino permanece online enquanto o slot de origem está sendo preparado e aquecido, independentemente de onde a troca tenha êxito ou falhe. Para trocar um slot de staging pelo slot de produção, certifique-se de que o slot de produção é sempre o slot de destino. Dessa forma, a operação de swap não afeta o seu aplicativo de produção.

### <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Para configurar uma string de conexão ou configuração de aplicativo para se fixar a um slot específico (não trocado), vá para a página de **Configuração** deste slot. Adicione ou edite uma configuração e, em seguida, selecione **configuração do slot de implantação**. Marcar essa caixa de seleção informa ao Serviço de Aplicativo que a configuração não é intercambiável.

![Configuração do slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Alternar dois slots
Você pode alternar os slots de implantação na página de **Slots de implantação** do aplicativo e na página **Visão geral**. Para obter detalhes técnicos sobre o swap de slot, consulte [o que acontece durante um swap](#AboutConfiguration).

> [!IMPORTANT]
> Antes de alternar um aplicativo de um slot de implantação para produção, certifique-se de que produção é o slot de destino e que todas as configurações no slot de origem estão configuradas exatamente como você deseja tê-las em produção.
> 
> 

Para alternar slots de implantação:

1. Vá para a página **Slots de implantação** do aplicativo e selecione **Alternar**.
   
    ![Botão Alternar](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A caixa de diálogo **Alternar** mostra as configurações nos slots de origem e destino selecionados, que serão alternados.

2. Selecione os slots de **Origem** e de **Destino** desejados. Geralmente, o destino é o slot de produção. Além disso, selecione as guias **Alterações de Origem** e **Alterações de Destino** e verifique se as alterações de configuração são esperadas. Quando tiver terminado, você poderá trocar os slots imediatamente selecionando **Alternar**.

    ![Troca completa](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como o slot de destino seria executado com as novas configurações antes que o swap realmente aconteça, não selecione **Alternar**, ao invés disso siga as instruções em [Swap com pré-visualização](#Multi-Phase).

3. Quando tiver terminado, feche a caixa de diálogo selecionando **Fechar**.

Se você tiver algum problema, consulte [Solucionar problemas com swaps](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Swap com pré-visualização (swap de multiplas fases)

Antes de trocar para produção como slot de destino, valide se o aplicativo funciona com as configurações alternadas. O slot de origem também é aquecido antes da conclusão do swap, o que é desejável para aplicativos de missão crítica.

Quando você executa uma troca com pré-visualização, o Serviço de Aplicativo executa a mesma [operação de swap](#AboutConfiguration), mas pausa após a primeira etapa. Em seguida, você pode verificar o resultado no slot de staging antes de concluir o swap.

Se você cancelar a troca, o Serviço de Aplicativo reaplicará os elementos de configuração no slot de origem.

Para alternar com pré-visualização:

1. Siga as etapas em [Alternar slots de implantação](#Swap), porem selecione **Executar swap com pré-visualização**.

    ![Swap com pré-visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra como a configuração no slot de origem muda na fase 1, e como a origem e o slot de destino serão alterados na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **Iniciar Swap**.

    Quando a fase 1 for concluída, você será notificado na caixa de diálogo. Visualize a troca no slot de origem acessando `https://<nome_do_aplicativo>-<nome-slot-origem>.azurewebsites.net`.

3. Quando estiver pronto para concluir o swap que esta pendente, selecione **Concluir swap** em **Ação de swap** e selecione **Concluir swap**.

    Para cancelar um swap pendente, ao invés disso selecione **Cancelar swap**.

4. Quando tiver terminado, feche a caixa de diálogo selecionando **Fechar**.

Se você tiver algum problema, consulte [Solucionar problemas com swaps](#troubleshoot-swaps).

Para automatizar um swap de multiplas fases, consulte [Automatizar com PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Reverter um swap
Se ocorrerem erros no slot de destino (por exemplo, o slot de produção) após uma troca de slot, restaure os slots para seus estados pré-swap alternando os mesmos dois slots imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurando o auto swap

> [!NOTE]
> Não ha suporte para a troca automática em aplicativos Web no Linux.

A troca automática simplifica cenários no Azure DevOps onde você deseje implantar seu aplicativo de forma continua sem interrupções e sem downtime para os consumidores do aplicativo. Quando a troca automática de um slot para produção está habilitada, toda vez que você enviar alterações de código por push para este slot, o Serviço de Aplicativo [alterna automaticamente o aplicativo para produção](#swap-operation-steps) depois que ele for aquecido no slot de origem.

   > [!NOTE]
   > Antes de configurar a troca automática para o slot de produção, considere testar a troca automática em um slot de destino não produtivo.
   > 

Para configurar a troca automática:

1. Vá para a página de recursos do aplicativo. Selecione **Slots de implantação** > *\<slot de origem desejado>* > **Configuração** > **Configurações gerais**.
   
2. Em **Troca automática habilitada**, selecione **Ativado**. Em seguida, selecione o slot de destino desejado para o **Slot de implantação de troca automática** e selecione **Salvar** na barra de comandos.
   
    ![Seleções para configurar a troca automática](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para o slot de origem. A troca automática ocorre após um curto período de tempo e a atualização é refletida na URL do slot de destino.

Se você tiver algum problema, consulte [Solucionar problemas com swaps](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especificar aquecimento personalizado

Alguns aplicativos podem exigir ações personalizadas de aquecimento antes do swap. O elemento de configuração `applicationInitialization` no web.config permite que você especifique ações de inicialização personalizadas. A [operação de swap](#AboutConfiguration) aguarda que esse aquecimento personalizado seja concluído antes de alternar com o slot de destino. Aqui está um trecho de web.config como exemplo.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[host name do aplicativo]" />
        <add initializationPage="/Home/About" hostName="[host name do aplicativo]" />
    </applicationInitialization>
</system.webServer>
```

Para obter mais informações sobre como personalizar o elemento `applicationInitialization`, consulte [Falhas de swap de slot de implantação mais comuns e como corrigi-las](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Você também pode personalizar o comportamento de aquecimento com uma, ou ambas, as seguintes [configurações de aplicativo](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para o ping de aquecimento do seu site. Adicione essa configuração de aplicativo especificando um caminho personalizado que começe com uma barra (“/”) como valor. Um exemplo é `/statuscheck`. O valor padrão é `/`.
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para a operação de aquecimento. Adicione essa configuração de aplicativo com uma lista de códigos HTTP separada por vírgulas. Um exemplo é `200,202`. Se o código de status retornado não estiver na lista, as operações aquecimento e swap serão interrompidas. Por padrão, todos os códigos de resposta são válidos.

> [!NOTE]
> O elemento de configuração `<applicationInitialization>` faz parte de cada inicialização de aplicativo, enquanto as duas configurações de comportamento de aquecimento do aplicativo se aplicam somente a trocas de slot.

Se você tiver algum problema, consulte [Solucionar problemas com swaps](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorar uma troca

Se a [operação de swap](#AboutConfiguration) levar muito tempo para ser concluída, você poderá obter informações sobre a operação de swap nos [logs de atividades](../azure-monitor/platform/platform-logs-overview.md).

Na página de recursos do seu aplicativo no portal, no painel esquerdo, selecione **Logs de atividades**.

Uma operação de troca é exibida na consulta de log como `Swap Web App Slots`. Você pode expandi-la e selecionar uma das suboperações ou um dos erros para ver os detalhes.

## <a name="route-traffic"></a>Rotear o tráfego

Por padrão, todas as requisições de cliente para a URL de produção do aplicativo (`http://<nome_do_aplicativo>.azurewebsites.net`) são roteadas para o slot de produção. É possível rotear uma parte do tráfego para outro slot. Esse recurso é útil se você precisa de feedback do usuário referente a uma nova atualização, mas você não está pronto para liberá-la em produção.

### <a name="route-production-traffic-automatically"></a>Rotear tráfego de produção automaticamente

Para rotear o tráfego de produção automaticamente:

1. Vá para a página de recursos do aplicativo e selecione **Slots de implantação**.

2. Na coluna **% do Tráfego** do slot para o qual você deseja rotear, especifique um percentual (entre 0 e 100) para representar a quantidade de tráfego total que deseja rotear. Clique em **Salvar**.

    ![Definindo um percentual de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois que a configuração for salva, a porcentagem especificada de clientes é roteada de forma aleatória para o slot não produtivo.

Depois que um cliente é roteado automaticamente para um slot específico, ele é "fixado" a esse slot durante a vida útil da sessão do cliente. No navegador do cliente, você pode ver em qual slot a sessão dele está fixada observando o cookie `x-ms-routing-name` nos cabeçalhos HTTP. Uma requisição roteada para o slot de "staging" irá conter o cookie `x-ms-routing-name=staging`. Uma requisição roteada para o slot de produção irá conter o cookie `x-ms-routing-name=self`.

   > [!NOTE]
   > Além do Portal do Azure, você também pode usar o comando [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) na CLI do Azure para definir as porcentagens de roteamento a partir de ferramentas de CI/CD, como pipelines DevOps ou outros sistemas de automação.
   > 

### <a name="route-production-traffic-manually"></a>Rotear tráfego de produção manualmente

Além do roteamento de tráfego automático, o Serviço de Aplicativo pode rotear requisições para um slot específico. Isso é útil quando você deseja que os usuários possam aceitar ou recusar seu aplicativo beta. Para rotear o tráfego de produção manualmente, use o parâmetro de query string `x-ms-routing-name`.

Para permitir que os usuários recusem seu aplicativo beta, por exemplo, você pode colocar esse link em sua página web:

```html
<a href="<nomedoaplicativoweb>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A query string `x-ms-routing-name=self` especifica o slot de produção. Depois que o navegador do cliente acessa o link, ele é redirecionado para o slot de produção. Cada requisição subsequente terá o cookie `x-ms-routing-name=self` que fixa a sessão ao slot de produção.

Para permitir que os usuários optem por seu aplicativo beta, defina o mesmo parâmetro de query string para o nome do slot não produtivo. Veja um exemplo:

```
<nomedoaplicativoweb>.azurewebsites.net/?x-ms-routing-name=staging
```

Por padrão, novos slots recebem a regra de roteamento `0%`, exibido em cinza. Quando você define esse valor explicitamente como `0%` (exibido em texto preto), seus usuários poderão acessar o slot de staging manualmente usando a query string `x-ms-routing-name`. Mas eles não serão roteados para o slot automaticamente porque a porcentagem de roteamento é definida como 0. Esse é um cenário avançado em que você pode "ocultar" o slot de staging do público, permitindo que as equipes internas testem as alterações no slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Excluir um slot

Pesquise e selecione seu aplicativo. Selecione **Slots de implantação** > *\<slot para excluir>* > **Visão geral**. O tipo de aplicativo é exibido como **Serviço de Aplicativo (Slot)** para lembrá-lo de que você está visualizando um slot de implantação. Selecione **Excluir** na barra de comandos.

![Excluir um slot de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação no Serviço de Aplicativo do Azure.

Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com a sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](/powershell/azure/).  

---
### <a name="create-a-web-app"></a>Criar um aplicativo Web
```powershell
New-AzWebApp -ResourceGroupName [nome do grupo de recursos] -Name [nome do aplicativo] -Location [região] -AppServicePlan [nome do plano do serviço de aplicativo]
```

---
### <a name="create-a-slot"></a>Criar um slot
```powershell
New-AzWebAppSlot -ResourceGroupName [nome do grupo de recursos] -Name [nome do aplicativo] -Slot [deployment slot name] -AppServicePlan [nome do plano do serviço de aplicativo]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Iniciar uma troca com uma visualização (troca de várias fases) e aplicar a configuração do slot de destino ao slot de origem
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [nome do grupo de recursos] -ResourceType Microsoft.Web/sites/slots -ResourceName [nome do aplicativo]/[nome do slot] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancelar um swap pendente (troca com pré-visualização) e restaurar a configuração do slot de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [nome do grupo de recursos] -ResourceType Microsoft.Web/sites/slots -ResourceName [nome do aplicativo]/[nome do slot] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Alternar slots de implantação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [nome do grupo de recursos] -ResourceType Microsoft.Web/sites/slots -ResourceName [nome do aplicativo]/[nome do slot] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorar eventos de swap no log de atividades
```powershell
Get-AzLog -ResourceGroup [nome do grupo de recursos] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Excluir um slot
```powershell
Remove-AzResource -ResourceGroupName [nome do grupo de recursos] -ResourceType Microsoft.Web/sites/slots –Name [nome do aplicativo]/[nome do slot] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizar com templates do Resource Manager

Os [templates do Azure Resource Manager](../azure-resource-manager/templates/overview.md) são arquivos JSON declarativos usados para automatizar a implantação e a configuração de recursos no Azure. Para trocar slots usando templates do Resource Manager, você irá definir duas propriedades nos recursos *Microsoft.Web/sites/slots* e *Microsoft.Web/sites*:

- `buildVersion`: essa é uma propriedade do tipo string que representa a versão atual do aplicativo implantado no slot. Por exemplo: "v1", "1.0.0.1" ou "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: essa é uma propriedade do tipo string que especifica qual `buildVersion` o slot deve possuir. Se o targetBuildVersion não for igual ao atual `buildVersion`, então isso irá dispar a operação de swap localizando o slot que tem o `buildVersion` que foi especificado.

### <a name="example-resource-manager-template"></a>Exemplo de template do Resource Manager

O template do Resource Manager a seguir atualizará o `buildVersion` do slot de staging e definirá o `targetBuildVersion` no slot de produção. Isso trocará os dois slots. O template pressupõe que você já tenha uma webapp criada com um slot chamado "staging".

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

Esse template do Resource Manager é idempotente, o que significa que ele pode ser executado repetidamente e produzir o mesmo estado dos slots. Após a primeira execução, o `targetBuildVersion` corresponderá ao atual `buildVersion`, de modo que uma troca não será disparada.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizar com a CLI

Para mais informações sobre comandos da [CLI do Azure](https://github.com/Azure/azure-cli) para slots de implantação, confira [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Solucionar problemas com swaps

Se ocorrer algum erro durante uma [troca de slot](#AboutConfiguration), ele será registrado em *D:\home\LogFiles\eventlog.xml*. Ele também será registrado no log de erros específico do aplicativo.

Aqui estão alguns erros de swap comuns:

- Uma requisição HTTP para a raiz do aplicativo é cronometrada. A operação de swap aguarda por 90 segundos cada requisição HTTP e tenta novamente até 5 vezes. Se todas as tentativas atingirem o tempo limite, a operação de troca será interrompida.

- A inicialização do cache local poderá falhar quando o conteúdo do aplicativo exceder a cota de disco local especificada para o cache local. Para obter mais informações, consulte [Visão geral do cache local](overview-local-cache.md).

- Durante o [aquecimento personalizado](#Warm-up), as requisições HTTP são feitas internamente (sem passar pela URL externa). Elas podem falhar com determinadas regras de rewrite de URL no *Web.config*. Por exemplo, regras para redirecionar nomes de domínio ou impor HTTPS podem impedir que requisições de aquecimento atinjam o código do aplicativo. Para contornar esse problema, modifique suas regras de rewrite adicionando as duas condições a seguir:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de rewrite de URL ainda podem bloquear requisições HTTP. Para contornar esse problema, modifique suas regras de rewrite adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Após trocas de slot, o aplicativo pode passar por reinicializações inesperadas. Isso ocorre porque, após um swap, a configuração de associação de nome de host fica fora de sincronia, o que por si só não causa reinicializações. No entanto, determinados eventos subjacentes de armazenamento (como failovers de volume de armazenamento) podem detectar essas discrepâncias e forçar a reinicialização de todos os worker processes. Para minimizar esses tipos de reinicializações, defina a [configuração de aplicativo `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1`](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) em *todos os slots*. No entanto, essa configuração de aplicativo *não funciona* com aplicativos Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Próximas etapas
[Bloquear o acesso aos slots não produtivo](app-service-ip-restrictions.md)