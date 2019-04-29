---
title: Usar chaves SSH com Windows para VMs Linux | Microsoft Docs
description: Saiba como gerar e usar chaves SSH em um computador Windows para se conectar a uma máquina virtual Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 0ac62a99f5735647f67917d441645e30444b3818
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473586"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como usar chaves SSH com o Windows no Azure

Este artigo descreve maneiras de gerar e usar chaves SSH (*secure shell*) em um computador Windows para criar e conectar-se a uma VM (máquina virtual) do Linux no Azure. Para usar as chaves SSH de um cliente Linux ou macOS, consulte as diretrizes [rápidas](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes SSH
Você se conecta às VMs Linux e as gerencia no Azure usando um *cliente SSH*. Computadores que executam o Linux ou macOS geralmente têm um pacote de comandos SSH para gerar e gerenciar chaves SSH e estabelecer conexões SSH. 

Computadores Windows nem sempre têm comandos SSH equivalentes instalados. Versões recentes do Windows 10 fornecem [comandos do cliente OpenSSH](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) para criar e gerenciar chaves SSH e fazer conexões SSH de um prompt de comando. Versões recentes do Windows 10 também incluem o [Windows Subsystem para Linux](https://docs.microsoft.com/windows/wsl/about) para executar e acessar utilitários como um cliente SSH de forma nativa dentro de um shell Bash. 

Outros clientes SSH comuns do Windows que podem ser instalados localmente estão incluídos nos seguintes pacotes:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git for Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Também é possível usar os utilitários SSH disponíveis no Bash no [Azure Cloud Shell](../../cloud-shell/overview.md). 

* Acesse o Cloud Shell no seu navegador da Web em [https://shell.azure.com](https://shell.azure.com) ou no [Portal do Azure](https://portal.azure.com). 
* Acesse o Cloud Shell como um terminal de dentro do Visual Studio Code instalando a [extensão de Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH
As seguintes seções descrevem duas opções para criar um par de chaves SSH no Windows. É possível usar um comando shell (`ssh-keygen`) ou uma ferramenta de GUI (PuTTYgen).

### <a name="create-ssh-keys-with-ssh-keygen"></a>Criar chaves SSH com ssh-keygen

Se você executar um shell de comando no Windows que ofereça suporte a ferramentas de cliente SSH (ou use o Shell de Nuvem do Azure), crie um par de chaves SSH usando o comando `ssh-keygen`. Digite o comando a seguir e responda aos prompts. Se um par de chaves SSH existir no local escolhido, esses arquivos serão substituídos. 

```bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, confira as etapas [rápidas](mac-create-ssh-keys.md) ou [detalhadas](create-ssh-keys-detailed.md) para criar chaves SSH usando `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Criar chaves SSH com PuttyGen

Se você preferir usar uma ferramenta baseada em GUI para criar chaves SSH, use o gerador de chaves PuttyGen, incluído no [pacote de download do Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). 

Para criar um par de chaves SSH RSA com o PuttyGen:

1. Inicie o PuttyGen.

2. Clique em **Gerar**. Por padrão, o PuttyGen gera uma chave SSH-2 RSA de 2048 bits.

4. Mova o mouse na área em branco para fornecer a aleatoriedade para a chave.

5. Depois que a chave pública for gerada, opcionalmente, insira e confirme uma frase secreta. Será solicitado que você insira a frase secreta ao autenticar-se na VM com sua chave SSH privada. Sem uma frase secreta, se um indivíduo obtiver a chave privada, ele poderá entrar em qualquer VM ou serviço que use essa chave. É recomendável criar uma frase secreta. No entanto, se você esquecer a senha, não é possível recuperá-la.

6. A chave pública é exibida na parte superior da janela. É possível copiar toda esta chave pública e, em seguida, colá-la no portal do Azure ou em um modelo do Azure Resource Manager quando você cria uma VM Linux. Também é possível selecionar **Salvar chave pública** para salvar uma cópia no computador:

    ![Salvar o arquivo de chave pública PuTTY](./media/ssh-from-windows/save-public-key.png)

7. Opcionalmente, para salvar a chave privada no formato de chave privada do PuTTy (arquivo .ppk), clique em **Salvar chave privada**. Será necessário ter o arquivo .ppk posteriormente para usar o PuTTY para realizar uma conexão SSH com a VM.

    ![Salvar o arquivo de chave privada do PuTTY](./media/ssh-from-windows/save-ppk-file.png)

    Se desejar salvar a chave privada no formato OpenSSH, o formato de chave privada usado por muitos clientes SSH, selecione **Conversões** > **Exportar chave OpenSSH**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que usa chaves SSH para autenticação, forneça sua chave SSH pública ao criar a VM usando o Portal do Azure ou outros métodos.

O exemplo a seguir mostra como você pode copiar e colar essa chave pública no portal do Azure ao criar uma VM Linux. Normalmente, a chave pública é armazenada no diretório ~/.ssh/authorized_key em sua nova VM.

   ![Usar chave pública quando você cria uma VM no portal do Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Uma maneira de criar uma conexão SSH do Windows para a VM Linux é usar um cliente SSH. Este será o método preferencial se você tiver um cliente SSH instalado no seu sistema Windows ou se usar ferramentas SSH no Bash no Azure Cloud Shell. Se você preferir uma ferramenta baseada em GUI, conecte-se com o PuTTY.  

### <a name="use-an-ssh-client"></a>Usar um cliente SSH
Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, realize SSH para sua VM usando o endereço IP ou nome DNS da VM. Substitua *azureuser* e *myvm.westus.cloudapp.azure.com* no comando a seguir pelo nome de usuário administrador e o nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você tiver configurado uma frase secreta ao criar o par de chaves, insira-a quando solicitado durante o processo de entrada.

Se a VM estiver usando a política de acesso Just-In-Time, você precisará solicitar acesso antes que possa se conectar à VM. Para obter mais informações sobre a política Just-In-Time, confira [Gerenciar o acesso à máquina virtual usando a política Just-In-Time](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Conectar com o PuTTY

Se você tiver instalado o [pacote de download do PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) e gerado anteriormente um arquivo .ppk (chave privada PuTTY), será possível conectar-se a uma VM Linux com o PuTTY.

1. Inicie o PuTTy.

2. Preencha o nome do host ou o endereço IP da sua VM no portal do Azure:

    ![Abrir nova conexão PuTTY](./media/ssh-from-windows/putty-new-connection.png)

3. Selecione a categoria **Conexão** > **SSH** > **Autenticação**. Procure e selecione a chave privada do PuTTY (arquivo .ppk):

    ![Selecionar a chave privada do PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)

4. Clique em **Abrir** para se conectar à VM.

## <a name="next-steps"></a>Próximas etapas

* Para ver etapas detalhadas, opções e exemplos avançados para trabalhar com chaves SSH, confira [Detailed steps to create SSH key pairs](create-ssh-keys-detailed.md) (Etapas detalhadas para criar pares de chave SSH).

* Você também pode usar o PowerShell no Azure Cloud Shell para gerar as chaves SSH e estabelecer conexões SSH com VMs Linux. Consulte o [Início rápido do PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

* Se você tiver dificuldades ao usar o SSH para se conectar às suas VMs Linux, confira [Solucionar problemas de conexão SSH com uma VM Linux no Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
