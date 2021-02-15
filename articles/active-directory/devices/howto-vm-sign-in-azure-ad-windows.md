---
title: Entrar na máquina virtual do Windows no Azure usando Azure Active Directory (versão prévia)
description: Entrar no Azure AD em uma VM do Azure executando o Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e22e5fc4bc7211d905dbe8775b0ef6e893bd2cc
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761006"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Entrar na máquina virtual do Windows no Azure usando a autenticação Azure Active Directory (versão prévia)

As organizações agora podem utilizar a autenticação Azure Active Directory (AD) para suas VMs (máquinas virtuais) do Azure que executam o **Windows Server 2019 Datacenter Edition** ou o **Windows 10 1809** e posterior. Usar o Azure AD para autenticar em VMs fornece uma maneira de controlar e impor políticas de forma centralizada. Ferramentas como o Azure RBAC (controle de acesso baseado em função) e o acesso condicional do Azure AD permitem que você controle quem pode acessar uma VM. Este artigo mostra como criar e configurar uma VM do Windows Server 2019 para usar a autenticação do Azure AD.

> [!NOTE]
> A entrada do Azure AD para VMs do Windows do Azure é um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte  [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Há muitos benefícios em usar a autenticação do Azure AD para fazer logon em VMs do Windows no Azure, incluindo:

- Utilize as mesmas credenciais do Azure AD federadas ou gerenciadas que você normalmente usa.
- Não precisa mais gerenciar contas de administrador local.
- O RBAC do Azure permite conceder o acesso apropriado às VMs com base na necessidade e removê-la quando não for mais necessária.
- Antes de permitir o acesso a uma VM, o acesso condicional do Azure AD pode impor requisitos adicionais, como: 
   - Autenticação multifator
   - Verificação de risco de entrada
- Automatize e dimensione o ingresso no Azure AD de VMs do Windows do Azure que fazem parte para suas implantações de VDI.

> [!NOTE]
> Depois de habilitar esse recurso, suas VMs do Windows no Azure serão ingressadas no Azure AD. Você não pode associá-lo a outro domínio como o AD local ou o Azure AD DS. Se você precisar fazer isso, será necessário desconectar a VM do seu locatário do Azure AD desinstalando a extensão.

## <a name="requirements"></a>Requisitos

### <a name="supported-azure-regions-and-windows-distributions"></a>Regiões do Azure e distribuições do Windows com suporte

No momento, há suporte para as seguintes distribuições do Windows durante a versão prévia deste recurso:

- Windows Server 2019 Datacenter
- Windows 10 1809 e posterior

> [!IMPORTANT]
> A conexão remota com VMs Unidas ao Azure AD só é permitida em computadores Windows 10 que estejam registrados no Azure AD (iniciando o Windows 10 20H1), ingressado no Azure ad ou Azure AD híbrido ingressado no **mesmo** diretório que a VM. 

No momento, há suporte para as seguintes regiões do Azure durante a versão prévia desse recurso:

- Todas as regiões globais do Azure

> [!IMPORTANT]
> Para usar esse recurso de visualização, implante apenas uma distribuição do Windows com suporte e em uma região do Azure com suporte. No momento, não há suporte para o recurso em nuvens do Azure governamental ou soberanas.

### <a name="network-requirements"></a>Requisitos de rede

Para habilitar a autenticação do Azure AD para suas VMs do Windows no Azure, você precisa garantir que sua configuração de rede de VMs permita o acesso de saída aos seguintes pontos de extremidade pela porta TCP 443:

- https: \/ /enterpriseregistration.Windows.net
- https:\//login.microsoftonline.com
- https: \/ /Device.login.microsoftonline.com
- https: \/ /Pas.Windows.net

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Habilitando o logon do Azure AD no para VM do Windows no Azure

Para usar o logon do Azure AD no para VM do Windows no Azure, você precisa primeiro habilitar a opção de logon do Azure AD para sua VM do Windows e, em seguida, você precisa configurar as atribuições de função do Azure para usuários que estão autorizados a fazer logon na VM.
Há várias maneiras pelas quais você pode habilitar o logon do Azure AD para sua VM do Windows:

- Usando a experiência de portal do Azure ao criar uma VM do Windows
- Usando a experiência de Azure Cloud Shell ao criar uma VM **do Windows ou para uma VM do Windows existente**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Usando portal do Azure criar experiência de VM para habilitar o logon do Azure AD

Você pode habilitar o logon do Azure AD para o Windows Server 2019 Datacenter ou o Windows 10 1809 e imagens de VM posteriores. 

Para criar uma VM do Windows Server 2019 datacenter no Azure com o logon do Azure AD: 

1. Entre no [portal do Azure](https://portal.azure.com), com uma conta que tenha acesso para criar VMs e selecione **+ criar um recurso**.
1. Digite **Windows Server** em Pesquisar na barra de pesquisa do Marketplace.
   1. Clique em **Windows Server** e escolha **Windows Server 2019 datacenter** na lista suspensa Selecionar um plano de software.
   1. Clique em **criar**.
1. Na guia "gerenciamento", habilite a opção para **fazer logon com as credenciais do AAD (versão prévia)** na seção Azure Active Directory de desativado para **ativado**.
1. Verifique se a **identidade gerenciada atribuída pelo sistema** na seção identidade está definida como **ativada**. Essa ação deve ocorrer automaticamente depois que você habilitar o logon com as credenciais do Azure AD.
1. Percorra o restante da experiência de criação de uma máquina virtual. Durante essa visualização, você precisará criar um nome de usuário e senha de administrador para a VM.

![Logon com as credenciais do Azure AD criar uma VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Para fazer logon na VM usando sua credencial do Azure AD, primeiro será necessário configurar atribuições de função para a VM, conforme descrito em uma das seções a seguir.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Usando a experiência de Azure Cloud Shell para habilitar o logon do Azure AD

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ferramentas comuns do Azure estão pré-instaladas e configuradas para uso com sua conta. Basta selecionar o botão Copiar para copiar o código, colá-lo no Cloud Shell e pressionar Enter para executá-lo. Há algumas maneiras de abrir o Cloud Shell:

Selecione Experimente no canto superior direito de um bloco de código.
Abra o Cloud Shell em seu navegador.
Selecione o botão Cloud Shell no menu no canto superior direito do [Portal do Azure](https://portal.azure.com).

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.31 ou posterior. Execute az --version para localizar a versão. Se você precisar instalar ou atualizar, consulte o artigo [instalar CLI do Azure](/cli/azure/install-azure-cli).

1. Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). 
1. Crie uma VM com [AZ VM Create](/cli/azure/vm#az-vm-create) usando uma distribuição com suporte em uma região com suporte. 
1. Instale a extensão de VM de logon do Azure AD. 

O exemplo a seguir implanta uma VM chamada myVM que usa Win2019Datacenter, em um grupo de recursos chamado MyResource Group, na região southcentralus. Nos exemplos a seguir, você pode fornecer seu próprio grupo de recursos e seus próprios nomes de VM, conforme o necessário.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> É necessário que você habilite a identidade gerenciada atribuída pelo sistema em sua máquina virtual antes de instalar a extensão de VM de logon do Azure AD.

A criação da VM e dos recursos de suporte demora alguns minutos.

Por fim, instale a extensão de VM de logon do Azure AD para habilitar o logon do Azure AD para a VM do Windows. As extensões de VM são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure. Use [AZ VM Extension](/cli/azure/vm/extension#az-vm-extension-set) set para instalar a extensão AADLOGINFORWINDOWS na VM denominada myVM no grupo de recursos MyResource Group:

> [!NOTE]
> Você pode instalar a extensão AADLoginForWindows em uma VM existente do Windows Server 2019 ou Windows 10 1809 e posterior para habilitá-la para a autenticação do Azure AD. Um exemplo de AZ CLI é mostrado abaixo.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O `provisioningState` de `Succeeded` é mostrado, depois que a extensão é instalada na VM.

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de função para a VM

Agora que você criou a VM, precisará configurar a política RBAC do Azure para determinar quem pode fazer logon na VM. Duas funções do Azure são usadas para autorizar o logon da VM:

- **Logon de administrador da máquina virtual**: os usuários com essa função atribuída podem fazer logon em uma máquina virtual do Azure com privilégios de administrador.
- **Logon de usuário da máquina virtual**: os usuários com essa função atribuído podem fazer logon uma máquina virtual do Azure com privilégios de usuários regulares.

> [!NOTE]
> Para permitir que um usuário faça logon na VM por RDP, você deve atribuir o logon de administrador da máquina virtual ou a função de logon de usuário da máquina virtual. Um usuário do Azure com as funções de proprietário ou colaborador atribuídas a uma VM não tem privilégios automaticamente para fazer logon na VM por RDP. Isso é para fornecer separação auditada entre o conjunto de pessoas que controlam máquinas virtuais em comparação com o conjunto de pessoas que podem acessar máquinas virtuais.

Há várias maneiras pelas quais você pode configurar atribuições de função para a VM:

- Usando a experiência do portal do Azure AD
- Usando a experiência de Azure Cloud Shell

> [!NOTE]
> As funções de logon de administrador de máquina virtual e de usuário de máquina virtual usam dataactions e, portanto, não podem ser atribuídas no escopo do grupo de gerenciamento. Atualmente, essas funções só podem ser atribuídas na assinatura, no grupo de recursos ou no escopo do recurso.

### <a name="using-azure-ad-portal-experience"></a>Usando a experiência do portal do AD do Azure

Para configurar atribuições de função para suas VMs do Windows Server 2019 datacenter habilitadas para o Azure AD:

1. Navegue até a página de visão geral da máquina virtual específica
1. Selecione o **controle de acesso (iam)** nas opções de menu
1. Selecione **Adicionar**, **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.
1. Na lista suspensa **função** , selecione uma função, como logon de **administrador de máquina virtual** ou logon de usuário de **máquina virtual**.
1. No campo **selecionar** , selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.
1. Selecione **salvar** para atribuir a função.

Após alguns instantes, a entidade de segurança é atribuída a função no escopo selecionado.

![Atribuir funções a usuários que acessarão a VM](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Usando a experiência de Azure Cloud Shell

O exemplo a seguir usa [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) para atribuir a função Logon de Administrador da Máquina Virtual para a VM ao usuário atual do Azure. O nome de usuário da conta do Azure ativa é obtido com [az account show](/cli/azure/account#az-account-show) e o escopo é definido para a VM criada na etapa anterior com [az vm show](/cli/azure/vm#az-vm-show). O escopo também pode ser atribuído em um nível de assinatura ou grupo de recursos, e as permissões normais de herança do RBAC do Azure se aplicam. Para obter mais informações, consulte [fazer logon em uma máquina virtual Linux no Azure usando Azure Active Directory autenticação](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o domínio do AAD e o domínio de nome de usuário de logon não corresponderem, você deverá especificar a ID de objeto da sua conta de usuário com o `--assignee-object-id` , e não apenas o nome do usuário para `--assignee` . É possível obter a ID de objeto para a conta de usuário com [az ad user list](/cli/azure/ad/user#az-ad-user-list).

Para obter mais informações sobre como usar o RBAC do Azure para gerenciar o acesso aos recursos de sua assinatura do Azure, consulte os seguintes artigos:

- [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
- [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../../role-based-access-control/role-assignments-portal.md)
- [Adicionar ou remover atribuições de função do Azure usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Usando o acesso condicional

Você pode impor políticas de acesso condicional, como a autenticação multifator ou a verificação de risco de entrada do usuário antes de autorizar o acesso às VMs do Windows no Azure que estão habilitadas com o logon do Azure AD. Para aplicar a política de acesso condicional, você deve selecionar o aplicativo "entrada de VM do Azure Windows" na opção de atribuição de aplicativos ou ações de nuvem e, em seguida, usar o risco de entrada como uma condição e/ou exigir autenticação multifator como um controle de acesso de concessão. 

> [!NOTE]
> Se você usar "exigir autenticação multifator" como um controle de acesso de concessão para solicitar acesso ao aplicativo "entrada de VM do Azure Windows", deverá fornecer a declaração de autenticação multifator como parte do cliente que inicia a sessão RDP para a VM Windows de destino no Azure. A única maneira de conseguir isso em um cliente Windows 10 é usar o PIN do Windows Hello para empresas ou a autenticação biométrica com o cliente RDP. O suporte para autenticação biométrica foi adicionado ao cliente RDP no Windows 10 versão 1809. A área de trabalho remota usando a autenticação do Windows Hello para empresas está disponível somente para implantações que usam o modelo de confiança de certificado e não estão disponíveis no momento para o modelo de confiança de chave

> [!WARNING]
> A autenticação multifator habilitada/imposta por usuário do Azure AD não tem suporte para entrada de VM.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Fazer logon usando as credenciais do Azure AD para uma VM do Windows

> [!IMPORTANT]
> A conexão remota com VMs Unidas ao Azure AD só é permitida a partir de computadores Windows 10 que estejam registrados no Azure AD (a compilação mínima necessária é 20H1) ou ingressado no Azure ad ou híbrido do AD LDS ingressado no **mesmo** diretório da VM. Além disso, para o RDP usando as credenciais do Azure AD, o usuário deve pertencer a uma das duas funções do Azure, logon de administrador de máquina virtual ou logon de usuário de máquina virtual. Se estiver usando um computador Windows 10 registrado no Azure AD, você deverá inserir as credenciais no formato AzureAD\UPN (por exemplo, AzureAD\john@contoso.com ). Neste momento, a bastiões do Azure não pode ser usada para fazer logon usando Azure Active Directory autenticação com a extensão AADLoginForWindows; somente o RDP direto tem suporte.

Para fazer logon em sua máquina virtual do Windows Server 2019 usando o Azure AD: 

1. Navegue até a página Visão geral da máquina virtual que foi habilitada com o logon do Azure AD.
1. Selecione **conectar** para abrir a folha conectar-se à máquina virtual.
1. Selecione **Baixar Arquivo RDP**.
1. Selecione **abrir** para iniciar o cliente de conexão de área de trabalho remota.
1. Selecione **conectar** para iniciar a caixa de diálogo de logon do Windows.
1. Faça logon usando suas credenciais do Azure AD.

Agora você está conectado à máquina virtual do Azure do Windows Server 2019 com as permissões de função atribuídas, como o usuário da VM ou o administrador da VM. 

> [!NOTE]
> Você pode salvar o. Arquivo RDP localmente no seu computador para iniciar futuras conexões de área de trabalho remota para sua máquina virtual em vez de ter que navegar até a página de visão geral da máquina virtual no portal do Azure e usando a opção conectar.

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="troubleshoot-deployment-issues"></a>Solucionar problemas de implantação

A extensão AADLoginForWindows deve ser instalada com êxito para que a VM conclua o processo de ingresso no Azure AD. Execute as etapas a seguir se a extensão da VM não for instalada corretamente.

1. RDP para a VM usando a conta de administrador local e examine o CommandExecuti'n. log em  
   
   C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0. 

   > [!NOTE]
   > Se a extensão for reiniciada após a falha inicial, o log com o erro de implantação será salvo como CommandExecution_YYYYMMDDHHMMSSSSS. log. "
1. Abra um prompt de comando do PowerShell na VM e verifique se essas consultas no ponto de extremidade do IMDS (serviço de metadados de instância) em execução no host do Azure retorna:

   | Comando a ser executado | Saída esperada |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Informações corretas sobre a VM do Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | ID de locatário válida associada à assinatura do Azure |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Token de acesso válido emitido por Azure Active Directory para a identidade gerenciada atribuída a esta VM |

   > [!NOTE]
   > O token de acesso pode ser decodificado usando uma ferramenta como [http://calebb.net/](http://calebb.net/) . Verifique se "AppID" no token de acesso corresponde à identidade gerenciada atribuída à VM.

1. Verifique se os pontos de extremidade necessários estão acessíveis da VM usando a linha de comando:
   
   - enrolar https: \/ /login.microsoftonline.com/-D –
   - enrolar https: \/ /login.microsoftonline.com/ `<TenantID>` /-D –

   > [!NOTE]
   > Substitua `<TenantID>` pela ID de locatário do Azure AD que está associada à assinatura do Azure.

   - enrolar https: \/ /enterpriseregistration.Windows.net/-D-
   - enrolar https: \/ /Device.login.microsoftonline.com/-D-
   - enrolar https: \/ /Pas.Windows.net/-D-

1. O estado do dispositivo pode ser exibido executando `dsregcmd /status` . O objetivo é que o estado do dispositivo seja mostrado como `AzureAdJoined : YES` .

   > [!NOTE]
   > A atividade de ingresso no Azure AD é capturada no Visualizador de eventos no log do dispositivo Registration\Admin do usuário.

Se a extensão AADLoginForWindows falhar com determinado código de erro, você poderá executar as seguintes etapas:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problema 1: a extensão AADLoginForWindows falha ao instalar com o código de erro de terminal ' 1007 ' e código de saída:-2145648574.

Esse código de saída é convertido para DSREG_E_MSI_TENANTID_UNAVAILABLE porque a extensão não pode consultar as informações de locatário do Azure AD.

1. Verifique se a VM do Azure pode recuperar a Tenantid do serviço de metadados de instância.

   - RDP para a VM como um administrador local e verificar se o ponto de extremidade retorna uma ID de locatário válida executando esse comando de uma linha de comando elevada na VM:
      
      - Metadados de ondulação-H: verdadeiro http://169.254.169.254/metadata/identity/info?api-version=2018-02-01

1. O administrador da VM tenta instalar a extensão AADLoginForWindows, mas uma identidade gerenciada atribuída ao sistema não habilitou a VM primeiro. Navegue até a folha identidade da VM. Na guia atribuído pelo sistema, verifique se status está alternado para ativado.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problema 2: a extensão AADLoginForWindows falha ao instalar com o código de saída:-2145648607

Esse código de saída é convertido para DSREG_AUTOJOIN_DISC_FAILED porque a extensão não é capaz de alcançar o `https://enterpriseregistration.windows.net` ponto de extremidade.

1. Verifique se os pontos de extremidade necessários estão acessíveis da VM usando a linha de comando:

   - enrolar https: \/ /login.microsoftonline.com/-D –
   - enrolar https: \/ /login.microsoftonline.com/ `<TenantID>` /-D –
   
   > [!NOTE]
   > Substitua `<TenantID>` pela ID de locatário do Azure AD que está associada à assinatura do Azure. Se você precisar localizar a ID do locatário, passe o mouse sobre o nome da sua conta para obter a ID do diretório/locatário ou selecione Azure Active Directory > Propriedades > ID do diretório no portal do Azure.

   - enrolar https: \/ /enterpriseregistration.Windows.net/-D-
   - enrolar https: \/ /Device.login.microsoftonline.com/-D-
   - enrolar https: \/ /Pas.Windows.net/-D-

1. Se qualquer um dos comandos falhar com "não foi possível resolver `<URL>` o host", tente executar esse comando para determinar o servidor DNS que está sendo usado pela VM.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Substitua `<URL>` pelos nomes de domínio totalmente qualificados usados pelos pontos de extremidade, como "login.microsoftonline.com".

1. Em seguida, veja se a especificação de um servidor DNS público permite que o comando seja executado com sucesso:

   `nslookup <URL> 208.67.222.222`

1. Se necessário, altere o servidor DNS atribuído ao grupo de segurança de rede ao qual a VM do Azure pertence.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problema 3: a extensão AADLoginForWindows falha ao instalar com o código de saída: 51

O código de saída 51 é convertido em "esta extensão não tem suporte no sistema operacional da VM".

Na visualização pública, a extensão AADLoginForWindows destina-se apenas a ser instalada no Windows Server 2019 ou no Windows 10 (Build 1809 ou posterior). Verifique se há suporte para a versão do Windows. Se não houver suporte para a compilação do Windows, desinstale a extensão de VM.

### <a name="troubleshoot-sign-in-issues"></a>Solucionar problemas de entrada

Alguns erros comuns ao tentar usar o RDP com as credenciais do Azure AD não incluem nenhuma função do Azure atribuída, cliente não autorizado ou método de entrada 2FA necessário. Use as informações a seguir para corrigir esses problemas.

O estado do dispositivo e do SSO pode ser exibido executando `dsregcmd /status` . O objetivo é que o estado do dispositivo seja mostrado como `AzureAdJoined : YES` e `SSO State` para mostrar `AzureAdPrt : YES` .

Além disso, a entrada RDP usando contas do Azure AD é capturada no Visualizador de eventos nos logs de eventos do AAD\Operational.

#### <a name="azure-role-not-assigned"></a>Função do Azure não atribuída

Se você vir a seguinte mensagem de erro ao iniciar uma conexão de área de trabalho remota para sua VM: 

- Sua conta está configurada para impedir que você use este dispositivo. Para obter mais informações, contate o administrador do sistema

![Sua conta está configurada para impedir que você use este dispositivo.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Verifique se você [configurou as políticas RBAC do Azure](../../virtual-machines/linux/login-using-aad.md) para a VM que concede ao usuário o logon de administrador da máquina virtual ou a função de logon de usuário da máquina virtual:

> [!NOTE]
> Se você estiver executando problemas com as atribuições de função do Azure, consulte [solucionar problemas do RBAC do Azure](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).
 
#### <a name="unauthorized-client"></a>Cliente não autorizado

Se você vir a seguinte mensagem de erro ao iniciar uma conexão de área de trabalho remota para sua VM: 

- Suas credenciais não funcionaram

![Suas credenciais não funcionaram](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Verifique se o computador Windows 10 que você está usando para iniciar a conexão de área de trabalho remota é um que seja ingressado no Azure AD ou que o Azure AD híbrido ingressou no mesmo diretório do Azure AD em que sua VM está unida. Para obter mais informações sobre a identidade do dispositivo, consulte o artigo [o que é uma identidade de dispositivo](./overview.md).

> [!NOTE]
> O Windows 10 Build 20H1 adicionou suporte para um PC registrado do Azure AD para iniciar a conexão RDP com sua VM. Ao usar um computador de registro do Azure AD (não ingressado no Azure ad ou ingressado no Azure AD híbrido) como o cliente RDP para iniciar conexões com sua VM, você deve inserir as credenciais no formato AzureAD\UPn (por exemplo, AzureAD\john@contoso.com ).

Verifique se a extensão AADLoginForWindows não foi desinstalada após a conclusão da junção do Azure AD.

Além disso, verifique se a política de segurança "segurança de rede: permitir que solicitações de autenticação PKU2U para este computador use identidades online" está habilitada no servidor *e* no cliente.
 
#### <a name="mfa-sign-in-method-required"></a>Método de entrada MFA necessário

Se você vir a seguinte mensagem de erro ao iniciar uma conexão de área de trabalho remota para sua VM: 

- O método de entrada que você está tentando usar não é permitido. Tente um método de entrada diferente ou contate o administrador do sistema.

![O método de entrada que você está tentando usar não é permitido.](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Se você tiver configurado uma política de acesso condicional que exige a autenticação multifator (MFA) antes de poder acessar o recurso, você precisará garantir que o PC com Windows 10 que inicia a conexão de área de trabalho remota para sua VM se conecte usando um método de autenticação forte, como o Windows Hello. Se você não usar um método de autenticação forte para sua conexão de área de trabalho remota, verá o erro anterior.

Se você não tiver implantado o Windows Hello para empresas e se isso não for uma opção por enquanto, você poderá excluir o requisito de MFA Configurando a política de acesso condicional que exclui o aplicativo "entrada de VM do Windows do Azure" na lista de aplicativos de nuvem que exigem MFA. Para saber mais sobre o Windows Hello para empresas, consulte [visão geral do Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> A autenticação de PIN do Windows Hello para empresas com RDP tem sido suportada pelo Windows 10 para várias versões, no entanto, o suporte para autenticação biométrica com RDP foi adicionado no Windows 10 versão 1809. O uso do Windows Hello para empresas auth durante o RDP só está disponível para implantações que usam o modelo de confiança de certificado e que não estão disponíveis no momento para o modelo de confiança de chave.
 
## <a name="preview-feedback"></a>Comentários de visualização

Compartilhe seus comentários sobre este recurso de visualização ou relate problemas usando-os no [Fórum de comentários do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Active Directory, confira [O que é o Azure Active Directory](../fundamentals/active-directory-whatis.md)
