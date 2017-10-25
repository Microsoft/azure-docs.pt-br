---
title: "Entender os endereços IP compartilhados no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como o Azure DevTest Labs usa endereços IP compartilhados para minimizar os endereços IP públicos necessários para acessar as VMs de seu laboratório."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
ms.openlocfilehash: 9f6e1980bf5ea5b41da98a135d89f1c5159921a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Entender os endereços IP compartilhados no Azure DevTest Labs

O Azure DevTest Labs permite que VMs de laboratório compartilhem o mesmo endereço IP para minimizar o número de endereços IP públicos necessários para acessar as VMs de seu laboratório individual.  Este artigo descreve como funcionam os IPs compartilhados e suas opções de configuração relacionadas.

## <a name="shared-ip-setting"></a>Configuração de IP de compartilhado

Quando você cria um laboratório, ele reside em uma sub-rede de uma rede virtual.  Por padrão, essa sub-rede é criada com **Habilitar IP público compartilhado** definido como *Sim*.  Essa configuração cria um endereço IP público para toda a sub-rede.  Para obter mais informações sobre como configurar redes virtuais e sub-redes, consulte [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nova sub-rede do laboratório](media/devtest-lab-shared-ip/lab-subnet.png)

Em laboratórios existentes, você pode habilitar essa opção selecionando **Políticas e configurações > Redes Virtuais**. Em seguida, selecione uma rede virtual da lista e escolha **HABILITAR IP PÚBLICO COMPARTILHADO** para uma sub-rede selecionada. Você também pode desabilitar essa opção em qualquer laboratório se você não deseja compartilhar um endereço IP público em VMs do laboratório.

Todas as VMs criadas nesse laboratório assumem como padrão o uso de um IP compartilhado.  Ao criar a VM, essa configuração pode ser observada na folha **Configurações avançadas** em **Configuração do endereço IP**.

![Nova VM](media/devtest-lab-shared-ip/new-vm.png)

- **Compartilhado:** todas as VMs criadas como **Compartilhadas** são colocadas em um grupo de recursos (RG). Um único endereço IP será atribuído para esse RG e todas as VMs no RG usarão esse endereço IP.
- **Público:** cada uma das VMs que você cria tem seu próprio endereço IP e é criada em seu próprio grupo de recursos.
- **Privado:** todas as VMs que você criar usarão um endereço IP privado. Você não poderá se conectar a essas VM diretamente da Internet com a Área de Trabalho Remota.

Sempre que uma VM com IP compartilhado habilitado é adicionada à sub-rede, o DevTest Labs adiciona automaticamente a VM a um balanceador de carga e atribui um número da porta TCP no endereço IP público, encaminhando para a porta RDP na VM.  

## <a name="using-the-shared-ip"></a>Usar o IP compartilhado

- **Usuários do Linux:** conecte-se à VM por SSH usando o endereço IP ou o nome de domínio totalmente qualificado, seguido por dois pontos e depois pela porta. Por exemplo, na imagem abaixo, o endereço RDP para se conectar à VM é `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Exemplo de VM](media/devtest-lab-shared-ip/vm-info.png)

- **Usuários do Windows:** selecione o botão **Conectar** no Portal do Azure para baixar um arquivo RDP pré-configurado e acessar a VM.

## <a name="next-steps"></a>Próximas etapas

* [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md)





