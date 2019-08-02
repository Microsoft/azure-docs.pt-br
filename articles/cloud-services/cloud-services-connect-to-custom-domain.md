---
title: Conectar um Serviço de Nuvem em um Controlador de Domínio personalizado | Microsoft Docs
description: Saiba como conectar suas funções web/de trabalho a um domínio do AD personalizado usando o PowerShell e a extensão de domínio do AD
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 97a24720e65539a68745a5a1bb3f13ce1cafb9be
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359175"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Conectando funções dos Serviços de Nuvem do Azure a um controlador de domínio do AD personalizado hospedado no Azure
Primeiro iremos definir a uma Rede Virtual (VNet) no Azure. Em seguida, adicionaremos um Controlador de Domínio do Active Directory (hospedado em uma Máquina Virtual do Azure) à VNet. Em seguida, adicionaremos funções de serviço de nuvem existentes à VNet pré-criada e as conectaremos ao controlador de domínio.

Antes de começar, algumas das coisas que você precisa ter em mente:

1. Este tutorial usa o PowerShell; portanto, certifique-se de que o Azure PowerShell esteja instalado e pronto. Para obter ajuda na configuração do Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
2. As instâncias do controlador de domínio do AD e a função web/de trabalho precisam estar na VNet.

Siga este guia passo a passo e, se tiver algum problema, deixe um comentário ao final do artigo. Alguém entrará em contato com você (sim, lemos comentários).

A rede que é referenciada pelo serviço de nuvem deve ser uma **rede virtual clássica**.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Você pode criar uma Rede Virtual no Azure usando o Portal do Azure ou o PowerShell. Para este tutorial, será usado o PowerShell. Para criar uma rede virtual usando o Portal do Azure, confira [Criar uma rede virtual](../virtual-network/quick-create-portal.md). O artigo aborda a criação de uma rede virtual (Gerenciador de Recursos), mas você deve criar uma rede virtual (clássica) para serviços de nuvem. Para fazer isso, no portal, selecione **Criar um recurso**, digite *rede virtual* na caixa **Pesquisa** e, em seguida, pressione **Enter**. Nos resultados da pesquisa, em **Tudo**, selecione **Rede virtual**. Em **Selecionar um modelo de implantação**, selecione **Clássico** e, em seguida, **Criar**. Você pode seguir as etapas do artigo.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual
Depois de ter concluído a configuração da rede virtual, você precisará criar um controlador de domínio do AD. Para este tutorial, vamos configurar um controlador de domínio do AD em uma máquina virtual do Azure.

Para fazer isso, crie uma máquina virtual por meio do PowerShell usando os comandos a seguir:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promova a máquina virtual a um controlador de domínio
Para configurar a máquina virtual como um controlador de domínio do AD, você precisará fazer logon na VM e configurá-la.

Para fazer logon na VM, você pode obter o arquivo RDP por meio do PowerShell. Use os comandos a seguir:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Depois de fazer logon na VM, configure sua Máquina virtual como um Controlador de domínio do AD, seguindo o guia passo a passo [Como configurar o Controlador de domínio do AD do cliente](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicionar seu Serviço de Nuvem à Rede Virtual
Em seguida, você precisa adicionar a implantação do serviço de nuvem à nova VNet. Para fazer isso, modifique seu cscfg do serviço de nuvem adicionando as seções relevantes ao seu cscfg usando o Visual Studio ou o editor de sua escolha.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Em seguida, compile o projeto de serviços de nuvem e implante-o no Azure. Para obter ajuda com a implantação do pacote de serviços de nuvem no Azure, consulte [Como criar e implantar um Serviço de Nuvem](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Conectar suas funções Web/de trabalho ao domínio
Quando seu projeto de serviço de nuvem for implantado no Azure, conecte suas instâncias de função ao domínio do AD personalizado usando a extensão de domínio do AD. Para adicionar a Extensão de Domínio do AD à sua implantação de serviços de nuvem existente e ingressar no domínio personalizado, execute os seguintes comandos do PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

E isso é tudo.

Os serviços de nuvem devem ser ingressados no seu controlador de domínio personalizado. Se você gostaria de saber mais sobre as diferentes opções disponíveis para configurar a extensão do domínio do AD, use a Ajuda do PowerShell. Aqui estão alguns exemplos:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
