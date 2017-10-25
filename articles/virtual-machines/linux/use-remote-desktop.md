---
title: "Usar a Área de Trabalho Remota para uma VM do Linux no Azure | Microsoft Docs"
description: "Saiba como instalar e configurar a Área de Trabalho Remota (xrdp) para conectar-se a uma VM do Linux no Azure usando ferramentas gráficas"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: iainfou
ms.openlocfilehash: d8d6130a270285c84c1dd057a3512cdeb39287f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalar e configurar a Área de Trabalho Remota para conectar-se uma VM do Linux no Azure
As VMs (máquinas virtuais) do Linux no Azure são normalmente gerenciadas a partir da linha de comando usando uma conexão SSH (secure shell). Para novos usuários Linux, ou para cenários de solução rápida de problemas, o uso da área de trabalho remota pode ser mais fácil. Este artigo fornece detalhes sobre como instalar e configurar um ambiente de área de trabalho ([xfce](https://www.xfce.org)) e área de trabalho remota ([xrdp](http://www.xrdp.org)) para sua VM do Linux usando o modelo de implantação do Resource Manager.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo exige uma VM do Linux no Azure. Se você precisar criar uma VM, use um dos seguintes métodos:

- A [CLI 2.0 do Azure](quick-create-cli.md)
- O [Portal do Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalar um ambiente de área de trabalho em sua VM do Linux
A maioria das VMs do Linux no Azure não tem um ambiente de área de trabalho instalado por padrão. As VMs do Linux são gerenciadas normalmente usando conexões SSH, em vez de um ambiente de área de trabalho. Há vários ambientes de área de trabalho no Linux para sua escolha. Dependendo de sua escolha de ambiente de área de trabalho, ele pode consumir de um a 2 GB de espaço em disco e demorar de cinco a 10 minutos para instalar e configurar todos os pacotes necessários.

O exemplo a seguir instala o ambiente de área de trabalho leve [xfce4](https://www.xfce.org/) em uma VM do Ubuntu. Os comandos para outras distribuições variam um pouco (use `yum` para instalar no Red Hat Enterprise Linux e configurar as regras `selinux` apropriadas ou use `zypper` para instalar no SUSE, por exemplo).

Primeiro, SSH para sua VM. O seguinte exemplo conecta-se à VM chamada *myvm.westus.cloudapp.azure.com* com o nome de usuário *azureuser*:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você estiver usando o Windows e precisar de mais informações sobre como usar o SSH, veja [Como usar chaves do SSH com o Windows](ssh-from-windows.md).

Em seguida, instale o xfce usando `apt` da seguinte maneira:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalar e configurar um servidor de área de trabalho remoto
Agora que você tem um ambiente de área de trabalho instalado, configure um serviço de área de trabalho remoto para escutar as conexões de entrada. [xrdp](http://xrdp.org) é um servidor RDP (Protocolo de Área de Trabalho Remota) de código-fonte aberto que está disponível na maioria das distribuições Linux e funciona bem com xfce. Instale o xrdp em sua VM do Ubuntu da seguinte maneira:

```bash
sudo apt-get install xrdp
```

Diga ao xrdp o ambiente de área de trabalho a ser usado ao iniciar a sessão. Configure xrdp para usar xfce como seu ambiente de área de trabalho da seguinte maneira:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço xrdp para que as alterações entrem em vigor da seguinte maneira:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir a senha da conta de usuário local
Se você tiver criado uma senha para sua conta de usuário durante a criação de sua VM, ignore esta etapa. Se você usar apenas a autenticação de chave SSH e não tiver uma senha de conta local definida, especifique uma senha antes de usar o xrdp para fazer logon em sua VM. O xrdp não pode aceitar chaves SSH para autenticação. O seguinte exemplo especifica uma senha para a conta de usuário *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> A especificação de uma senha não atualiza a configuração de SSHD para permitir logons de senha, caso ela não permita no momento. De uma perspectiva de segurança, convém conectar-se à sua VM com um túnel SSH usando a autenticação baseada em chave e, depois, conectar-se ao xrdp. Nesse caso, ignore a etapa a seguir sobre a criação de uma regra de grupo de segurança de rede para permitir o tráfego de área de trabalho remota.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra de Grupo de Segurança de Rede para tráfego de Área de Trabalho Remota
Para permitir que o tráfego da Área de Trabalho Remota alcance sua VM do Linux, é necessário criar uma regra de grupo de segurança de rede que permite ao TCP na porta 3389 acessar sua VM. Para saber mais sobre grupos de segurança de rede, confira [O que é um Grupo de Segurança de Rede?](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Você também pode [usar o Portal do Azure para criar uma regra de grupo de segurança de rede](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Os exemplos a seguir criam uma regra de grupo de segurança de rede com [az network nsg rule create](/cli/azure/network/nsg/rule#create) chamada *myNetworkSecurityGroupRule* para *permitir* o tráfego na porta *TCP* *3389*.

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1010 \
    --destination-port-range 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Conectar-se a sua VM do Linux com um cliente de Área de Trabalho Remota
Abra o cliente da área de trabalho remota local e conecte-se ao endereço IP ou nome DNS de sua VM do Linux. Insira o nome de usuário e a senha da conta de usuário em sua VM da seguinte maneira:

![Conectar-se ao xrdp usando seu cliente de Área de Trabalho Remota](./media/use-remote-desktop/remote-desktop-client.png)

Após a autenticação, o ambiente de área de trabalho xfce carregará e será semelhante ao exemplo a seguir:

![Ambiente de área de trabalho xfce por meio de xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)


## <a name="troubleshoot"></a>Solucionar problemas
Se você não puder se conectar à sua VM do Linux usando um cliente de Área de Trabalho Remota, use `netstat` em sua VM do Linux para verificar se sua VM está escutando conexões RDP da seguinte maneira:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo a seguir mostra a VM escutando na porta TCP 3389, conforme o esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o serviço xrdp não estiver escutando, em uma VM do Ubuntu, reinicie o serviço da seguinte maneira:

```bash
sudo service xrdp restart
```

Examine os logs */var/log*Thug na VM Ubuntu para obter indicações sobre o motivo de o serviço não estar respondendo. Você também pode monitorar o syslog durante uma tentativa de conexão de área de trabalho remota para exibir quaisquer erros:

```bash
tail -f /var/log/syslog
```

Outras distribuições do Linux como Red Hat Enterprise Linux e SUSE podem ter maneiras diferentes de reiniciar serviços e locais alternativos de arquivos de log para examinar.

Se você não receber nenhuma resposta em seu cliente de área de trabalho remota e não ver quaisquer eventos no log do sistema, esse comportamento indicará que o tráfego da área de trabalho remota não consegue alcançar a VM. Examine suas regras de grupo de segurança de rede para garantir que você tenha uma regra para permitir o TCP na porta 3389. Para saber mais, veja [Solucionar problemas de conectividade do aplicativo](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como criar e usar chaves SSH com VMs do Linux, veja [Criar chaves SSH para VMs do Linux no Azure](mac-create-ssh-keys.md).

Para saber mais sobre como usar o SSH do Windows, veja [Como usar chaves SSH com o Windows](ssh-from-windows.md).

