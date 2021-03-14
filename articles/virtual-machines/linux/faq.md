---
title: Perguntas frequentes sobre VMs do Linux no Azure
description: Fornece respostas para algumas das perguntas mais comuns sobre as máquinas virtuais Linux criadas com o modelo do Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 22be45403a7863328c5f6f2c883886296b734914
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549083"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Perguntas frequentes sobre as Máquinas Virtuais Linux
Este artigo trata de algumas das perguntas mais comuns feitas sobre as máquinas virtuais Linux criadas no Azure com o modelo de implantação do Gerenciador de Recursos. Para a versão deste tópico para o Windows, confira [Perguntas frequentes sobre Máquinas Virtuais do Windows](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que eu posso executar em uma VM do Azure?
Todos os assinantes podem executar software para servidores em uma máquina virtual do Azure. Para saber mais, veja [Linux em distribuições endossadas pelo Azure](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento eu posso usar com uma máquina virtual?
Cada disco de dados pode ter até 32.767 GiB. O número de discos de dados que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [tamanhos das máquinas virtuais](../sizes.md).

Os Azure Managed Disks são as ofertas de armazenamento em disco recomendadas para uso com Máquinas Virtuais do Azure para armazenamento persistente de dados. Em cada Máquina Virtual, é possível usar vários Managed Disks. Os Managed Disks oferecem dois tipos de opções de armazenamento durável: Managed Disks Premium e Standard. Para obter informações sobre preço, consulte [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

As contas de armazenamento do Azure também podem fornecer o armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um arquivo .vhd armazenado como um blob de páginas. Para obter detalhes sobre preços, veja [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como posso acessar minha máquina virtual?
Estabeleça uma conexão remota para fazer logon na máquina virtual usando Secure Shell (SSH). Veja as instruções sobre como conectar [do Windows](ssh-from-windows.md) ou [do Linux e do Mac](mac-create-ssh-keys.md). Por padrão, o SSH permite um máximo de 10 conexões simultâneas. Você pode aumentar esse número editando o arquivo de configuração.

Se você estiver tendo problemas, confira [Solucionar problemas de conexões SSH (Secure Shell)](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Posso usar o disco temporário (/dev/sdb1) para armazenar os dados?
Não use o disco temporário (/dev/sdb1) para armazenar os dados. Ele existe somente para armazenamento temporário. Você corre o risco de perder dados que não podem ser recuperados.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Posso copiar ou clonar uma VM do Azure existente?
Sim. Para obter instruções, veja [Como criar uma cópia de uma máquina virtual do Linux no modelo de implantação do Resource Manager](copy-vm.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que não vejo as regiões Central e Leste do Canadá por meio do Azure Resource Manager?
As duas novas regiões, Central do Canadá e Leste do Canadá, não são registradas automaticamente para a criação de máquinas virtuais para as assinaturas existentes do Azure. Esse registro é feito automaticamente quando uma máquina virtual é implantada por meio do Portal do Azure para qualquer outra região usando o Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar um NIC à minha VM depois que ela é criada?
Sim, agora isso é possível. Primeiro, a VM precisa ser parada e desalocada. Em seguida, é possível adicionar ou remover uma NIC (a menos que ela seja a última NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Há algum requisito de nome do computador?
Sim. O nome do computador pode ter, no máximo, 64 caracteres. Confira [Regras e restrições de convenções de nomenclatura](/azure/architecture/best-practices/resource-naming) para obter mais informações sobre como nomear recursos.

## <a name="are-there-any-resource-group-name-requirements"></a>Há algum requisito de nome de grupo de recursos?
Sim. O nome do grupo de recursos pode ter, no máximo, 90 caracteres. Confira [Regras e restrições de convenções de nomenclatura](/azure/architecture/best-practices/resource-naming) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de usuário ao criar uma VM?

Os nomes de usuário devem ter de 1 a 32 caracteres.

Os seguintes nomes de usuário não são permitidos:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de senha ao criar uma VM?

Há requisitos de comprimento de senha variados, dependendo da ferramenta que você está usando:
 - Portal-entre 12-72 caracteres
 - PowerShell-entre 8-123 caracteres
 - CLI-entre 12-123 caracteres
 - Modelos de Azure Resource Manager (ARM)-12-72 caracteres e caracteres de controle não são permitidos
 

As senhas também devem atender a três dos 4 seguintes requisitos de complexidade:

* Ter caracteres minúsculos
* Ter caracteres maiúsculos
* Ter um dígito
* Ter um caractere especial (Correspondência de regex [\W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
