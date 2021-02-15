---
title: Criar VLANs/sub-redes-solução do Azure VMware por CloudSimple
description: Soluções do Azure VMware por CloudSimple – descreve como criar e gerenciar VLANs/sub-redes para suas nuvens privadas e, em seguida, aplicar regras de firewall.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06bebcb7369f6604fc79c1d3d0a4a6afa8b0a1da
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896304"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>Criar e gerenciar VLANs/sub-redes para suas nuvens privadas

Abra a guia VLANs/sub-redes na página rede para criar e gerenciar VLANs/sub-redes para suas nuvens privadas. Depois de criar uma VLAN/sub-rede, você pode aplicar regras de firewall.

## <a name="create-a-vlansubnet"></a>Criar uma VLAN/sub-rede

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md) e selecione **rede** no menu lateral.
2. Selecione **VLANs/sub-redes**.
3. Clique em **criar VLAN/sub-rede**.

    ![Página VLAN/sub-rede](media/vlan-subnet-page.png)

4. Selecione a nuvem privada para a nova VLAN/sub-rede.
5. Insira uma ID de VLAN.
6. Insira o nome da sub-rede.
7. Para habilitar o roteamento na VLAN (sub-rede), especifique o intervalo de CIDR da sub-rede. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-rede de gateway.
8. Clique em **Enviar**.

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> Há uma cota de 30 VLANs por nuvem privada. Esses limites podem ser aumentados [entrando em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>Usar informações de VLAN para configurar um grupo de portas distribuídas no vSphere

Para criar um grupo de portas distribuídas no vSphere, siga as instruções no tópico "adicionar um grupo de portas distribuídas" do VMware no <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">Guia de rede do vSphere</a>. Ao configurar o grupo de portas distribuídas, forneça as informações de VLAN da configuração CloudSimple.

![Grupo de portas distribuídas](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>Selecionar uma tabela de firewall

As tabelas de firewall e as regras associadas são definidas na página **tabelas de firewall > de rede** . Para selecionar a tabela de firewall a ser aplicada à VLAN/sub-rede para uma nuvem privada, selecione a VLAN/sub-rede clique em **anexo de tabela de firewall** na página **VLANs/sub-redes** . Consulte [tabelas de firewall](firewall.md) para obter instruções sobre como configurar tabelas de firewall e definir regras.

![Link de tabela de firewall](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> Uma sub-rede pode ser associada a uma tabela de firewall. Uma tabela de firewall pode ser associada a várias sub-redes.

## <a name="edit-a-vlansubnet"></a>Editar uma VLAN/sub-rede

Para editar as configurações de uma VLAN/sub-rede, selecione-a na página **VLANs/sub-redes** e clique no ícone **Editar** . Faça alterações e clique em **submet**.

## <a name="delete-a-vlansubnet"></a>Excluir uma VLAN/sub-rede

Para excluir uma VLAN/sub-rede, selecione-a na página **VLANs/sub-redes** e clique no ícone **excluir** . Clique em **excluir** para confirmar.
