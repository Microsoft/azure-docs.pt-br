---
title: Conectar computadores híbridos ao Azure em escala
description: Neste artigo, você aprende a conectar computadores ao Azure usando os servidores habilitados para Arc do Azure usando uma entidade de serviço.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175933"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Conectar computadores híbridos ao Azure em escala

Você pode habilitar os servidores habilitados para Arc do Azure para vários computadores Windows ou Linux em seu ambiente com várias opções flexíveis, dependendo de seus requisitos. Usando o script de modelo fornecido, você pode automatizar cada etapa da instalação, incluindo estabelecer a conexão com o Azure Arc. No entanto, é necessário executar esse script interativamente com uma conta que tenha permissões elevadas no computador de destino e no Azure.

Para conectar os computadores aos servidores habilitados para Arc do Azure, você pode usar uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory em vez de usar sua identidade privilegiada para [conectar-se interativamente ao computador](onboard-portal.md). Uma entidade de serviço é uma identidade de gerenciamento limitada especial que recebe apenas a permissão mínima necessária para conectar computadores ao Azure usando o comando `azcmagent`. Isso é mais seguro do que usar uma conta com privilégios maiores como um Administrador de Locatários e segue nossas melhores práticas de segurança de controle de acesso. A entidade de serviço só é usada durante a integração. Ela não é usada para outras finalidades.  

Os métodos de instalação e configuração do agente do Connected Machine requerem que o método automatizado usado tenha permissões de administrador nos computadores. No Linux, usando a conta raiz, e no Windows, como membro do grupo de Administradores Locais.

Antes de começar, examine os [pré-requisitos](agent-overview.md#prerequisites) e verifique se a sua assinatura e os recursos atendem aos requisitos. Para obter informações sobre regiões com suporte e outras considerações relacionadas, consulte [regiões do Azure com suporte](overview.md#supported-regions). Examine também nosso [Guia de planejamento em escala](plan-at-scale-deployment.md) para entender os critérios de design e implantação, bem como nossas recomendações de gerenciamento e monitoramento.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Criar uma Entidade de Serviço para integração em escala

Você pode usar o [Azure PowerShell](/powershell/azure/install-az-ps) para criar uma entidade de serviço com o cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Ou então, você pode seguir as etapas listadas em [Criar uma Entidade de Serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para concluir essa tarefa.

> [!NOTE]
> Antes de criar uma entidade de serviço, sua conta deve ser membro da função **proprietário** ou **administrador de acesso do usuário** na assinatura que você deseja usar para integração. Se você não tiver permissões suficientes para configurar atribuições de função, a entidade de serviço poderá ser criada, mas não poderá carregar computadores.
>

Para criar a entidade de serviço usando o PowerShell, execute as etapas a seguir.

1. Execute o comando a seguir. Você deve armazenar a saída do cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) em uma variável ou não poderá recuperar a senha necessária em uma etapa posterior.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Para recuperar a senha armazenada na variável `$sp`, execute o seguinte comando:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Na saída, encontre o valor da senha no campo **senha** e copie-o. Além disso, encontre o valor no campo **ApplicationId** e copie-o também. Salve-os para mais tarde em um local seguro. Se você esquecer ou perder a senha da entidade de serviço, poderá redefini-la usando o cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

Os valores das propriedades a seguir são usados com os parâmetros passados para o `azcmagent`:

* O valor da propriedade **ApplicationId** é usado para o valor do parâmetro `--service-principal-id`
* O valor da propriedade **senha** é usado para o parâmetro `--service-principal-secret` usado para conectar o agente.

> [!NOTE]
> Use a propriedade **ApplicationId** da entidade de serviço, não a propriedade **Id**.
>

A função **Integração do Azure Connected Machine** contém apenas as permissões necessárias para carregar um computador. Você pode atribuir a permissão da entidade de serviço para possibilitar que o escopo inclua um grupo de recursos ou uma assinatura. Para adicionar a atribuição de função, consulte [atribuir funções do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md) ou [atribuir funções do Azure usando CLI do Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Gerar o script de instalação no portal do Azure

O script usado para automatizar o download e a instalação e para estabelecer a conexão com o Azure Arc está disponível no portal do Azure. Para concluir o processo, execute as seguintes etapas:

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

1. Na página **Servidores – Azure Arc**, selecione **Adicionar** no canto superior esquerdo.

1. Na página **selecionar um método** , selecione o bloco **adicionar vários servidores** e, em seguida, selecione **gerar script**.

1. Na página **Gerar script**, selecione a assinatura e o grupo de recursos nos quais você deseja que o computador seja gerenciado no Azure. Selecione uma localização do Azure em que os metadados do computador serão armazenados. Essa localização pode ser a mesma ou uma diferente, assim como ocorre com a localização do grupo de recursos.

1. Na página **Pré-requisitos**, examine as informações e selecione **Avançar: Detalhes do recurso**.

1. Na página **Detalhes do recurso**, forneça o seguinte:

    1. Na lista suspensa **Grupo de recursos**, selecione o grupo de recursos no qual o computador será gerenciado.
    1. Na lista suspensa **Região**, selecione a região do Azure que armazenará os metadados dos servidores.
    1. Na lista suspensa **sistema operacional** , selecione o sistema operacional no qual o script está configurado para execução.
    1. Se o computador estiver se comunicando com a Internet por meio de um servidor proxy, especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usará para se comunicar com o servidor proxy. Digite o valor no formato `http://<proxyURL>:<proxyport>`.
    1. Selecione **Avançar: autenticação**.

1. Na página **autenticação** , na lista suspensa **entidade de serviço** , selecione **Arc-for-Servers**.  Em seguida, selecione, **próximo: marcas**.

1. Na página **Marcas**, examine as **Marcas de localização física** padrão sugeridas e insira um valor ou especifique uma ou mais **Marcas personalizadas** a fim de dar suporte aos seus padrões.

1. Selecione **Avançar: Baixar e executar o script**.

1. Na página **Baixar e executar o script**, examine as informações de resumo e selecione **Baixar**. Se você ainda precisar fazer alterações, selecione **Anterior**.

Para o Windows, você será solicitado a salvar `OnboardingScript.ps1` e para o Linux `OnboardingScript.sh` em seu computador.

## <a name="install-the-agent-and-connect-to-azure"></a>Instalar o agente e conectar-se ao Azure

Com o modelo de script criado anteriormente, você pode instalar e configurar o agente do computador conectado em vários computadores Linux e Windows híbridos usando a ferramenta de automação preferida de suas organizações. O script executa etapas semelhantes descritas no artigo [conectar máquinas híbridas ao Azure do portal do Azure](onboard-portal.md) . A diferença está na etapa final, em que você estabelece a conexão com o arco do Azure usando o `azcmagent` comando usando a entidade de serviço.

Estas são as configurações do comando `azcmagent` a ser usado para a entidade de serviço.

* `service-principal-id` : O identificador exclusivo (GUID) que representa a ID do aplicativo da entidade de serviço.
* `service-principal-secret` | A senha da entidade de serviço.
* `tenant-id`: O identificador exclusivo (GUID) que representa a instância dedicada do Azure AD.
* `subscription-id`: A ID da assinatura (GUID) do Azure onde você deseja que os computadores estejam.
* `resource-group`: O nome do grupo de recursos ao qual você deseja que os computadores conectados pertençam.
* `location`: Confira [Regiões do Azure com suporte](overview.md#supported-regions). Essa localização pode ser a mesma ou uma diferente, assim como ocorre com a localização do grupo de recursos.
* `resource-name`: (*Opcional*) Usado para a representação de recursos do Azure do seu computador local. Se você não especificar esse valor, o nome do host do computador será usado.

Você pode saber mais sobre a ferramenta de linha de comando `azcmagent` examinando a [Referência de Azcmagent](./manage-agent.md).

>[!NOTE]
>O script do Windows PowerShell só dá suporte à execução de uma versão de 64 bits do Windows PowerShell.
>

Depois de instalar o agente e configurá-lo para se conectar aos servidores habilitados para Azure Arc, acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma conexão de servidor bem-sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

- Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

- Saiba como gerenciar seu computador usando [Azure Policy](../../governance/policy/overview.md), para itens como [configuração de convidado](../../governance/policy/concepts/guest-configuration.md)de VM, verifique se o computador está relatando para o espaço de trabalho esperado log Analytics, habilite o monitoramento com o [Azure monitor com VMs](../../azure-monitor/vm/vminsights-enable-policy.md)e muito mais.

- Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento de carga de trabalho e sistema operacional com o Azure Monitor para VMs, gerenciá-los usando runbooks de automação ou recursos como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-introduction.md).
