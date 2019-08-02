---
title: Etapas detalhadas — par de chaves SSH para VMs Linux no Azure | Microsoft Docs
description: Conheça as etapas detalhadas para criar e gerenciar um par de chaves SSH pública e privada para VMs Linux no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 1859cdfaead27fda1956b553ebea06374c9cdc6a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668075"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Etapas detalhadas: Criar e gerenciar chaves SSH para autenticação para uma VM do Linux no Azure 
Com um par de chaves SSH (Secure Shell), você poderá criar uma máquina virtual Linux no Azure que usam chaves SSH para autenticação como padrão, eliminando a necessidade de senhas para entrar. VMs criadas com o Portal do Azure, CLI do Azure, modelos do Resource Manager ou outras ferramentas podem incluir sua chave pública SSH como parte da implantação, que configura a autenticação de chave SSH para conexões SSH. 

Este artigo fornece informações detalhadas e etapas para criar e gerenciar um par de arquivos de chaves públicas-privadas SSH RSA para conexões de cliente. Se você quiser usar comandos rápidos, consulte [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](mac-create-ssh-keys.md).

Para ver outras maneiras de gerar e usar chaves SSH em um computador Windows, consulte [Como usar chaves SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Frase secreta da chave privada
A chave privada SSH deve ter uma frase secreta bastante segura para protegê-la. Esta frase secreta serve apenas para acessar o arquivo de chave SSH privada e *não é* a senha da conta do usuário. Quando você adiciona uma frase secreta à chave SSH, ela criptografa a chave privada usando AES de 128 bits, para que a chave privada seja inútil sem a frase secreta para descriptografá-la. Se um invasor roubasse sua chave privada e se ela não tivesse uma frase secreta, ele poderia usar essa chave privada para entrar em qualquer servidor com a chave pública correspondente. Se uma chave privada for protegida por uma frase secreta, ela não poderá ser usada por esse invasor, proporcionando uma camada adicional de segurança para sua infraestrutura no Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Benefícios e uso das chaves SSH

Quando você criar uma VM do Azure especificando a chave pública, o Azure copia a chave pública (no formato `.pub`) para a pasta `~/.ssh/authorized_keys` na VM. As chaves SSH no `~/.ssh/authorized_keys` são usadas para desafiar o cliente para coincidir com a chave privada correspondente em uma conexão SSH. Em uma VM Linux do Azure que usa chaves SSH para autenticação, o Azure configura o servidor SSHD para não permitir entrada com senha, apenas com chaves SSH. Portanto, ao criar VMs Linux do Azure com chaves SSH, você pode ajudar a proteger a implantação de VM e evitar a etapa de configuração pós-implantação típica de desabilitar as senhas no arquivo `sshd_config`.

Se não quiser usar chaves SSH, você ainda poderá configurar suas VMs Linux usando uma autenticação de senha. Se a VM não for exposta à Internet, o uso de senhas pode ser suficiente. No entanto, ainda é preciso gerenciar as senhas para cada VM Linux, bem como manter políticas e práticas de senha íntegras, como tamanho mínimo de senha e atualizações regulares. O uso de chaves SSH reduz a complexidade do gerenciamento de credenciais individuais em várias VMs.

## <a name="generate-keys-with-ssh-keygen"></a>Gerar chaves com ssh-keygen

Para criar as chaves, um comando preferencial é `ssh-keygen`, que está disponível com utilitários OpenSSH no Azure Cloud Shell, um host do Linux ou macOS o [Subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/about) e outras ferramentas. O `ssh-keygen` faz uma série de perguntas e, em seguida, grava uma chave privada e uma chave pública correspondente. 

As chaves SSH são mantidas por padrão no diretório `~/.ssh`.  Se você não tiver um diretório `~/.ssh`, o comando `ssh-keygen` o criará para você com as permissões corretas.

### <a name="basic-example"></a>Exemplo básico

O comando `ssh-keygen` a seguir gera arquivos de chave pública e privada SSH RSA de 2048 bits por padrão no diretório `~/.ssh`. Se um par de chaves SSH existir no local atual, esses arquivos serão substituídos.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Exemplo detalhado
O exemplo a seguir mostra as opções de comando adicionais para criar um par de chaves SSH RSA. Se um par de chaves SSH existir no local atual, esses arquivos serão substituídos. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Comando explicado**

`ssh-keygen` = programa usado para criar as chaves

`-t rsa` = tipo de chave a ser criada, nesse caso o formato de RSA

`-b 4096` = o número de bits na chave, nesse caso, 4096

`-C "azureuser@myserver"` = um comentário acrescentado ao fim do arquivo de chave pública para identificá-lo facilmente. Normalmente, um endereço de email é usado como o comentário, mas você pode usar o que melhor funcionar para sua infraestrutura.

`-f ~/.ssh/mykeys/myprivatekey` = o nome do arquivo de chave privada se você optar por não usar o nome padrão. Um arquivo de chave pública correspondente é acrescentado com `.pub` e gerado no mesmo diretório. O diretório precisa existir.

`-N mypassphrase` = uma frase secreta adicional usada para acessar o arquivo de chave privada. 

### <a name="example-of-ssh-keygen"></a>Exemplo de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>Arquivos de chave salvos

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

O nome do par de chaves para este artigo. Ter um par de chaves denominado `id_rsa` é o padrão; algumas ferramentas podem esperar o nome de arquivo da chave privada `id_rsa`, portanto, é uma boa ideia ter um. O diretório `~/.ssh/` é o local padrão para os pares de chave SSH e o arquivo de configuração SSH. Se não for especificado com um caminho completo, `ssh-keygen` criará as chaves no diretório de trabalho atual e não o `~/.ssh` padrão.

#### <a name="list-of-the-ssh-directory"></a>Lista do diretório `~/.ssh`

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Frase secreta da chave

`Enter passphrase (empty for no passphrase):`

É *altamente* recomendável adicionar uma frase secreta à sua chave privada. Sem uma frase secreta para proteger o arquivo de chave, qualquer pessoa com o arquivo poderá usá-lo para entrar em qualquer servidor com a chave pública correspondente. Portanto, adicionar uma frase secreta oferece mais proteção caso alguém obtenha acesso ao arquivo da chave privada, concedendo tempo para alterar as chaves.

## <a name="generate-keys-automatically-during-deployment"></a>Gerar chaves automaticamente durante a implantação

Ao usar a [CLI do Azure](/cli/azure) para criar a VM, opcionalmente, você poderá gerar arquivos de chave SSH pública e privada executando o comando [az vm create](/cli/azure/vm) com a opção `--generate-ssh-keys`. As chaves são armazenadas no diretório ~/.ssh. Observe que essa opção de comando não substitui as chaves se elas já existem no local.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Forneça a chave pública SSH ao implantar uma VM

Para criar uma VM Linux que usa chaves SSH para autenticação, forneça sua chave SSH pública ao criar a VM usando o Portal do Azure, a CLI, modelos do Resource Manager ou outros métodos. Ao usar o portal, insira a chave pública em si. Se você usar a [CLI do Azure](/cli/azure) para criar a VM com uma chave pública existente, especifique o valor ou o local dessa chave pública executando o comando [az vm create](/cli/azure/vm) com a opção `--ssh-key-value`. 

Se você não estiver familiarizado com o formato de uma chaves públicas SSH, veja sua chave pública executando `cat` da seguinte forma, substituindo `~/.ssh/id_rsa.pub` por seu próprio local de arquivo de chave pública:

```bash
cat ~/.ssh/id_rsa.pub
```

A saída é semelhante à seguinte (mostrada aqui em uma versão editada):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Se você copiar e colar o conteúdo do arquivo de chave pública no Portal do Azure ou em um modelo do Resource Manager, não copie um espaço em branco adicional nem introduza quebras de linha adicionais. Por exemplo, caso use o macOS, direcione o arquivo de chave pública (por padrão, `~/.ssh/id_rsa.pub`) para **pbcopy** para copiar o conteúdo (há outros programas Linux que fazem o mesmo, como o `xclip`).

Se você preferir usar uma chave pública que está em um formato de várias linhas, gere uma chave formatada RFC4716 em um contêiner de pem da chave pública que você criou anteriormente.

Para criar uma chave formatada RFC4716 com base em uma chave pública SSH existente:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH para a VM com um cliente SSH
Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, realize SSH para sua VM usando o endereço IP ou nome DNS da VM. Substitua *azureuser* e *myvm.westus.cloudapp.azure.com* no comando a seguir pelo nome de usuário administrador e o nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você tiver fornecido uma senha ao criar o par de chaves, insira a senha quando receber a solicitação durante o processo de entrada. (O servidor é adicionado à sua pasta `~/.ssh/known_hosts` e você não receberá uma solicitação para se conectar novamente até que a chave pública em sua VM do Azure mude ou o nome do servidor seja removido do `~/.ssh/known_hosts`.)

Se a VM estiver usando a política de acesso Just-In-Time, você precisará solicitar acesso antes que possa se conectar à VM. Para obter mais informações sobre a política Just-In-Time, confira [Gerenciar o acesso à máquina virtual usando a política Just-In-Time](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Usando ssh-agent para armazenar sua frase secreta da chave privada

Para evitar digitar sua frase secreta do arquivo de chave privada a cada entrada do SSH, você poderá usar `ssh-agent` para armazenar em cache a frase secreta do arquivo de chave privada. Se você estiver usando um Mac, o Keychain do macOS armazenará com segurança a frase secreta da chave privada ao invocar `ssh-agent`.

Verifique e use `ssh-agent` e `ssh-add` para informar o sistema SSH sobre os arquivos de chave, para que a frase secreta não precise ser usada interativamente.

```bash
eval "$(ssh-agent -s)"
```

Agora, adicione a chave privada ao `ssh-agent` usando o comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Agora, a frase secreta da chave privada está armazenada em `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Usar ssh-copy-id para copiar a chave para uma VM existente
Se já tiver criado uma VM, você poderá instalar a nova chave pública SSH na VM Linux com um comando semelhante ao seguinte:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Criar e configurar um arquivo de configuração do SSH

Você pode criar e configurar um arquivo de configuração SSH (`~/.ssh/config`) para acelerar os logons e otimizar o comportamento do seu cliente SSH. 

O exemplo a seguir mostra uma configuração simples que você pode usar para entrar rapidamente como um usuário para uma VM específica usando a chave privada SSH padrão. 

### <a name="create-the-file"></a>Criar o arquivo

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Edite o arquivo para adicionar a nova configuração de SSH

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Exemplo de configuração

Adicione os parâmetros de configurações apropriadas para a VM do seu host.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Você pode acrescentar configurações para os hosts adicionais para permitir que cada um use seu próprio par de chaves dedicado. Consulte o [arquivo de configuração SSH](https://www.ssh.com/ssh/config/) para obter mais opções de configuração avançadas.

Agora que tem um par de chaves SSH e um arquivo de configuração do SSH configurado, você pode entrar na VM do Linux de forma rápida e segura. Quando você executa o comando a seguir, o SSH localiza e carrega as configurações do bloco `Host myvm` no arquivo de configuração SSH.

```bash
ssh myvm
```

Na primeira vez que você entrar em um servidor usando uma chave SSH, o comando solicitará a frase secreta para esse arquivo de chave.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa é criar VMs do Linux do Azure usando a nova chave pública SSH. As VMs do Azure criadas com uma chave pública SSH como a entrada estão mais protegidas do que as VMs criadas com as senhas do método de entrada padrão.

* [Criar uma máquina virtual Linux com o Portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com a CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM Linux usando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
