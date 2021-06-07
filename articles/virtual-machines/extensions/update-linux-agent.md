---
title: Atualizar o agente Linux do Azure do GitHub
description: Saiba como atualizar o Agente Linux do Microsoft Azure para sua VM do Linux no Azure
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 08/02/2017
ms.openlocfilehash: a274435d6e0fc32bdf5b2ab04702ed971d7c5175
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558977"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o Agente Linux do Azure em uma VM

Para atualizar seu [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) em uma VM do Linux no Azure, você já deve ter:

- uma VM do Linux em execução no Azure.
- uma conexão com essa VM do Linux usando o SSH.

Primeiro, você sempre deve verificar um pacote no repositório de distribuição de Linux. É possível que o pacote disponível não seja a versão mais recente, no entanto, habilitar a atualização automática garantirá que o Agente do Linux sempre obterá a atualização mais recente. Se você tiver problemas de instalação a partir dos gerenciadores de pacotes, procure o suporte do fornecedor de distribuição.

> [!NOTE]
> Para obter mais informações, consulte [distribuições do Linux endossadas no Azure](../linux/endorsed-distros.md)

Verifique o [Suporte mínimo da versão para agentes de máquina virtual no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) antes de continuar.


## <a name="ubuntu"></a>Ubuntu

Verificar a versão atual do pacote

```bash
apt list --installed | grep walinuxagent
```

Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

Instalar a versão mais recente do pacote

```bash
sudo apt-get install walinuxagent
```

Verifique se a atualização automática está habilitada. Primeiro, verifique se está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reiniciar o serviço waagengt para 14, 4

```bash
initctl restart walinuxagent
```

Reiniciar o serviço waagent para 16, 4/17, 4

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

Verificar a versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

Verificar as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

Verificar se a atualização automática está habilitada 

Primeiro, verifique se está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço do waagent

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

Verificar a versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

Verificar as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

Verifique se a atualização automática está habilitada. Primeiro, verifique se está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço do waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

Verificar a versão atual do pacote

```bash
zypper info python-azure-agent
```

Verifique as atualizações disponíveis. A saída acima mostrará se o pacote está atualizado.

Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

Verificar se a atualização automática está habilitada 

Primeiro, verifique se está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço do waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

Verificar a versão atual do pacote

```bash
zypper info python-azure-agent
```

Verificar as atualizações disponíveis

A saída acima mostrará se o pacote está atualizado.

Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

Verificar se a atualização automática está habilitada 

Primeiro, verifique se está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

Reinicie o serviço do waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "Stretch"

Verificar a versão atual do pacote

```bash
dpkg -l | grep waagent
```

Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

Instalar a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

Habilitar atualização automática do agente esta versão do Debian não tem uma versão >= 2.0.16, portanto, o AutoUpdate não está disponível para ele. A saída do comando acima mostrará se o pacote está atualizado.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "Stretch"

Verificar a versão atual do pacote

```bash
apt list --installed | grep waagent
```

Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

Instalar a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

Verifique se a atualização automática está habilitada primeiro, verifique se ela está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 e Oracle Linux 7

Para Oracle Linux, verifique se o repositório `Addons` está habilitado. Escolha editar o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** nesse arquivo.

Em seguida, para instalar a versão mais recente do agente Linux do Azure e digite:

```bash
sudo yum install WALinuxAgent
```

Caso você não encontre o repositório de complementos, basta adicionar essas linhas no final do arquivo .repo de acordo com sua versão do Oracle Linux:

Para máquinas virtuais do Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Para máquinas virtuais do Oracle Linux 7:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Em seguida, digite:

```bash
sudo yum update WALinuxAgent
```

Normalmente, isso é tudo de que você precisa. Porém, se por algum motivo for necessário instalá-lo no https://github.com diretamente, use as etapas a seguir.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Atualizar o Agente do Linux quando não existir qualquer pacote de agente para distribuição

Instale o wget (existem algumas distribuições que não o instalam por padrão, como Red Hat, CentOS e Oracle Linux versões 6.4 e 6.5) digitando `sudo yum install wget` na linha de comando.

### <a name="1-download-the-latest-version"></a>1. Baixar a última versão
Abra [a versão do Agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent/releases) em uma página da Web e encontre o número de versão mais recente. (Você pode localizar sua versão atual digitando `waagent --version`.)

Para a versão 2.2. x ou posterior, digite:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

A linha a seguir usa a versão 2.2.0 como exemplo:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. instalar o agente Linux do Azure

Para a versão 2.2. x, use: Talvez seja necessário instalar o pacote `setuptools` primeiro--veja [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

Verifique se a atualização automática está habilitada. Primeiro, verifique se está habilitada:

```bash
cat /etc/waagent.conf
```

Localize 'AutoUpdate.Enabled'. Se você vir esta saída, ela estará habilitada:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Para habilitar a execução:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Reinicie o serviço waagent
Para a maioria das distribuições do Linux:

```bash
sudo service waagent restart
```

Para o Ubuntu, use:

```bash
sudo service walinuxagent restart
```

Para o CoreOS, use:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirme a versão do agente Linux do Azure
    
```bash
waagent -version
```

Para o CoreOS, o comando acima pode não funcionar.

Você verá que a versão do Agente Linux do Azure foi atualizada para a nova versão.

Para obter mais informações sobre o Agente Linux do Azure, consulte [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent)(LEIAME do Agente Linux do Azure).
