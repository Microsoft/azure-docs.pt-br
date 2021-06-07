---
title: Fazer logon em uma VM do Linux com credenciais do Azure Active Directory
description: Saiba como criar e configurar uma VM do Linux para entrar usando a autenticação do Azure Active Directory.
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 654d47102685c04d6440d7c155e4d6eb931abcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788107"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Versão prévia: fazer logon em uma máquina virtual do Linux no Azure usando a autenticação do Azure Active Directory

Para melhorar a segurança das VMs (máquinas virtuais) do Linux no Azure, você pode fazer a integração com a autenticação do AD (Azure Active Directory). Ao usar a autenticação do Azure AD para VMs do Linux, você controla e impõe de forma central as políticas que permitem ou negam acesso às VMs. Este artigo mostra como criar e configurar uma VM do Linux para usar a autenticação do Azure AD.


> [!IMPORTANT]
> A autenticação do Azure Active Directory está em versão prévia pública atualmente.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Use esse recurso em uma máquina virtual de teste que você pretende descartar após o teste.
>


Há muitos benefícios de usar a autenticação do Azure AD para fazer logon em VMs do Linux no Azure, como:

- **Segurança aprimorada:**
  - Você pode usar suas credenciais corporativas do AD para fazer logon em VMs do Linux no Azure. Não é necessário criar contas de administrador local e gerenciar o tempo de vida da credencial.
  - Dependendo menos da contas de administrador local, você não precisa se preocupar com a perda ou o roubo de credenciais, credenciais fracas configuradas pelos usuários, etc.
  - As políticas de complexidade e de tempo de vida de senha configuradas para o diretório do Azure AD também ajudam a proteger as VMs do Linux.
  - Para proteger ainda mais o logon nas máquinas virtuais do Azure, você pode configurar a autenticação multifator.
  - A capacidade de fazer logon em VMs do Linux com Azure Active Directory também funciona para clientes que usam os [Serviços de Federação](../../active-directory/hybrid/how-to-connect-fed-whatis.md).

- **Colaboração contínua:** com o Azure RBAC (controle de acesso baseado em função do Azure), você pode especificar quem pode entrar em uma determinada VM como um usuário normal ou com privilégios de administrador. Quando os usuários entram na equipe ou saem dela, você pode atualizar a política do RBAC do Azure da VM para conceder acesso conforme o necessário. Essa experiência é muito mais simples do que ter que limpar as VMs para remover as chaves públicas SSH desnecessárias. Quando os funcionários saem da organização e a conta de usuário é desabilitada ou removida do Azure AD, eles deixam de ter acesso aos recursos.

## <a name="supported-azure-regions-and-linux-distributions"></a>Regiões do Azure e distribuições do Linux com suporte

No momento, há suporte para as seguintes distribuições do Linux durante a versão prévia desse recurso:

| Distribuição | Versão |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 e Ubuntu Server 18.04 |


No momento, há suporte para as seguintes regiões do Azure durante a versão prévia desse recurso:

- Todas as regiões globais do Azure

>[!IMPORTANT]
> Para usar esse recurso de versão prévia, somente implante uma distribuição do Linux com suporte em uma região do Azure com suporte. Não há suporte para o recurso no Azure Governamental nem nas nuvens soberanas.
>
> Não há suporte para usar essa extensão nos clusters do AKS (Serviço de Kubernetes do Azure). Para obter mais informações, confira [Políticas de suporte para o AKs](../../aks/support-policies.md).


Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Requisitos de rede

Para habilitar a autenticação do Azure AD para suas VMs do Linux no Azure, você precisa garantir que a configuração de rede das VMs permita o acesso de saída aos seguintes pontos de extremidade pela porta TCP 443:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Atualmente, os grupos de segurança de rede do Azure não podem ser configurados para VMs habilitadas com a autenticação do Azure AD.

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create) e, em seguida, crie uma VM com [az vm create](/cli/azure/vm#az_vm_create) usando uma distribuição com suporte em uma região com suporte. O exemplo a seguir implanta uma VM denominada *myVM* que usa o *Ubuntu 16.04 LTS* em um grupo de recursos denominado *myResourceGroup* na região *southcentralus*. Nos exemplos a seguir, você pode fornecer seu próprio grupo de recursos e seus próprios nomes de VM, conforme o necessário.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM e dos recursos de suporte demora alguns minutos.

## <a name="install-the-azure-ad-login-vm-extension"></a>Instalar a extensão da VM de logon do Azure AD

> [!NOTE]
> Se estiver implantando essa extensão em uma VM criada anteriormente, verifique se o computador tem pelo menos 1 GB de memória alocada; caso contrário, a extensão falhará na instalação

Para fazer logon em uma VM do Linux com credenciais do Azure AD, instale a extensão de VM de logon do Azure Active Directory. As extensões de VM são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure. Use [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) para instalar a extensão *AADLoginForLinux* na VM denominada *myVM* no grupo de recursos *myResourceGroup*:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

O *provisioningState* de *Êxito* é mostrado quando a extensão é instalada com êxito na VM. A VM precisa de um agente de VM em execução para instalar a extensão. Para obter mais informações, confira [Visão geral sobre Agente de VM](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Configurar atribuições de função para a VM

A política do Azure RBAC (controle de acesso baseado em função do Azure) determina quem pode fazer logon na VM. Duas funções do Azure são usadas para autorizar o logon na VM:

- **Logon de administrador da máquina virtual**: os usuários com essa função atribuída podem fazer logon em uma máquina virtual do Azure com privilégios de usuário de administrador do Windows ou raiz do Linux.
- **Logon de usuário da máquina virtual**: os usuários com essa função atribuído podem fazer logon uma máquina virtual do Azure com privilégios de usuários regulares.

> [!NOTE]
> Para permitir que um usuário faça logon na VM por SSH, você precisa atribuir a função *Logon de Administrador da Máquina Virtual* ou *Logon de Usuário da Máquina Virtual*. As funções de logon de Administrador de Máquina Virtual e de Usuário de Máquina Virtual usam dataActions e, portanto, não podem ser atribuídas no escopo do grupo de gerenciamento. Atualmente, essas funções só podem ser atribuídas na assinatura, no grupo de recursos ou no escopo do recurso. Um usuário do Azure com a função *Proprietário* ou *Colaborador* atribuída para uma VM não tem automaticamente privilégios para fazer logon na VM por SSH. 

O exemplo a seguir usa [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para atribuir a função *Logon de Administrador da Máquina Virtual* para a VM ao usuário atual do Azure. O nome de usuário da conta do Azure ativa é obtido com [az account show](/cli/azure/account#az_account_show) e o *escopo* é definido para a VM criada na etapa anterior com [az vm show](/cli/azure/vm#az_vm_show). O escopo também pode ser atribuído em um nível de assinatura ou de grupo de recursos e as permissões normais de herança do RBAC do Azure se aplicam. Para obter mais informações, confira [RBAC do Azure](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Se o domínio do AAD e o domínio do nome de usuário de logon não corresponderem, especifique a ID de objeto da conta de usuário com *--assignee-object-id*, não apenas o nome de usuário de *--assignee*. É possível obter a ID de objeto para a conta de usuário com [az ad user list](/cli/azure/ad/user#az_ad_user_list).

Para obter mais informações sobre como usar o RBAC do Azure para gerenciar o acesso aos recursos de sua assinatura do Azure, confira usando a [CLI do Azure](../../role-based-access-control/role-assignments-cli.md), o [portal do Azure](../../role-based-access-control/role-assignments-portal.md) ou o [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Usando o acesso condicional

Você pode impor políticas de Acesso Condicional, como a autenticação multifator ou a verificação de risco de entrada do usuário antes de autorizar o acesso às VMs do Linux no Azure que estejam habilitadas com a conexão do Azure AD. Para aplicar a política de Acesso Condicional, você deve selecionar o aplicativo "Entrada em Máquina Virtual do Linux do Microsoft Azure" na opção de atribuição de aplicativos ou ações de nuvem e, em seguida, usar o Risco de entrada como uma condição e/ou exigir a autenticação multifator como um controle de acesso de concessão. 

> [!WARNING]
> A autenticação multifator habilitada/imposta por usuário no Azure AD não tem suporte para entrada na VM.

## <a name="log-in-to-the-linux-virtual-machine"></a>Fazer logon na máquina virtual do Linux

Primeiro, exiba o endereço IP público da VM com [az vm show](/cli/azure/vm#az_vm_show):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Faça logon na máquina virtual do Linux no Azure usando suas credenciais do Azure AD. O parâmetro `-l` permite que você especifique seu próprio endereço da conta do Azure AD. Substitua a conta de exemplo pelo nome da sua própria conta. Endereços de conta devem ser inseridos em letras minúsculas. Substitua o endereço IP de exemplo pelo endereço IP público de sua VM, obtido no comando anterior.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Você precisará entrar no Azure AD com um código de uso avulso em [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin). Copie e cole o código de uso avulso na página de logon do dispositivo.

Quando solicitado, insira suas credenciais de logon do Azure AD na página de logon. 

A seguinte mensagem é mostrada no navegador da Web quando você é autenticado com êxito: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Feche a janela do navegador, retorne para o prompt do SSH e pressione a tecla **Enter**. 

Agora, você está conectado à máquina virtual do Linux no Azure com as permissões de função atribuídas, como *Usuário da VM* ou *Administrador da VM*. Se a conta de usuário recebeu a função *Logon de Administrador da Máquina Virtual*, você pode usar `sudo` para executar comandos que exigem privilégios de raiz.

## <a name="sudo-and-aad-login"></a>Logon do AAD e Sudo

Na primeira vez que você executar o sudo, será solicitado que você autentique uma segunda vez. Se você não quiser ter que se autenticar novamente para executar o sudo, edite o arquivo sudoers `/etc/sudoers.d/aad_admins` e substitua esta linha:

```bash
%aad_admins ALL=(ALL) ALL
```

Por esta linha:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>Solucionar problemas de entrada

Alguns erros comuns ao tentar usar SSH com as credenciais do Azure AD incluem a ausência de funções do Azure atribuídas e prompts repetidos para entrar. Use as seções a seguir para corrigir esses problemas.

### <a name="access-denied-azure-role-not-assigned"></a>Acesso negado: função do Azure não atribuída

Se o erro a seguir aparecer no prompt do SSH, verifique se você configurou políticas de RBAC do Azure para a VM que concedem ao usuário a função *Logon de Administrador da Máquina Virtual* ou *Logon de Usuário da Máquina Virtual*:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Se você estiver encontrando problemas com as atribuições de função do Azure, confira [Solucionar problemas do RBAC do Azure](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).

### <a name="continued-ssh-sign-in-prompts"></a>Prompts contínuos de entrada de SSH

Ao concluir com êxito a etapa de autenticação em um navegador da Web, você poderá ser imediatamente solicitado a entrar novamente com um novo código. Este erro normalmente é causado por uma incompatibilidade entre o nome de entrada especificado no prompt de SSH e a conta usada para entrar no Azure AD. Para corrigir esse problema:

- Verifique se o nome de entrada especificado no prompt de SSH está correto. Um erro de digitação no nome de entrada pode causar uma incompatibilidade entre o nome de entrada especificado no prompt de SSH e a conta usada para entrar no Azure AD. Por exemplo, você digitou *azuresuer\@contoso.onmicrosoft.com* em vez de *azureuser\@contoso.onmicrosoft.com*.
- Se você tiver várias contas de usuário, não forneça uma conta de usuário diferente na janela do navegador ao entrar no Azure AD.
- O Linux é um sistema operacional que diferencia maiúsculas de minúsculas. Há uma diferença entre 'Azureuser@contoso.onmicrosoft.com' e 'azureuser@contoso.onmicrosoft.com', que pode causar uma incompatibilidade. Especifique o UPN com a diferenciação correta de maiúsculas de minúsculas no prompt de SSH.

### <a name="other-limitations"></a>Outras limitações

Atualmente não há suporte aos usuários que herdam direitos de acesso por meio de grupos aninhados ou atribuições de função. O usuário ou grupo deve ser atribuído diretamente às [atribuições de função necessárias](#configure-role-assignments-for-the-vm). Por exemplo, o uso de grupos de gerenciamento ou atribuições de função de grupo aninhado não concederá as permissões corretas para permitir que o usuário entre.

## <a name="preview-feedback"></a>Comentários de visualização

Compartilhe seus comentários sobre este recurso de visualização ou informe problemas usando-o no [Fórum de comentários do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Active Directory, confira [O que é o Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
