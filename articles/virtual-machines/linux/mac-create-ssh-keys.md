---
title: Criar e usar um par de chaves SSH para VMs do Linux no Azure
description: Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure para melhorar a segurança do processo de autenticação.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 163d200573dc93f7c9e1d5e404febd4f939a6164
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565250"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Etapas rápidas: Criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure

Com um par de chaves SSH (Secure Shell), você pode criar VMs (máquinas virtuais) no Azure que usam chaves SSH para autenticação. Este artigo mostra como gerar e usar rapidamente um par de arquivos de chaves SSH pública e privada para VMs Linux. Você pode concluir essas etapas com o Azure Cloud Shell, um host macOS ou Linux. 

> [!NOTE]
> As VMs criadas usando chaves SSH são por padrão configuradas com senhas desabilitadas, o que aumenta muito a dificuldade de ataques de adivinhação de força bruta. 

Para obter mais informações e exemplos, confira [Etapas detalhadas para criar pares de chaves SSH](create-ssh-keys-detailed.md).

Para ver outras maneiras de gerar e usar chaves SSH em um computador Windows, consulte [Como usar chaves SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Use o comando `ssh-keygen` para gerar arquivos de chave SSH pública e privada. Por padrão, esses arquivos são criados no diretório ~/.ssh. Você pode especificar um local diferente e uma senha opcional (*frase secreta*) para acessar o arquivo de chave privada. Se um par de chaves SSH com o mesmo nome existir no local especificado, esses arquivos serão substituídos.

O seguinte comando cria um par de chaves SSH usando a criptografia RSA e o tamanho de bit 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Ao usar a [CLI do Azure](/cli/azure) para criar a VM com o comando [az vm create](/cli/azure/vm#az-vm-create), opcionalmente, você pode gerar arquivos de chave SSH pública e privada executando a opção `--generate-ssh-keys`. Os arquivos de chave são armazenados no diretório ~/.ssh, a menos que você especifique outro local com a opção `--ssh-dest-key-path`. Se um par de chaves SSH já existir e a  `--generate-ssh-keys` opção for usada, um novo par de chaves não será gerado, mas em vez disso, o par de chaves existente será usado. No comando a seguir, substitua *VMname* e *RGname* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que use chaves SSH para autenticação, especifique sua chave pública SSH ao criar a VM usando o portal do Azure, a CLI do Azure, os modelos do Azure Resource Manager ou outros métodos:

* [Criar uma máquina virtual Linux com o Portal do Azure](quick-create-portal.md)
* [Criar uma máquina virtual Linux com a CLI do Azure](quick-create-cli.md)
* [Criar uma VM Linux usando um modelo do Azure](create-ssh-secured-vm-from-template.md)

Se você não estiver familiarizado com o formato de uma chave SSH pública, exiba sua chave pública com o seguinte comando `cat`, substituindo `~/.ssh/id_rsa.pub` pelo caminho e o nome de arquivo do seu próprio arquivo de chave pública, se necessário:

```bash
cat ~/.ssh/id_rsa.pub
```

Um valor de chave pública típico se parece com este exemplo:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se você copiar e colar o conteúdo do arquivo de chave pública a ser usado no portal do Azure ou em um modelo do Resource Manager, não copie nenhum espaço em branco à direita. Para copiar uma chave pública no macOS, redirecione o arquivo de chave pública para `pbcopy`. Como no Linux, é possível redirecionar o arquivo de chave pública para programas como o `xclip`.

A chave pública colocada em sua VM do Linux no Azure é armazenada, por padrão, em ~/.ssh/id_rsa.pub, a menos que tenha especificado um local diferente ao criar o par de chaves. Para usar a [CLI do Azure 2.0](/cli/azure) para criar sua VM com uma chave pública existente, especifique o valor e, opcionalmente, o local dessa chave pública usando o comando [az vm create](/cli/azure/vm#az-vm-create) a opção `--ssh-key-values`. No seguinte comando, substitua *myVM*, *myResourceGroup*, *UbuntuLTS*, *azureuser* e *mysshkey.pub* por valores próprios:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Caso deseje usar várias chaves SSH com a VM, insira-as em uma lista separada por espaço, como este `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`.


## <a name="ssh-into-your-vm"></a>SSH em sua VM

Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, entre com SSH na VM usando o endereço IP ou o nome DNS da VM. No comando a seguir, substitua *azureuser* e *myvm.westus.cloudapp.azure.com* pelo nome de usuário administrador e o nome de domínio totalmente qualificado (ou o endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você tiver especificado uma frase secreta quando criou o par de chaves, insira-a quando solicitado durante o processo de logon. A VM é adicionada ao arquivo ~/.ssh/known_hosts e você não precisará se conectar novamente até que a chave pública na VM do Azure seja alterada ou o nome do servidor seja removido de ~/.ssh/known_hosts.

Se a VM estiver usando a política de acesso Just-In-Time, você precisará solicitar acesso antes que possa se conectar à VM. Para obter mais informações sobre a política Just-In-Time, confira [Gerenciar o acesso à máquina virtual usando a política Just-In-Time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações de como trabalhar com pares de chaves SSH, confira [Etapas detalhadas para criar e gerenciar pares de chaves SSH](create-ssh-keys-detailed.md).

* Se você tiver dificuldades com conexões SSH às VM no Azure, confira [Solucionar problemas de conexão SSH a uma VM do Linux no Azure](../troubleshooting/troubleshoot-ssh-connection.md).
