---
title: "Usando o DNS interno para a resolução de nomes da VM no Azure | Microsoft Docs"
description: "Usando o DNS interno para a resolução de nomes da VM no Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: bfba2cf38a0624e8480a32bf153f391d820da5a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Usando o DNS interno para a resolução de nomes da VM no Azure

Este artigo mostra como definir nomes DNS internos estáticos para VMs Linux usando VNic (Placas NIC virtuais) e nomes de rótulo DNS. Nomes DNS estáticos são usados para serviços de infraestrutura permanentes como um servidor de build Jenkins, que é usado para este documento ou um servidor Git.

Esses requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="quick-commands"></a>Comandos rápidos

Se você precisar executar a tarefa rapidamente, a seção a seguir fornecerá detalhes dos comandos necessários. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](#detailed-walkthrough).  

Pré-requisitos: Grupo de Recursos, VNet, NSG com SSH de entrada e Sub-rede.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Criar uma VNic com um nome DNS interno estático

O sinalizador `-r` da CLI serve para definir o rótulo DNS, que fornece o nome DNS estático para a VNic.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Implantar a VM na VNet, no NSG e conectar à VNic

O `-N` conecta a VNic à nova VM durante a implantação no Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Passo a passo detalhado

Uma infraestrutura completa de CiCd (implantação e integração contínuas) no Azure exige que alguns servidores sejam estáticos ou de longa duração.  É recomendável que os ativos do Azure, como as VNets (Redes Virtuais) e os NSGs (Grupos de Segurança de Rede), sejam recursos estáticos e de longa duração que raramente são implantados.  Após a implantação de uma VNET, ela pode ser reutilizada por novas implantações sem nenhum efeito negativo sobre a infraestrutura.  A adição de um servidor de repositório Git e um servidor de automação Jenkins a essa rede estática oferece CiCd para os ambientes de desenvolvimento ou teste.  

Nomes DNS internos podem ser resolvidos apenas em uma rede virtual do Azure.  Como os nomes DNS são internos, eles não podem ser resolvidos para a Internet externa, fornecendo segurança adicional para a infraestrutura.

_Substitua os exemplos por sua própria nomenclatura._

## <a name="create-the-resource-group"></a>Criar o Grupo de recursos

Um Grupo de Recursos é necessário para organizar tudo o que criamos neste passo a passo.  Para obter mais informações sobre os Grupos de Recursos do Azure, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Criar a VNET

A primeira etapa é criar uma VNET na qual as VMs serão iniciadas.  A VNET contém uma sub-rede para este passo a passo.  Para obter mais informações sobre as VNETs do Azure, consulte [Criar uma rede virtual usando a CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Criar o NSG

A Sub-rede é criada por trás de um Grupo de Segurança de Rede existente e, portanto, criamos o NSG antes da Sub-rede.  Os NSGs do Azure são equivalentes a um firewall na camada de rede.  Para obter mais informações sobre os NSGs do Azure, consulte [Como criar NSGs na CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Adicionar uma regra de permissão de SSH de entrada

A VM do Linux precisa de acesso da Internet e, portanto, é necessária uma regra permitindo que o tráfego da porta 22 de entrada passe pela rede para a porta 22 na VM do Linux.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Adicionar uma sub-rede à VNET

As VMs na VNET devem estar localizadas em uma sub-rede.  Cada VNET pode ter várias sub-redes.  Crie a sub-rede e associe-a ao NSG para adicionar um firewall à sub-rede.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Agora, a Sub-rede é adicionada à VNET e associada ao NSG e à regra do NSG.

## <a name="creating-static-dns-names"></a>Criando nomes DNS estáticos

O Azure é muito flexível, mas para usar nomes DNS para a resolução de nomes de VMs, você precisa criá-los como VNics (Placas de rede virtual) usando a rotulagem DNS.  As VNics são importantes, pois você pode reutilizá-las conectando-as a VMs diferentes, o que mantém a VNic como um recurso estático, enquanto as VMs podem ser temporárias.  Ao usar a rotulagem DNS na VNic, podemos habilitar a resolução de nomes simples em outras VMs na VNet.  Usar nomes que podem ser resolvidos permite que outras VMs acessem o servidor de automação com o nome DNS `Jenkins` ou o servidor Git como `gitrepo`.  Crie uma VNic e associe-a à Sub-rede criada na etapa anterior.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Implantar a VM na VNET e no NSG

Agora temos uma VNET, uma sub-rede nela e um NSG atuando como um firewall, para proteger nossa sub-rede bloqueando todo o tráfego de entrada, exceto pela porta 22 para o SSH.  Agora a VM pode ser implantada nessa infraestrutura de rede existente.

Usando a CLI do Azure e o comando `azure vm create`, a VM do Linux é implantada no Grupo de Recursos do Azure, na VNET, na Sub-rede e na VNic existentes.  Para obter mais informações sobre como usar a CLI para implantar uma VM completa, consulte [Criar um ambiente completo do Linux usando a CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Ao usar sinalizadores da CLI para chamar os recursos existentes, instruímos o Azure a implantar a VM na rede existente.  Em outras palavras, depois que uma VNET e uma sub-rede forem implantadas, elas poderão ser mantidas como recursos estáticos ou permanentes na região do Azure.  

## <a name="next-steps"></a>Próximas etapas
* [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
