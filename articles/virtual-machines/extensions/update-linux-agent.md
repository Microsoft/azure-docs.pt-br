---
title: Atualizar o Agente Linux do Azure por meio do GitHub | Microsoft Docs
description: Saiba como atualizar o Agente Linux do Microsoft Azure para sua VM do Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: roiyz
ms.openlocfilehash: 5d53f34ea6b0983d0687cdaf6ec6271c703bb055
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799750"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Como atualizar o Agente Linux do Azure em uma VM

Para atualizar seu [agente Linux do Azure](https://github.com/Azure/WALinuxAgent) em uma VM do Linux no Azure, você já deve ter:

- uma VM do Linux em execução no Azure.
- uma conexão com essa VM do Linux usando o SSH.

Primeiro, você sempre deve verificar um pacote no repositório de distribuição de Linux. É possível que o pacote disponível não seja a versão mais recente, no entanto, habilitar a atualização automática garantirá que o Agente do Linux sempre obterá a atualização mais recente. Se você tiver problemas de instalação a partir dos gerenciadores de pacotes, procure o suporte do fornecedor de distribuição.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Suporte mínimo do agente de máquina virtual no Azure
Verifique o [Suporte mínimo da versão para agentes de máquina virtual no Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) antes de continuar.

## <a name="updating-the-azure-linux-agent"></a>Atualizar o Agente Linux do Azure

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada

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

### <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent

#### <a name="restart-agent-for-1404"></a>Reinicie o agente para 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Reinicie o agente para 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Habilitar atualização automática do agente
Esta versão do Debian não tem uma versão maior ou igual à 2.0.16, portanto, a Atualização automática não está disponível para ele. A saída do comando acima mostrará se o pacote está atualizado.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Cache do pacote de atualização

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

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

### <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

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

### <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

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

### <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

A saída acima mostrará se o pacote está atualizado.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

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

### <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Verificar a versão atual do pacote

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Verificar as atualizações disponíveis

A saída acima mostrará se o pacote está atualizado.

#### <a name="install-the-latest-package-version"></a>Instalar a versão mais recente do pacote

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada 

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

### <a name="restart-the-waagent-service"></a>Reinicie o serviço do waagent

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 e 7

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

#### <a name="for-version-22x-or-later-type"></a>Para a versão 2.2. x ou posterior, digite:
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

### <a name="2-install-the-azure-linux-agent"></a>2. Instale o Agente Linux do Azure

#### <a name="for-version-22x-use"></a>Para a versão 2.2. x, use:
Talvez seja necessário instalar o pacote `setuptools` primeiro, veja [aqui](https://pypi.python.org/pypi/setuptools). Em seguida, execute:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Verificar se a atualização automática está habilitada

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

### <a name="3-restart-the-waagent-service"></a>3. Reinicie o serviço do waagent
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

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Confirme a versão do Agente Linux do Azure
    
```bash
waagent -version
```

Para o CoreOS, o comando acima pode não funcionar.

Você verá que a versão do Agente Linux do Azure foi atualizada para a nova versão.

Para obter mais informações sobre o Agente Linux do Azure, consulte [Azure Linux Agent README](https://github.com/Azure/WALinuxAgent)(LEIAME do Agente Linux do Azure).
