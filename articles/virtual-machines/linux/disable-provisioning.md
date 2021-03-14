---
title: Desabilitar ou remover o agente de provisionamento
description: Saiba como desabilitar ou remover o agente de provisionamento em imagens e VMs do Linux.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 7c797957c292b9859ca41951b15f58c3d0be40b2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561051"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Desabilitar ou remover o agente do Linux de VMs e imagens

Antes de remover o agente do Linux, você deve entender o que a VM não poderá fazer depois que o agente do Linux for removido.

[As extensões](../extensions/overview.md) de VM (máquina virtual) do Azure são aplicativos pequenos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure, as extensões são instaladas e gerenciadas pelo plano de controle do Azure. É o trabalho do [agente Linux do Azure](../extensions/agent-linux.md) processar os comandos de extensão de plataforma e garantir o estado correto da extensão dentro da VM.

A plataforma do Azure hospeda várias extensões que variam de configuração de VM, monitoramento, segurança e aplicativos de utilidade. Há uma grande opção de extensões de primeira e de terceiros, exemplos de principais cenários para os quais as extensões são usadas:
* Suporte a serviços do Azure de primeira parte, como o backup do Azure, monitoramento, criptografia de disco, segurança, replicação de site e outros.
* SSH/redefinições de senha
* Configuração da VM-executando scripts personalizados, instalando o chefe, agentes Puppet etc..
* Produtos de terceiros, como produtos AV, ferramentas de vulnerabilidade de VM, VM e ferramenta de monitoramento de aplicativo.
* As extensões podem ser incluídas com uma nova implantação de VM. Por exemplo, podem fazer parte de uma implantação maior, configuração de aplicativos no provisionamento de VM, ou executar em qualquer pós-implantação de sistemas operado por  extensão com suporte.

## <a name="disabling-extension-processing"></a>Desabilitando o processamento de extensão

Há várias maneiras de desabilitar o processamento de extensão, dependendo de suas necessidades, mas antes de continuar, você **deve** remover todas as extensões implantadas na VM, por exemplo, usando o CLI do Azure, você pode [listar](/cli/azure/vm/extension#az-vm-extension-list) e [excluir](/cli/azure/vm/extension#az-vm-extension-delete):

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Se você não fizer isso, a plataforma tentará enviar a configuração de extensão e o tempo limite após 40min.

### <a name="disable-at-the-control-plane"></a>Desabilitar no plano de controle
Se você não tiver certeza se precisará de extensões no futuro, poderá deixar o agente do Linux instalado na VM e desabilitar a capacidade de processamento da extensão da plataforma. Essa opção está disponível na `Microsoft.Compute` versão da API `2018-06-01` ou superior e não tem uma dependência na versão do agente do Linux instalada.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Você pode reabilitar facilmente esse processamento de extensão da plataforma, com o comando acima, mas defini-lo como ' true '.

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Remover o agente do Linux de uma VM em execução

Verifique se você **removeu** todas as extensões existentes da VM antes, conforme descrito acima.

### <a name="step-1-remove-the-azure-linux-agent"></a>Etapa 1: remover o agente Linux do Azure

Se você apenas remover o agente do Linux e não os artefatos de configuração associados, poderá reinstalar o em uma data posterior. Execute uma das seguintes opções, como raiz, para remover o agente Linux do Azure:

#### <a name="for-ubuntu-1804"></a>Para o Ubuntu >= 18, 4
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Para redhat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Para SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Etapa 2: (opcional) remover os artefatos do agente Linux do Azure
> [!IMPORTANT] 
>
> Você pode remover todos os artefatos associados do agente do Linux, mas isso significa que você não poderá reinstalá-lo em uma data posterior. Portanto, é altamente recomendável que você considere desabilitar o agente do Linux primeiro, removendo o agente do Linux usando apenas o. 

Se você souber que não reinstalará o agente do Linux novamente, será possível executar o seguinte:

#### <a name="for-ubuntu-1804"></a>Para o Ubuntu >= 18, 4
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Para redhat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Para SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Preparando uma imagem sem o agente do Linux
Se você tiver uma imagem que já contém Cloud-init e quiser remover o agente do Linux, mas ainda provisionar usando Cloud-init, execute as etapas na etapa 2 (e, opcionalmente, a etapa 3) como raiz para remover o agente Linux do Azure e, em seguida, o seguinte removerá a configuração de Cloud-init e os dados armazenados em cache, e preparar a VM

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Desprovisionar e criar uma imagem
O agente do Linux tem a capacidade de limpar alguns dos metadados de imagem existentes, com a etapa "waagent-deprovision + User", no entanto, depois que ele tiver sido removido, será necessário executar ações como a abaixo e remover quaisquer outros dados confidenciais dela.

- Remover todas as chaves de host SSH existentes

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Excluir a conta do administrador

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Excluir a senha raiz

   ```bash
   passwd -d root
   ```

Depois de concluir o acima, você poderá criar a imagem personalizada usando o CLI do Azure.


**Criar uma imagem gerenciada regular**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Criar uma versão de imagem em uma galeria de imagens compartilhadas**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Criando uma VM com base em uma imagem que não contém um agente do Linux
Ao criar a VM a partir da imagem sem nenhum agente Linux, você precisa garantir que a configuração de implantação da VM indique que as extensões não têm suporte nesta VM.

> [!NOTE] 
> 
> Se você não fizer isso, a plataforma tentará enviar a configuração de extensão e o tempo limite após 40min.

Para implantar a VM com extensões desabilitadas, você pode usar o CLI do Azure com [--Enable-Agent](/cli/azure/vm#az-vm-create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Como alternativa, você pode fazer isso usando modelos de Azure Resource Manager (ARM), definindo `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Provisionando o Linux](provisioning.md).
