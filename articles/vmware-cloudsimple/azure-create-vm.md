---
title: Solução do Azure VMware por CloudSimple-criar uma máquina virtual no Azure com modelos de VM
description: Descreve como criar máquinas virtuais no Azure usando modelos de VM na infraestrutura do VMware para sua nuvem privada do CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898752"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Criar uma máquina virtual no Azure usando modelos de VM na infra-estrutura do VMware

Você pode criar uma máquina virtual no portal do Azure usando modelos de VM na infra-estrutura do VMware que o administrador do CloudSimple habilitou para sua assinatura.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Criar máquina virtual CloudSimple

1. Selecione **Todos os serviços**.

2. Pesquise **Máquinas Virtuais do CloudSimple**.

3. Clique em **Adicionar**.

    ![Criar máquina virtual CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Inserir informações básicas clique em **Avançar: tamanho**.

    > [!NOTE]
    > A criação de máquina virtual CloudSimple no Azure requer um modelo de VM.  Esse modelo de VM deve existir em seu vCenter de nuvem privada.  Crie uma máquina virtual em sua nuvem privada da interface do usuário do vCenter com o sistema operacional e as configurações desejadas.  Usando as instruções em [clonar uma máquina virtual para um modelo no cliente Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), crie um modelo.

    ![Criar máquina virtual CloudSimple-noções básicas](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Campo | Descrição |
    | ------------ | ------------- |
    | Subscription | Assinatura do Azure associada à sua nuvem privada.  |
    | Grupo de recursos | Grupo de recursos ao qual a VM será atribuída. Selecione um grupo existente ou crie um novo. |
    | Nome | Nome para identificar a VM.  |
    | Location | Região do Azure na qual essa VM está hospedada.  |
    | Nuvem privada | CloudSimple nuvem privada em que você deseja criar a máquina virtual. |
    | Pool de recursos | Pool de recursos mapeados para a VM. Selecione entre os pools de recursos disponíveis. |
    | Modelo do vSphere | modelo de vSphere para a VM.  |
    | Nome de usuário | Nome de usuário do administrador da VM (para modelos do Windows)|
    | Senha <br>Confirmar senha | Senha para o administrador da VM (para modelos do Windows).  |

5. Selecione o número de núcleos e a capacidade de memória para a VM e clique em **Avançar: configurações**. Marque a caixa de seleção se você quiser expor a virtualização de CPU completa para o sistema operacional convidado para que os aplicativos que exigem a virtualização de hardware possam ser executados em máquinas virtuais sem conversão binária ou paravirtualização. Saiba mais no artigo da VMware [Expor virtualização assistida de hardware do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Criar máquina virtual CloudSimple-tamanho](media/create-cloudsimple-virtual-machine-size.png)

6. Configure os discos e os adaptadores de rede conforme descrito nas tabelas a seguir e clique em **revisar + criar**.

    ![Criar máquina virtual CloudSimple-configurações](media/create-cloudsimple-virtual-machine-configurations.png)

    Para interfaces de rede, clique em **Adicionar interface de rede** e defina as configurações a seguir.

    | Control | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o adaptador.  |
    | Rede | Selecione na lista de grupos de portas distribuídas configuradas em sua nuvem privada vSphere.  |
    | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware [escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
    | Ligar na inicialização | Escolha se quer habilitar o hardware da NIC quando a VM for inicializada. O padrão é **Habilitar**. |

    Para discos, clique em **adicionar disco** e defina as configurações a seguir.

    | Item | Descrição |
    | ------------ | ------------- |
    | Nome | Insira um nome para identificar o disco.  |
    | Tamanho | Selecione um dos tamanhos disponíveis.  |
    | Controlador SCSI | Selecione um controlador SCSI para o disco.  |
    | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> – Não persistente independente: as alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual.  O modo independente não persistente permite que você sempre reinicie a máquina virtual no mesmo estado. Saiba mais na [documentação da VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Após a conclusão da validação, examine as configurações e clique em **criar**. Para fazer alterações, clique nas guias na parte superior ou clique em.

    ![Criar máquina virtual CloudSimple-examinar](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Exibir lista de máquinas virtuais do CloudSimple

1. Selecione **Todos os serviços**.

2. Pesquise **Máquinas Virtuais do CloudSimple**.

3. Selecione o em que sua nuvem privada foi criada.

    ![Lista de máquinas virtuais CloudSimple](media/list-cloudsimple-virtual-machines.png)

A lista de máquinas virtuais CloudSimple inclui máquinas virtuais criadas a partir de portal do Azure.  As máquinas virtuais criadas no vCenter de nuvem privada no pool de recursos do vCenter mapeado serão mostradas na lista.  
