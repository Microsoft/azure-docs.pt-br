---
title: Perguntas Frequentes sobre máquinas virtuais do Windows no Azure | Microsoft Docs
description: Fornece respostas para algumas das perguntas mais comuns sobre as máquinas virtuais do Windows criadas com o modelo do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: 65d4326763ef9754159e94c9426f3aee69f80ffd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095580"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre as Máquinas Virtuais do Windows
Este artigo trata de algumas das perguntas mais comuns sobre as máquinas virtuais do Windows criadas no Azure com o modelo de implantação do Gerenciador de Recursos. Para a versão deste tópico relativa ao Linux, veja [Perguntas frequentes sobre as Máquinas Virtuais Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que eu posso executar em uma VM do Azure?
Todos os assinantes podem executar software para servidores em uma máquina virtual do Azure. Para saber mais sobre a política de suporte para software de servidor Microsoft em execução no Azure, veja [Suporte a software de servidor Microsoft para Máquinas Virtuais do Azure](https://support.microsoft.com/kb/2721672)

Algumas versões do Windows 7, Windows 8.1 e Windows 10 estão disponíveis para assinantes dos benefícios do Azure no MSDN e para assinantes do modelo “pré-pago” de Desenvolvimento e Teste no MSDN, para tarefas de desenvolvimento e teste. Para obter detalhes, incluindo instruções e limitações, veja [Imagens do Windows Client para assinantes do MSDN](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento eu posso usar com uma máquina virtual?
Cada disco de dados pode ter até 4 TB (4.095 GB). O número de discos de dados que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Os Azure Managed Disks são as ofertas de armazenamento em disco recomendadas para uso com Máquinas Virtuais do Azure para armazenamento persistente de dados. Em cada Máquina Virtual, é possível usar vários Managed Disks. Os Managed Disks oferecem dois tipos de opções de armazenamento durável: Discos Gerenciados Premium e Standard. Para obter informações sobre preço, consulte [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

As contas de armazenamento do Azure também podem fornecer o armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um arquivo .vhd armazenado como um blob de páginas. Para obter detalhes sobre preços, veja [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Estabeleça uma conexão remota usando o protocolo RDP (Conexão de Área de Trabalho Remota) para uma VM do Windows. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Há suporte para o máximo de duas conexões simultâneas, a menos que o servidor esteja configurado como um host de sessão dos Serviços de Área de Trabalho Remota.  

Se você estiver tendo problemas com a Área de Trabalho Remota, veja [Solucionar problemas de conexões da Área de Trabalho Remota para uma Máquina Virtual Azure baseada no Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se estiver familiarizado com o Hyper-V, você pode estar em busca de uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque não há suporte para o acesso de console a uma máquina virtual.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Posso usar o disco temporário (a unidade d:, por padrão) para armazenar dados?
Não use o disco temporário para armazenar dados. Ele é apenas um armazenamento temporário, você se arriscaria a perder dados que não podem ser recuperados. Pode ocorrer perda de dados quando a máquina virtual é movida para um host diferente. O redimensionamento de uma máquina virtual, atualização do host ou uma falha de hardware no host são algumas das razões que podem levar à mudança de uma máquina virtual.

Se tiver um aplicativo que precise usar a letra da unidade D:, você poderá reatribuir as letras da unidade para que o disco temporário use algo diferente de D:. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como eu posso alterar a letra da unidade do disco temporário?
Você pode alterar a letra da unidade movendo o arquivo de paginação e reatribuindo as letras da unidade, mas precisa se lembrar de seguir as etapas em uma ordem específica. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Posso adicionar uma VM existente a um conjunto de disponibilidade?
Não. Se você quiser que sua VM faça parte de um conjunto de disponibilidade, precisará criar a VM no conjunto. Atualmente, não há uma maneira de adicionar uma VM a um conjunto de disponibilidade após ela ter sido criada.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Posso carregar uma máquina virtual no Azure?
Sim. Para obter instruções, consulte [Migrando VMs locais para o Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Posso redimensionar o disco do sistema operacional?
Sim. Para obter instruções, veja [Como expandir a unidade do sistema operacional de uma Máquina Virtual em um Grupo de Recursos do Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar uma VM do Azure existente?
Sim. Usando imagens gerenciadas, você pode criar uma imagem de uma máquina virtual e, em seguida, usar a imagem para criar várias novas VMs. Para obter instruções, consulte [Criar uma imagem personalizada de uma VM](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que não vejo as regiões Central e Leste do Canadá por meio do Azure Resource Manager?

As duas novas regiões, Central do Canadá e Leste do Canadá, não são registradas automaticamente para a criação de máquinas virtuais para as assinaturas existentes do Azure. Esse registro é feito automaticamente quando uma máquina virtual é implantada por meio do Portal do Azure para qualquer outra região usando o Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>O Azure oferece suporte a VMs Linux?
Sim. Para criar rapidamente uma VM Linux para experimentá-la, veja [Criar uma VM Linux no Azure usando o Portal](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC à minha VM depois que ela é criada?
Sim, agora isso é possível. Primeiro, a VM precisa ser parada e desalocada. Em seguida, é possível adicionar ou remover uma NIC (a menos que ela seja a última NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome do computador?
Sim. O nome do computador pode ter, no máximo, 15 caracteres. Confira [Regras e restrições de convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions#compute) para obter mais informações sobre como nomear recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Há algum requisito de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter, no máximo, 90 caracteres. Confira [Regras e restrições de convenções de nomenclatura](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de usuário ao criar uma VM?

Nomes de usuário podem ter, no máximo, 20 caracteres e não podem terminar com um ponto (“.”). 


Os seguintes nomes de usuário não são permitidos:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">adm</td><td style="text-align:center">administrador</td><td style="text-align:center">admin1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">administrator</td><td style="text-align:center">aspnet</td><td style="text-align:center">backup</td><td style="text-align:center">console</td>
    </tr>
    <tr>
        <td style="text-align:center">david </td><td style="text-align:center">guest</td><td style="text-align:center">julio</td><td style="text-align:center">proprietário</td>
    </tr>
    <tr>
        <td style="text-align:center">root</td><td style="text-align:center">Servidor</td><td style="text-align:center">sql</td><td style="text-align:center">suporte</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">sys</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">test2</td><td style="text-align:center">test3</td><td style="text-align:center">usuário</td><td style="text-align:center">user1</td>
    </tr>
    <tr>
        <td style="text-align:center">user2</td><td style="text-align:center">user3</td><td style="text-align:center">user4</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar uma VM?
As senhas devem ter de 12 a 123 caracteres e atender a três dos quatro requisitos de complexidade a seguir:

* Ter caracteres minúsculos
* Tem caracteres maiúsculos
* Tem um dígito
* Tem um caractere especial (Correspondência de regex [\W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Password!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
