---
title: Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como usar a publicação em estágios para aplicativos Web no Serviço de Aplicativo do Azure.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445609"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurar ambientes de preparo no Serviço de Aplicativo do Azure
<a name="Overview"></a>

Quando você implanta seu aplicativo web, aplicativo web no Linux, back-end móvel ou aplicativo de API [serviço de aplicativo do Azure](https://go.microsoft.com/fwlink/?LinkId=529714), você pode usar um slot de implantação separado em vez de no slot de produção padrão quando você estiver executando no **Standard**, **Premium**, ou **isolado** camada de plano de serviço de aplicativo. Slots de implantação são aplicativos em tempo real com seus próprios nomes de host. Os elementos de configurações e conteúdo de aplicativo podem ser trocados entre dois slots de implantação, incluindo o slot de produção. 

A implantação do aplicativo em um slot de não produção traz os seguintes benefícios:

* É possível validar as alterações no aplicativo em um slot de implantação de preparo antes de permutá-lo pelo slot de produção.
* Implantar um aplicativo em um slot primeiro e alterná-lo para produção garantem que todas as instâncias do slot estejam aquecidas antes de alterná-lo para produção. Isso elimina o tempo de inatividade quando você for implantar seu aplicativo. O redirecionamento de tráfego é contínuo, e nenhuma solicitação é removida devido a operações de alternância. Você pode automatizar todo esse fluxo de trabalho configurando [troca automática](#Auto-Swap) quando a validação de pré-permuta não é necessária.
* Após a troca, o slot com o aplicativo de preparo anterior terá o aplicativo de produção anterior. Se as alterações alternadas para o slot de produção não correspondem às suas expectativas, você pode realizar a mesma alternância imediatamente para ter o "último site válido conhecido" de volta.

Cada tipo de plano do Serviço de Aplicativo dá suporte a um número diferente de slots de implantação. Não há nenhum custo adicional para usar slots de implantação. Para descobrir o número de slots dá suporte à camada de seu aplicativo, consulte [limites de serviço de aplicativo](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Para dimensionar seu aplicativo para uma camada diferente, certifique-se de que a camada de destino suporta o número de slots de que seu aplicativo já usa. Por exemplo, se seu aplicativo tem mais de cinco slots, você não pode dimensioná-lo para baixo até a **Standard** tier, porque o **padrão** camada dá suporte a apenas cinco slots de implantação. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Adicionar um slot
O aplicativo precisa estar em execução na camada **Standard**, **Premium** ou **Isolado** para que seja possível habilitar vários slots de implantação.

1. No [Portal do Azure](https://portal.azure.com/), abra a [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources) do seu aplicativo.

2. No painel esquerdo, selecione **slots de implantação** > **adicionar Slot**.
   
    ![Adicionar um novo slot de implantação](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Se o aplicativo não estiver na **Standard**, **Premium**, ou **isolado** camada, você recebe uma mensagem que indica os tipos compatíveis para habilitar a publicação em etapas. Neste ponto, você tem a opção de selecionar **Upgrade** e vá para o **escala** guia do seu aplicativo antes de continuar.
   > 

3. No **adicionar um slot** caixa de diálogo, nomeie o slot e opte por clonar uma configuração do aplicativo de outro slot de implantação. Selecione **adicionar** para continuar.
   
    ![Fonte de configuração](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Você pode clonar uma configuração de qualquer slot existente. As configurações que podem ser clonadas incluem configurações de aplicativo, cadeias de conexão, versões da estrutura de linguagem, soquetes da Web, versão HTTP e número de bits da plataforma.

4. Depois que o slot é adicionado, selecione **feche** para fechar a caixa de diálogo. O novo slot agora é mostrado na **slots de implantação** página. Por padrão, **% do tráfego** é definido como 0 para o novo slot, com todo o tráfego de cliente roteado para o slot de produção.

5. Selecione o novo slot de implantação para abrir a página de recursos desse slot.
   
    ![Título do slot de implantação](./media/web-sites-staged-publishing/StagingTitle.png)

    O slot de preparo tem uma página de gerenciamento como qualquer outro aplicativo do Serviço de Aplicativo. É possível alterar a configuração do slot. O nome do slot é mostrado na parte superior da página para lembrá-lo de que você está exibindo o slot de implantação.

6. Selecione a URL do aplicativo na página de recursos do slot. O slot de implantação tem seu próprio nome de host e também é um aplicativo em tempo real. Para limitar o acesso público ao slot de implantação, consulte [restrições de IP do serviço de aplicativo do Azure](app-service-ip-restrictions.md).

O novo slot de implantação não tem nenhum conteúdo, mesmo se as configurações são clonadas de outro slot. Por exemplo, você pode [publicar neste slot com o Git](app-service-deploy-local-git.md). É possível fazer uma implantação no slot de outro branch do repositório ou de outro repositório. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>O que acontece durante uma troca

### <a name="swap-operation-steps"></a>Etapas da operação de permuta

Quando você troca dois slots (geralmente a partir de um slot de preparo no slot de produção), o serviço de aplicativo faz o seguinte para garantir que o slot de destino que não tenham o tempo de inatividade:

1. Aplique as seguintes configurações do slot de destino (por exemplo, o slot de produção) para todas as instâncias do slot de origem: 
    - [Específicas do slot](#which-settings-are-swapped) configurações do aplicativo e cadeias de caracteres de conexão, se aplicável.
    - [Implantação contínua](deploy-continuous-deployment.md) configurações, se habilitado.
    - [Autenticação do serviço de aplicativo](overview-authentication-authorization.md) configurações, se habilitado.
    
    Qualquer um desses casos disparar todas as instâncias no slot de origem para reiniciar. Durante [troca com visualização](#Multi-Phase), isso marca o final da primeira fase. A operação de troca está em pausa e você pode validar que o slot de origem funciona corretamente com configurações do slot de destino.

1. Aguarde até que todas as instâncias no slot de origem para concluir sua reinicialização. Se qualquer instância falhar ao reiniciar, a operação de permuta reverte todas as alterações para o slot de origem e para a operação.

1. Se [cache local](overview-local-cache.md) é habilitado, disparar a inicialização do cache local, fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância do slot de origem. Aguarde até que cada instância retorna qualquer resposta HTTP. Inicialização do cache local faz com que outra reinicialização em cada instância.

1. Se [troca automática](#Auto-Swap) é habilitada com [aquecimento personalizado](#Warm-up), gatilho [iniciação do aplicativo](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , fazendo uma solicitação HTTP para a raiz do aplicativo ("/") em cada instância da fonte slot.

    Se `applicationInitialization` não for especificada, disparar uma solicitação HTTP para a raiz do aplicativo do slot de origem em cada instância. 
    
    Se uma instância retorna qualquer resposta HTTP, ele será considerado para aquecido.

1. Se todas as instâncias no slot de origem sejam aquecidas com êxito, alternar os dois slots, alternando as regras de roteamento para os dois slots. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que está pronta no slot de origem anteriormente.

1. Agora que o slot de origem tem o aplicativo de pré-permuta anteriormente no slot de destino, execute a mesma operação aplicando todas as configurações e reinicializar as instâncias.

Em qualquer ponto da operação de troca, todo o trabalho de inicializar os aplicativos trocados ocorre no slot de origem. O slot de destino permanece online enquanto o slot de origem está sendo preparada e aquecido, independentemente de onde a permuta de êxito ou falha. Para trocar um slot de preparo pelo slot de produção, certifique-se de que o slot de produção é sempre o slot de destino. Dessa forma, a operação de permuta não afeta seu aplicativo de produção.

### <a name="which-settings-are-swapped"></a>Quais configurações são trocadas?
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração execute o conteúdo em uma permuta (não específicos do slot), enquanto outros elementos de configuração permanecem no mesmo slot após uma permuta (específicos do slot). A lista a seguir mostra as configurações que serão alterada com a troca de slots.

**Configurações que são permutadas**:

* Configurações gerais, como a versão do framework, 64/32-bit, de soquetes da web
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Configurações de monitoramento e diagnóstico
* Certificados públicos
* Conteúdo de Trabalhos Web
* Conexões híbridas *
* Integração da rede virtual *
* Pontos de extremidade de serviço *
* Rede de distribuição de conteúdo do Azure *

Recursos marcados com um asterisco (*) são planejados sejam feitas adesivo no slot. 

**Configurações que não são alternadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Associações SSL e certificados privados
* Configurações de escala
* Agendadores de Trabalhos Web
* Restrições de IP
* Always On
* Configurações do protocolo (HTTPS, versão do TLS, certificados de cliente)
* Configurações de log de diagnóstico
* Recursos de origens cruzadas compartilhando (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

Para configurar uma cadeia de conexão ou de configuração de aplicativo para permanecer em um slot específico (não trocado), vá para o **configuração** página para esse slot. Adicionar ou editar uma configuração e, em seguida, selecione **configuração do slot de implantação**. Marcar essa caixa de seleção diz ao serviço de aplicativo que a configuração não é swap. 

![Configuração do slot](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Alternar dois slots 
Você pode trocar slots de implantação em seu aplicativo **slots de implantação** página e o **visão geral** página. Para obter detalhes técnicos sobre a permuta do slot, consulte [o que acontece durante a permuta](#AboutConfiguration).

> [!IMPORTANT]
> Antes de trocar um aplicativo por meio de um slot de implantação em produção, certifique-se de que a produção é seu slot de destino e se todas as configurações no slot de origem estão configuradas exatamente como você deseja tê-los em produção.
> 
> 

Para trocar slots de implantação:

1. Vá para o aplicativo **slots de implantação** página e selecione **trocar**.
   
    ![Botão permutar](./media/web-sites-staged-publishing/SwapButtonBar.png)

    O **troque** caixa de diálogo mostra as configurações dos slots de origem e de destino selecionados que serão alterados.

2. Selecione os slots de **Origem** e de **Destino** desejados. Geralmente, o destino é o slot de produção. Além disso, selecione o **alterações de código-fonte** e **as alterações no destino** guias e verifique se que as alterações de configuração são esperadas. Quando tiver terminado, você pode alternar os slots imediatamente, selecionando **permuta**.

    ![Troca completa](./media/web-sites-staged-publishing/SwapImmediately.png)

    Para ver como seu slot de destino seria executado com as novas configurações antes da permuta realmente ocorre, não selecione **permuta**, mas siga as instruções no [troca com visualização](#Multi-Phase).

3. Quando tiver terminado, feche a caixa de diálogo, selecionando **feche**.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Troca com visualização (troca de várias fases)

> [!NOTE]
> Não há suporte para a alternância com visualização em aplicativos Web no Linux.

Antes de você troca para produção que o slot de destino, valide que o aplicativo é executado com as configurações trocadas. O slot de origem também seja aquecido antes da conclusão de permuta, que é desejável para aplicativos de missão crítica.

Quando você executa uma troca com visualização, o serviço de aplicativo executa a mesma [operação de troca](#AboutConfiguration) faz uma pausa, mas após a primeira etapa. Em seguida, você pode verificar o resultado no slot de preparo antes de concluir a troca. 

Se você cancelar a troca, o serviço de aplicativo reaplica os elementos de configuração ao slot de origem.

A troca com visualização:

1. Siga as etapas em [trocar slots de implantação](#Swap) , mas selecione **executar troca com visualização**.

    ![Alternância com visualização](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A caixa de diálogo mostra como a configuração no slot de origem é alterado na fase 1, e como o slot de origem e destino alterar na fase 2.

2. Quando estiver pronto para iniciar a troca, selecione **permuta iniciar**.

    Quando termina a fase 1, você será notificado na caixa de diálogo. Visualizar a permuta do slot de origem acessando `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Quando estiver pronto para concluir a troca pendente, selecione **completar troca** na **ação de troca** e selecione **completar troca**.

    Para cancelar uma troca pendente, selecione **Cancelar troca** em vez disso.

4. Quando tiver terminado, feche a caixa de diálogo, selecionando **feche**.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

Para automatizar uma alternância de várias fases, confira [Automatização com o PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Reverter uma troca
Se ocorrerem erros no slot de destino (por exemplo, o slot de produção) após uma alternância de slot, restaure os slots para seus estados de pré-alternância alternando os mesmos dois slots imediatamente.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurar a troca automática

> [!NOTE]
> Não há suporte para a troca automática em aplicativos web no Linux.

Troca automática simplifica cenários DevOps do Azure onde você deseja implantar seu aplicativo continuamente com zero inicializações a frio e zero tempo de inatividade para clientes do aplicativo. Quando a permuta automática está habilitada de um slot em produção, sempre que você enviar por push as alterações de código para esse slot, o serviço de aplicativo automaticamente [alterna o aplicativo em produção](#swap-operation-steps) depois que ela está pronta no slot de origem.

   > [!NOTE]
   > Antes de configurar a permuta automática para o slot de produção, considere a testar a troca automática em um slot de destino não seja de produção.
   > 

Para configurar a troca automática:

1. Vá para a página de recursos do seu aplicativo. Selecione **slots de implantação** >  *\<slot origem desejada >*  > **configuração**  >  **Configurações gerais**.
   
2. Para **troca automática habilitada**, selecione **em**. Selecione o slot de destino desejado para **slot de implantação de troca automática**e selecione **salvar** na barra de comandos. 
   
    ![Seleções para configurar a troca automática](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Execute um push de código para o slot de origem. Troca automática ocorre após um curto período, e a atualização será refletida na URL do slot de destino.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Especifique o aquecimento personalizado
Quando você estiver usando [troca automática](#Auto-Swap), alguns aplicativos podem exigir ações personalizadas de aquecimento antes da troca. O `applicationInitialization` elemento de configuração no Web. config permite que você especifique ações de inicialização personalizadas. O [operação de troca](#AboutConfiguration) aguardará esse aquecimento personalizado concluir antes de trocá-lo com o slot de destino. Aqui está um fragmento do Web. config de exemplo.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Para obter mais informações sobre como personalizar o `applicationInitialization` elemento, consulte [falhas de permuta do slot de implantação mais comuns e como corrigi-los](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Você também pode personalizar o comportamento de aquecimento com uma ou ambas das seguintes [configurações do aplicativo](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: O caminho para executar o ping para aquecer o seu site. Adicione essa configuração de aplicativo especificando um caminho personalizado que começa com uma barra (“/”) como o valor. Um exemplo é `/statuscheck`. O valor padrão é `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Códigos de resposta HTTP válidos para a operação de aquecimento. Adicione essa configuração de aplicativo com uma lista separada por vírgulas dos códigos HTTP. Um exemplo é `200,202` . Se o código de status retornado não estiver na lista, as operações de aquecimento e de troca são interrompidas. Por padrão, todos os códigos de resposta são válidos.

Se você tiver problemas, consulte [solucionar problemas de trocas](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorar uma troca

Se o [operação de troca](#AboutConfiguration) demora muito para ser concluída, você pode obter informações sobre a operação de permuta na [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na página de recursos do seu aplicativo no portal, no painel esquerdo, selecione **log de atividades**.

Uma operação de troca é exibida na consulta de log como `Swap Web App Slots`. Você pode expandi-lo e selecionar uma das suboperações ou um dos erros para ver os detalhes.

## <a name="route-traffic"></a>Rotear o tráfego

Por padrão, todas as solicitações de cliente para a URL de produção do aplicativo (`http://<app_name>.azurewebsites.net`) são roteadas para o slot de produção. É possível rotear uma parte do tráfego para outro slot. Esse recurso é útil se você precisa de comentários do usuário para uma nova atualização, mas não está pronto para liberá-la para produção.

### <a name="route-production-traffic-automatically"></a>Rotear o tráfego de produção automaticamente

Para rotear o tráfego de produção automaticamente:

1. Vá para a página de recursos do seu aplicativo e selecione **slots de implantação**.

2. Na coluna **% do Tráfego** do slot para o qual você deseja rotear, especifique um percentual (entre 0 e 100) para representar a quantidade de tráfego total que deseja rotear. Clique em **Salvar**.

    ![Definindo uma porcentagem de tráfego](./media/web-sites-staged-publishing/RouteTraffic.png)

Depois que a configuração é salva, o percentual especificado de clientes aleatoriamente é roteado para o slot de não produção. 

Depois que um cliente será automaticamente roteado para um slot específico, é "fixados" para esse slot durante a vida útil da sessão de cliente em questão. No navegador do cliente, você pode ver em qual slot a sessão está fixada observando o cookie `x-ms-routing-name` nos cabeçalhos HTTP. Uma solicitação roteada para o slot "de preparo" tem o cookie `x-ms-routing-name=staging`. Uma solicitação roteada para o slot de produção tem o cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Rotear o tráfego de produção manualmente

Além do roteamento de tráfego automático, o Serviço de Aplicativo pode rotear solicitações para um slot específico. Isso é útil quando você deseja que os usuários sejam capazes de aceitar ou recusar seu aplicativo beta. Para rotear o tráfego de produção manualmente, use o parâmetro de consulta `x-ms-routing-name`.

Por exemplo, para permitir que os usuários a recusar seu aplicativo beta, você pode colocar esse link em sua página da Web:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A cadeia de caracteres `x-ms-routing-name=self` especifica o local de produção. Depois que o navegador do cliente acessa o link, ele é redirecionado para o slot de produção. Cada solicitação subsequente tem o `x-ms-routing-name=self` cookie que fixa a sessão para o slot de produção.

Para permitir que os usuários optar pelo seu aplicativo beta, defina o mesmo parâmetro de consulta com o nome do slot de não produção. Aqui está um exemplo:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Por padrão, novos slots recebem uma regra de roteamento de `0%`, conforme mostrado em cinza. Quando você define explicitamente esse valor como `0%` (mostrado em texto preto), os usuários podem acessar o slot de preparo manualmente usando o `x-ms-routing-name` parâmetro de consulta. Mas eles não serão roteados para o slot automaticamente como o percentual de roteamento é definido como 0. Isso é um cenário avançado, onde você pode "oculta" o slot de preparo do público, permitindo que equipes internas testar as alterações no slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Excluir um slot

Vá para a página de recursos do seu aplicativo. Selecione **slots de implantação** >  *\<slot para excluir >*  > **visão geral**. Selecione **excluir** na barra de comandos.  

![Excluir um slot de implantação](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizar com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell, incluindo suporte ao gerenciamento de slots de implantação no Serviço de Aplicativo do Azure.

Para obter mais informações sobre como instalar e configurar o PowerShell do Azure, e como autenticar o PowerShell do Azure com sua assinatura do Azure, consulte [Como instalar e configurar o PowerShell do Microsoft Azure](/powershell/azure/overview).  

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
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancelar uma troca pendente (troca com revisão) e restaurar a configuração do slot de origem
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Permute slots de implantação
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Troca de monitorar eventos no log de atividades
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Excluir um slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizar com a CLI

Para obter os comandos da [CLI do Azure](https://github.com/Azure/azure-cli) para slots de implantação, confira [Slot de implantação do az webapp](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>As trocas de solução de problemas

Se ocorrer algum erro durante uma [permuta do slot](#AboutConfiguration), ele é registrado *D:\home\LogFiles\eventlog.xml*. Ele também é registrado no log de erros específicos do aplicativo.

Aqui estão alguns erros comuns de permuta:

- Uma solicitação HTTP para a raiz do aplicativo é atingiu o tempo limite. A operação de permuta aguarda 90 segundos para cada solicitação HTTP e repetições até 5 vezes. Se todas as novas tentativas são atingiu o tempo limite, a operação de troca é interrompida.

- Inicialização do cache local pode falhar quando o conteúdo do aplicativo excede a cota de disco local especificada para o cache local. Para obter mais informações, consulte [visão geral do cache Local](overview-local-cache.md).

- Durante [aquecimento personalizado](#Warm-up), solicitações de HTTP são feitas internamente (sem passar por meio da URL externa). Eles podem falhar com determinadas regras de regravação de URL no *Web. config*. Por exemplo, as regras para redirecionar os nomes de domínio ou impor HTTPS podem impedir que solicitações de aquecimento alcançar o código do aplicativo. Para contornar esse problema, modifique as regras de reconfiguração, adicionando duas condições a seguir:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sem um aquecimento personalizado, as regras de reescrita de URL ainda podem bloquear solicitações HTTP. Para contornar esse problema, modifique as regras de reconfiguração, adicionando a seguinte condição:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Alguns [regras de restrição de IP](app-service-ip-restrictions.md) pode impedir que a operação de troca de enviar solicitações HTTP para seu aplicativo. Intervalos de endereço IPv4 que começam com `10.` e `100.` são internos para sua implantação. Você deve permitir que eles se conectem ao seu aplicativo.

## <a name="next-steps"></a>Próximas etapas
[Bloquear o acesso aos slots de não produção](app-service-ip-restrictions.md)
