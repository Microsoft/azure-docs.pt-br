---
title: 'ExpressRoute: como configurar alertas personalizados para rotas anunciadas'
description: Este artigo mostra como usar a automação do Azure e os aplicativos lógicos para monitorar o número de rotas anunciadas do gateway de ExpressRoute para redes locais a fim de evitar atingir o limite de rotas de 200.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: a960150d68cf2f939e206321a20d98b0e4080313
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371487"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Configurar alertas personalizados para monitorar rotas anunciadas

Este artigo ajuda você a usar a automação do Azure e os aplicativos lógicos para monitorar constantemente o número de rotas anunciadas do gateway de ExpressRoute para redes locais. O monitoramento pode ajudar a evitar atingir o [limite de rotas de 200](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering).

A **automação do Azure** permite automatizar a execução do script personalizado do PowerShell armazenado em um *runbook*. Ao usar a configuração neste artigo, o runbook contém um script do PowerShell que consulta um ou mais gateways do ExpressRoute. Ele coleta um conjunto de um DataSet que contém o grupo de recursos, o nome do gateway do ExpressRoute e o número de prefixos de rede anunciados no local.

Os **aplicativos lógicos do Azure** agendam um fluxo de trabalho personalizado que chama o runbook de automação do Azure. A execução do runbook é feita usando um trabalho. Após a execução da coleta de dados, o fluxo de trabalho dos aplicativos lógicos do Azure classifica os dados e, com base nos critérios de correspondência no número de prefixos de rede acima ou abaixo de um limite predefinido, envia informações para um endereço de email de destino.

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

A configuração de um alerta personalizado baseia-se em três etapas principais:

1. Crie uma conta de automação com uma conta "executar como" e permissões.

2. Criar e configurar runbooks.

3. Crie um aplicativo lógico que irá disparar a conta de automação e enviar um email de alertas se o número for maior que um limite (por exemplo, 160).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Você tem pelo menos um gateway de ExpressRoute em sua implantação.

* Você tem uma compreensão básica das [contas Executar como](../automation/manage-runas-account.md) na automação do Azure.

* Você está familiarizado com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

* Você está familiarizado com o uso de Azure PowerShell. Azure PowerShell é necessário para coletar os prefixos de rede no gateway do ExpressRoute. Para obter mais informações sobre Azure PowerShell em geral, consulte a [documentação do Azure PowerShell](/powershell/azure/?view=azps-4.1.0).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Notas e limitações

* O alerta personalizado discutido neste artigo é um complemento para alcançar uma operação e um controle melhores. Não é uma substituição para os alertas nativos no ExpressRoute.
* A coleta de dados para gateways de ExpressRoute é executada em segundo plano. O tempo de execução pode ser mais longo do que o esperado. Para evitar o enfileiramento de trabalhos, a recorrência do fluxo de trabalho deve ser configurada corretamente.
* Implantações por scripts ou modelos de ARM podem ocorrer mais rápido do que o gatilho de alarme personalizado. Isso pode resultar em um aumento no número de prefixos de rede no gateway de ExpressRoute acima do limite de 200 rotas.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Criar e configurar contas

Quando você cria uma conta de automação no portal do Azure, uma conta [Executar como](../automation/automation-security-overview.md#run-as-accounts) é criada automaticamente. Essa conta executa as seguintes ações:

* Cria um aplicativo Azure Active Directory (Azure AD) com um certificado autoassinado. A conta Executar como tem um certificado que precisa ser renovado por padrão todos os anos.

* Cria uma conta de entidade de serviço para o aplicativo no Azure AD.

* Atribui a si mesmo a função de colaborador (RBAC do Azure) na assinatura do Azure em uso. Essa função gerencia Azure Resource Manager recursos usando runbooks.

Para criar uma conta de automação, você precisa de privilégios e permissões. Para obter informações, consulte [permissões necessárias para criar uma conta de automação](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. criar uma conta de automação

Crie uma conta de automação com permissões executar como. Para obter instruções, consulte [criar uma conta de automação do Azure](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. atribuir a conta Executar como uma função

Por padrão, a função **colaborador** é atribuída à entidade de serviço que é usada pela sua conta **Executar como** . Você pode manter a função padrão atribuída à entidade de serviço ou pode restringir as permissões atribuindo uma [função interna](../role-based-access-control/built-in-roles.md) (por exemplo, leitor) ou uma [função personalizada](../active-directory/roles/custom-create.md).

 Use as etapas a seguir para determinar a função atribuir à entidade de serviço que é usada pela sua conta Executar como:

1. Abra sua conta de Automação. Navegue até **configurações de conta**e selecione **contas Executar como**.

2. Selecione **funções** para exibir a definição de função que está sendo usada.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Adicionar conta de automação":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Criar e configurar runbooks

### <a name="1-install-modules"></a><a name="install-modules"></a>1. instalar módulos

Para executar os cmdlets do PowerShell em runbooks de automação do Azure, você precisa instalar alguns módulos adicionais do Azure PowerShell AZ. Use as seguintes etapas para instalar os módulos:

1. Abra sua conta de automação do Azure e navegue até **módulos**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Adicionar conta de automação":::

2. Pesquise a galeria e importe os seguintes módulos: **AZ. Accounts**, **AZ. Network**, **AZ. Automation**e **AZ. Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. criar um runbook

1. Para criar seu runbook do PowerShell, navegue até sua conta de automação. Em **automação de processo**, selecione o bloco **Runbooks** e, em seguida, selecione **criar um runbook**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Adicionar conta de automação":::

2. Selecione **criar** para criar o runbook.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Adicionar conta de automação":::

3. Selecione o runbook recém-criado e, em seguida, selecione **Editar**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Adicionar conta de automação":::

4. Em **Editar**, Cole o script do PowerShell. O [script de exemplo](#script) pode ser modificado e usado para monitorar gateways de ExpressRoute em um ou mais grupos de recursos.

   No script de exemplo, observe as seguintes configurações:

    * A matriz **$rgList** contém a lista de grupos de recursos com gateways de ExpressRoute. Você pode personalizar os gateways de ExpressRoute baseados em lista.
    * A variável **$thresholdNumRoutes** definir o limite no número de prefixos de rede anunciados de um gateway de ExpressRoute para as redes locais.

#### <a name="example-script"></a><a name="script"></a>Script de exemplo

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. salvar e publicar o runbook

1. Selecione **salvar** para salvar uma cópia de rascunho do runbook.
2. Selecione **publicar** para publicar o runbook como a versão oficial do runbook na conta de automação.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Adicionar conta de automação":::

Quando você executa o script do PowerShell, uma lista de valores é coletada:
 
* Resource group

* Nome do gateway de ExpressRoute

* Endereço IP do primeiro par de BGP (ponto1)

* Endereço IP do segundo par de BGP (peer2)

* Número de prefixos de rede anunciados do gateway de ExpressRoute para o primeiro par de BGP (ponto1)

* Número de prefixos de rede anunciados do gateway de ExpressRoute para o segundo par de BGP (peer2)

* Timestamp

* Status, classificado como:

  * ' OK ' se o número de rotas for menor que um valor de limite
  * ' Alerta ' se o número de rotas for acima de um valor de limite
  * ' Aviso ' se o número de prefixos de rede anunciados para o par de dois BGP for diferente

* Mensagem de alerta, para obter uma descrição detalhada do status (OK, alerta, aviso)

O script do PowerShell converte as informações coletadas em uma saída JSON. O runbook usa o cmdlet [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  do PowerShell como fluxo de saída para comunicar informações ao cliente.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. validar o runbook

Depois que o runbook é criado, ele deve ser validado. Selecione **Iniciar** e verifique a saída e os erros para os fluxos de trabalho diferentes.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Criar e configurar um aplicativo lógico

O aplicativo lógico do Azure é o orquestrador de todo o processo de coleta e ações. Nas seções a seguir, você cria um fluxo de trabalho usando um aplicativo lógico.

### <a name="workflow"></a>Fluxo de trabalho

Neste fluxo de trabalho, você cria um aplicativo lógico que monitora regularmente os gateways do ExpressRoute. Se existirem novos itens, o aplicativo lógico envia um email para cada item. Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Adicionar conta de automação":::

### <a name="1-create-a-logic-app"></a>1. criar um aplicativo lógico

No **Designer de aplicativo lógico**, crie um aplicativo lógico usando o modelo de **aplicativo lógico em branco** . Para obter as etapas, consulte [criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app).

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Adicionar conta de automação":::

### <a name="2-add-a-trigger"></a>2. adicionar um gatilho

Cada aplicativo lógico é iniciado por um gatilho. Um gatilho é acionado quando ocorre um evento específico ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos do Azure cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho.

Para executar regularmente um aplicativo lógico baseado em um agendamento de tempo predefinido, adicione a **recorrência interna: agendamento** ao fluxo de trabalho. Na caixa de pesquisa, digite **Schedule**. Selecione **Gatilhos**. Na lista de gatilhos, selecione **agenda de recorrência**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Adicionar conta de automação":::

No gatilho agendamento de recorrência, você pode definir o fuso horário e uma recorrência para repetir o fluxo de trabalho. Juntos, o intervalo e a frequência definem o agendamento para o gatilho do aplicativo lógico. Para estabelecer uma frequência de recorrência mínima razoável, considere os seguintes fatores:

* O script do PowerShell no runbook de automação leva tempo para ser concluído. O tempo de execução depende do número de gateways de ExpressRoute a serem monitorados. Uma frequência de recorrência muito curta causará o enfileiramento de trabalhos.

* O script do PowerShell é executado como um trabalho em segundo plano. Ele não é iniciado imediatamente; Ele é executado após um atraso variável.

* Uma frequência de recorrência muito curta gerará carga desnecessária em seus gateways de ExpressRoute do Azure.

No final da configuração do fluxo de trabalho, você pode verificar a consistência da frequência de recorrência executando o fluxo de trabalho algumas vezes e, em seguida, verificando o resultado no **histórico de execuções**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. criar um trabalho

Um aplicativo lógico acessa outros aplicativos, serviços e a plataforma, por meio de conectores. A próxima etapa neste fluxo de trabalho é selecionar um conector para acessar a conta de automação do Azure que foi definida anteriormente.

1. No **Designer de aplicativos lógicos**, abaixo da **recorrência**, selecione **nova etapa**. Em **escolher uma ação** e a caixa de pesquisa, selecione **tudo**.
2. Na caixa de pesquisa, digite **automação e pesquisa do Azure** . Selecione **criar trabalho**. **Criar trabalho** será usado para acionar o runbook de automação que foi criado anteriormente.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Adicionar conta de automação":::

3. Entre usando uma entidade de serviço. Você pode usar uma entidade de serviço existente ou pode criar uma nova. Para criar uma nova entidade de serviço, consulte [como usar o portal para criar uma entidade de serviço do Azure AD que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md). Selecione **conectar com a entidade de serviço**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Adicionar conta de automação":::

4. Digite um **nome de conexão**, adicione **sua ID do cliente** (ID do aplicativo), segredo do **cliente**e sua ID de **locatário**. Em seguida, selecione **Criar**.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Adicionar conta de automação" na **conta de automação**. Além disso, verifique se você adicionou o **nome do runbook** como um novo parâmetro.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. obter a saída do trabalho

1. Selecione **Nova etapa**. Pesquise "automação do Azure". Na lista **ações** , selecione **obter saída do trabalho**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Adicionar conta de automação":::

2. Na página **obter saída do trabalho** , especifique as informações necessárias para acessar a conta de automação. Selecione a **assinatura, o grupo de recursos**e a **conta de automação** que você deseja usar. Clique dentro da caixa **ID do trabalho** . Quando a lista de **conteúdo dinâmico** for exibida, selecione **ID do trabalho**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. analisar o JSON

As informações contidas na saída da ' ação de criação do trabalho de automação do Azure ' (etapas anteriores) geram um objeto JSON. Os aplicativos lógicos **Parse JSON** é uma ação interna para criar tokens amigáveis para o usuário a partir das propriedades e seus valores no conteúdo JSON. Você pode usar essas propriedades em seu fluxo de trabalho.

1. Adicione uma ação. Na **ação obter saída do trabalho->**, selecione **nova etapa**.
2. Na caixa de pesquisa **escolher uma ação** , digite "analisar JSON" para procurar os conectores que oferecem essa ação. Na lista **ações** , selecione a ação **analisar JSON** para as operações de dados que você deseja usar.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Adicionar conta de automação":::

3. Clique dentro da caixa de **conteúdo** . Quando a lista de conteúdo dinâmico for exibida, selecione **conteúdo**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. A análise de um JSON requer um esquema. O esquema pode ser gerado usando a saída do runbook de automação. Abra uma nova sessão do navegador da Web, execute o runbook de automação e pegue a saída. Retorne à ação **aplicativos lógicos de análise de dados JSON** . Na parte inferior da página, selecione **usar conteúdo de exemplo para gerar o esquema**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Adicionar conta de automação":::

5. Para **Inserir ou colar um conteúdo JSON de exemplo**, Cole a saída do runbook de automação e selecione **concluído**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Um esquema é gerado automaticamente pela análise da carga de entrada JSON.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. definir e inicializar uma variável

Nesta etapa do fluxo de trabalho, criamos uma condição para enviar um alarme por email. Para uma formatação flexível e personalizada de uma mensagem de corpo de email, uma variável auxiliar é introduzida no fluxo de trabalho.

1. Na **ação obter saída do trabalho**, selecione **nova etapa**. Na caixa de pesquisa, localize e selecione **variáveis**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Adicionar conta de automação":::

2. Na lista **ações** , selecione a ação **inicializar variável** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Adicionar conta de automação":::

3. Especifique o nome da variável. Para **tipo**, selecione **cadeia de caracteres**. O **valor** da variável será atribuído posteriormente no fluxo de trabalho.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. criar uma ação "para cada"

Depois que o JSON é analisado, a ação **analisar operações de dados JSON** armazena o conteúdo na saída do *corpo* . Para processar a saída, você pode criar um loop "for each" repetindo uma ou mais ações em cada item na matriz.

1. Em **inicializar variável**, selecione **Adicionar uma ação**. Na caixa de pesquisa, digite "for each" como filtro.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Adicionar conta de automação":::

2. Na lista **ações** , selecione a ação **para cada controle**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Adicionar conta de automação":::

3. Clique na caixa de texto **selecionar uma saída de etapas anteriores** . Quando a lista de **conteúdo dinâmico** for exibida, selecione o **corpo**, que é a saída do JSON analisado.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Adicionar conta de automação":::

4. Para cada elemento do corpo JSON, queremos definir uma condição. No grupo de ações, selecione **controle**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Adicionar conta de automação":::

5. Na lista **ações** , selecione **condição-controle**. A Condition-Control é uma estrutura de controle que compara os dados em seu fluxo de trabalho com valores ou campos específicos. Em seguida, você pode especificar ações diferentes que são executadas com base em ou não, os dados atendem à condição.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Adicionar conta de automação":::

6. Na raiz da ação da **condição** , altere a operação lógica para **ou**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Verifique o valor do número de prefixos de rede que um gateway de ExpressRoute anuncia para os dois pares de BGP. O número de rotas está disponível em "numRoutePeer1" e "numRoutePeer2" em **conteúdo dinâmico**. Na caixa valor, digite o valor para **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="Adicionar conta de automação":::

8. Para adicionar outra linha à sua condição, escolha **Adicionar-> Adicionar linha**. Na segunda caixa, em **conteúdo dinâmico**, selecione **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="Adicionar conta de automação":::

9. A condição lógica é verdadeira quando uma das duas variáveis dinâmicas, numRoute1 ou numRoute2, é maior que o limite. Neste exemplo, o limite é fixo para 160 (80% do valor máximo de rotas 200). Você pode alterar o valor do limite para atender às suas necessidades. Para consistência, o valor deve ser o mesmo valor usado no script do PowerShell do runbook.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Adicionar conta de automação":::

10. Em **se verdadeiro**, formate e crie as ações para enviar o alerta por email. Em * * escolha uma ação, pesquise e selecione **variáveis**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Adicionar conta de automação":::

11. Em variáveis, selecione **Adicionar uma ação**. Na lista **ações** , selecione **definir variável**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Adicionar conta de automação":::

12. Em **nome**, selecione a variável chamada **EmailBody** que você criou anteriormente. Para **valor**, Cole o script HTML necessário para formatar o email de alerta. Use o **conteúdo dinâmico** para incluir os valores do corpo JSON. Depois de definir essas configurações, o resultado é que a variável **EmailBody** contém todas as informações relacionadas ao alerta, no formato HTML.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Adicionar conta de automação":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. Adicionar o conector de email

Os aplicativos lógicos fornecem muitos conectores de email. Neste exemplo, adicionamos um conector do Outlook para enviar o alerta por email. Em **definir variável**, selecione **Adicionar uma ação**. Em **escolher uma ação**, digite "enviar email" na caixa de pesquisa.

1. Selecione **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Adicionar conta de automação":::

2. Na lista **ações** , selecione **enviar um email (v2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Adicionar conta de automação":::

3. Entre para criar uma conexão com o Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Adicionar conta de automação":::

4. No campo **corpo** , clique em **adicionar conteúdo dinâmico**. No painel de conteúdo dinâmico, adicione a variável **EmailBody**. Preencha os campos **assunto** e **para** .

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Adicionar conta de automação":::

5. A ação **enviar um email (v2)** concluiu a configuração do fluxo de trabalho.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Adicionar conta de automação" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. validação do fluxo de trabalho

A etapa final é a validação do fluxo de trabalho. Em **visão geral dos aplicativos lógicos**, selecione **executar gatilho**. Selecione **recorrência**. O fluxo de trabalho pode ser monitorado e verificado no **histórico de execuções**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Adicionar conta de automação":::

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como personalizar o fluxo de trabalho, consulte [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).