---
title: Usar chaves SSH para se conectar a VMs Linux
description: Saiba como gerar e usar chaves SSH de um computador Windows para se conectar a uma máquina virtual Linux no Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: f018f591052050431996e3017335ab003973d25a
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770998"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como usar chaves SSH com o Windows no Azure

Este artigo é para usuários do Windows que desejam [criar](#create-an-ssh-key-pair) e usar chaves SSH ( *Secure Shell* ) para [se conectar](#connect-to-your-vm) a VMS (máquinas virtuais) do Linux no Azure. Você também pode [gerar e armazenar chaves SSH no portal do Azure](../ssh-keys-portal.md) para usar ao criar VMs no Portal.


Para usar chaves SSH de um cliente Linux ou macOS, consulte as [etapas rápidas](mac-create-ssh-keys.md). Para obter uma visão geral mais detalhada do SSH, consulte [etapas detalhadas: criar e gerenciar chaves SSH para autenticação em uma VM do Linux no Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

O [SSH](https://www.ssh.com/ssh/) é um protocolo de conexão criptografado que permite entradas seguras em conexões não seguras. SSH é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, usar senhas com SSH ainda deixa a VM vulnerável a ataques de força bruta. É recomendável conectar-se a uma VM por SSH usando um par de chaves pública-privada, também conhecido como *chaves SSH*. 

O par de chaves pública-privada é como o bloqueio na sua porta de frente. O bloqueio é exposto ao **público**, qualquer pessoa com a chave correta pode abrir a porta. A chave é **privada** e só é fornecida às pessoas confiáveis, pois podem ser usadas para desbloquear a porta. 

- A *chave pública* é colocada em sua VM Linux quando você cria a VM. 

- A *chave privada* permanece em seu sistema local. Proteja essa chave privada. Não a compartilhe.

Quando você se conecta à VM do Linux, a VM testa o cliente SSH para verificar se ele tem a chave privada correta. Se o cliente tiver a chave privada, será concedido o acesso à VM. 

Dependendo das políticas de segurança de sua organização, você pode reutilizar um único par de chaves para acessar várias VMs e serviços do Azure. Você não precisa de um par separado de chaves para cada VM. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) deve ter acesso à sua chave privada.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>Clientes SSH

As versões recentes do Windows 10 incluem [comandos de cliente do OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) para criar e usar chaves SSH e fazer conexões SSH do PowerShell ou de um prompt de comando. Essa é a maneira mais fácil de criar uma conexão SSH para sua VM Linux, de um computador Windows. 

Você também pode usar o bash no [Azure cloud Shell](../../cloud-shell/overview.md) para se conectar à sua VM. Você pode usar Cloud Shell em um [navegador da Web](https://shell.azure.com/bash), na [portal do Azure](https://portal.azure.com)ou como um terminal no Visual Studio Code usando a [extensão de conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Você também pode instalar o [subsistema do Windows para Linux](/windows/wsl/about) para se conectar à VM por SSH e usar outras ferramentas nativas do Linux em um shell bash.

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Crie um par de chaves SSH usando o `ssh-keygen` comando. Insira um nome de arquivo ou use o padrão mostrado entre parênteses (por exemplo `C:\Users\username/.ssh/id_rsa` ).  Insira uma frase secreta para o arquivo ou deixe a frase secreta em branco se você não quiser usar uma frase secreta. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Criar uma VM usando sua chave

Para criar uma VM do Linux que usa chaves SSH para autenticação, forneça sua chave pública SSH ao criar a VM.

Usando o CLI do Azure, você especifica o caminho e o nome do arquivo para a chave pública usando `az vm create` e o `--ssh-key-value` parâmetro.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa
```

Com o PowerShell, use `New-AzVM` e adicione a chave SSH à configuração da VM usando '. Para obter um exemplo, consulte [início rápido: criar uma máquina virtual Linux no Azure com o PowerShell](quick-create-powershell.md).

Se você fizer muitas implantações usando o portal, talvez queira carregar sua chave pública no Azure, onde ela pode ser facilmente selecionada ao criar uma VM no Portal. Para obter mais informações, consulte [carregar uma chave SSH](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, realize SSH para sua VM usando o endereço IP ou nome DNS da VM. Substitua *azureuser* e *10.111.12.123* no comando a seguir pelo nome de usuário administrador, o endereço IP (ou nome de domínio totalmente qualificado) e o caminho para a chave privada:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Se você tiver configurado uma frase secreta quando criou o par de chaves, insira a frase secreta quando solicitado.

Se a VM estiver usando a política de acesso Just-In-Time, você precisará solicitar acesso antes que possa se conectar à VM. Para obter mais informações sobre a política Just-In-Time, confira [Gerenciar o acesso à máquina virtual usando a política Just-In-Time](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre chaves SSH no portal do Azure, consulte [gerar e armazenar chaves SSH no portal do Azure](../ssh-keys-portal.md) para usar ao criar VMs no Portal.

- Para ver etapas detalhadas, opções e exemplos avançados para trabalhar com chaves SSH, confira [Detailed steps to create SSH key pairs](create-ssh-keys-detailed.md) (Etapas detalhadas para criar pares de chave SSH).

- Você também pode usar o PowerShell no Azure Cloud Shell para gerar as chaves SSH e estabelecer conexões SSH com VMs Linux. Consulte o [Início rápido do PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Se você tiver dificuldades ao usar o SSH para se conectar às suas VMs Linux, confira [Solucionar problemas de conexão SSH com uma VM Linux no Azure](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
