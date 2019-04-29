---
title: Preparar a imagem de VM do Azure para uso com a cloud-init | Microsoft Docs
description: Como preparar uma imagem de VM do Azure já existente para implantação com cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/27/2019
ms.author: danis
ms.openlocfilehash: da539a5bebc1613115f89a7b47c513ce486b5e3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627921"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Preparar uma imagem de VM do Azure do Linux para uso com cloud-init
Este artigo mostra como usar uma máquina virtual do Azure existente e prepará-la para ser reimplantada e estar pronta para usar cloud-init. A imagem resultante pode ser usada para implantar uma nova máquina virtual ou conjuntos de dimensionamento de máquinas virtuais, que podem ser ainda mais personalizados pela cloud-init no tempo de implantação.  Esses scripts de cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações de como o cloud-init funciona nativamente no Azure e as distribuições do Linux compatíveis, consulte [Visão geral de cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Pré-requisitos
Este documento assume que você já tem uma máquina virtual do Azure em execução executando uma versão compatível do sistema operacional Linux. Você já configurou o computador para atender às suas necessidades, instalou todos os módulos necessários, processou todas as atualizações necessárias e o testou para garantir que ele atende aos seus requisitos. 

## <a name="preparing-rhel-76--centos-76"></a>Preparando o RHEL 7.6 / CentOS 7.6
Você precisa se conectar por SSH à sua VM Linux e executar os seguintes comandos para instalar a cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Atualize a seção `cloud_init_modules` em `/etc/cloud/cloud.cfg` para incluir os seguintes módulos:
```bash
- disk_setup
- mounts
```

Aqui está um exemplo da aparência de uma seção `cloud_init_modules` de uso geral.
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
Várias tarefas relacionadas ao provisionamento e tratamento de discos efêmeros precisam ser atualizadas em `/etc/waagent.conf`. Execute os seguintes comandos para atualizar as configurações apropriadas. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cp /lib/systemd/system/waagent.service /etc/systemd/system/waagent.service
sed -i 's/After=network-online.target/WantedBy=cloud-init.service\\nAfter=network.service systemd-networkd-wait-online.service/g' /etc/systemd/system/waagent.service
systemctl daemon-reload
cloud-init clean
```
Permita apenas o Azure como uma fonte de dados para o Agente Linux do Azure criando um novo arquivo `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` usando um editor de sua preferência com as seguintes linhas:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
datasource:
   Azure:
     agent_command: [systemctl, start, waagent, --no-block]
```

Se a imagem do Azure existente tiver um arquivo de permuta configurado e você desejar alterar a configuração do arquivo de permuta para novas imagens usando a cloud-init, será necessário remover o arquivo de permuta existente.

Para imagens baseadas no Red Hat - siga as instruções no seguinte documento da Red Hat explicando como [remover o arquivo de troca](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Para imagens CentOS com o arquivo de permuta habilitado, você pode executar o seguinte comando para desativar o arquivo de permuta:
```bash
sudo swapoff /mnt/resource/swapfile
```

Certifique-se de que a referência do arquivo de permuta seja removida de `/etc/fstab`, ele deve ser semelhante à seguinte saída:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para economizar espaço e remover o arquivo de permuta, você pode executar o comando a seguir:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Etapa extra para imagem preparada de cloud-init
> [!NOTE]
> Se sua imagem era anteriormente uma imagem configurada e preparada de **cloud-init**, é necessário executar as etapas a seguir.

Os três comandos a seguir serão usados somente se a VM que está sendo personalizada para ser uma nova imagem de origem especializada tiver sido provisionada anteriormente por cloud-init.  Você NÃO precisará executá-los se sua imagem tiver sido configurada usando o Agente Linux do Azure.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizando a configuração do Agente Linux 
Todas as imagens de plataforma do Azure têm o Agente Linux do Azure instalado, independentemente se elas foram configuradas por cloud-init ou não.  Execute o seguinte comando para concluir o desprovisionamento de usuário do computador Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para obter mais informações sobre os comandos de desprovisionamento do Agente Linux do Azure, consulte [Agente Linux do Azure](../extensions/agent-linux.md) para obter mais detalhes.

Saia da sessão SSH, em seguida, do shell bash, execute os seguintes comandos de AzureCLI para desalocar, generalizar e criar uma nova imagem de VM do Azure.  Substitua `myResourceGroup` e `sourceVmName` pelas informações adequadas refletindo sua sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos adicionais de alterações de configuração do cloud-init, consulte o seguinte:
 
- [Adicionar um usuário do Linux adicional a uma VM](cloudinit-add-user.md)
- [Run a package manager to update existing packages on first boot](cloudinit-update-vm.md) (Executar um gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização)
- [Change VM local hostname](cloudinit-update-vm-hostname.md) (Alterar o nome do host local da VM) 
- [Install an application package, update configuration files and inject keys](tutorial-automate-vm-deployment.md) (Instalar um pacote de aplicativo, atualizar os arquivos de configuração e injetar chaves)
