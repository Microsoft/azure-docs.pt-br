---
title: Ingressar uma VM do Windows Server no Azure Active Directory Domain Services | Microsoft Docs
description: Ingressar uma máquina virtual do Windows Server em um domínio gerenciado usando modelos do Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 3d16a4240b7a30a483b70b068ab7d91ca7bdcb17
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473030"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Ingressar uma máquina virtual do Windows Server em um domínio gerenciado usando modelos do Resource Manager
Este artigo mostra como ingressar uma máquina virtual do Windows Server em um domínio gerenciado do Azure AD Domain Services usando modelos do Resource Manager.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisa do seguinte:
1. Uma **assinatura do Azure**válida.
2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.
3. **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua as etapas necessárias para [sincronizar senhas para seu domínio gerenciado do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Instalar e configurar as ferramentas necessárias
Você pode usar qualquer uma das opções a seguir para executar as etapas descritas neste documento:
* **Azure PowerShell**: [Instalar e configurar](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **CLI do Azure**: [Instalar e configurar](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opção 1: Provisionar uma nova VM do Windows Server e uni-la a um domínio gerenciado
**Nome do modelo de Início Rápido**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Execute as seguintes etapas para implantar uma máquina virtual do Windows Server e ingressá-la em um domínio gerenciado:
1. Navegue até o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Clique em **Implantar no Azure**.
3. Na página **Implantação Personalizada**, forneça as informações necessárias para provisionar a máquina virtual.
4. Selecione a **Assinatura do Azure** na qual você deseja provisionar a máquina virtual. Escolha a mesma assinatura do Azure em que você habilitou o Azure AD Domain Services.
5. Escolha um **Grupo de recursos** existente ou crie um novo.
6. Escolha um **Local** no qual implantar a nova máquina virtual.
7. Em **Nome de VNET existente** especifique a rede virtual na qual você implantou o domínio gerenciado do Azure AD Domain Services.
8. Em **Nome da sub-rede existente**, especifique a sub-rede na rede virtual em que você deseja implantar essa máquina virtual. Não selecione a sub-rede de gateway na rede virtual. Além disso, não selecione a sub-rede dedicada na qual seu domínio gerenciado está implantado.
9. Em **Prefixo do rótulo DNS**, especifique o nome do host para a máquina virtual que está sendo provisionada. Por exemplo, “contoso-win”.
10. Selecione o **Tamanho da VM** apropriado para a máquina virtual.
11. Em **Domínio de Ingresso**, especifique o nome de domínio DNS do seu domínio gerenciado.
12. Em **Nome de usuário de domínio**, especifique o nome da conta do usuário no domínio gerenciado que deve ser usado para ingressar a VM no domínio gerenciado.
13. Em **Senha do Domínio**, especifique a senha da conta de usuário de domínio referida pelo parâmetro “domainUsername”.
14. Opcional: É possível especificar um **Caminho de UO** para uma UO personalizada, à qual a máquina virtual será adicionada. Se você não especificar um valor para esse parâmetro, a máquina virtual será adicionada à UO de **Computadores AAD DC** no domínio gerenciado.
15. No campo **Nome do usuário administrador da VM**, especifique um nome de conta de administrador local para a máquina virtual.
16. No campo **Senha de administrador da VM**, especifique uma senha para o administrador local da máquina virtual. Escolha uma senha forte para o administrador local da máquina virtual para protegê-la contra ataques de força bruta de senha.
17. Clique em **Concordo com os termos e condições declarados acima**.
18. Clique em **Comprar** para provisionar a máquina virtual.

> [!WARNING]
> **Trate as senhas com cuidado.**
> O arquivo de parâmetro de modelo contém as senhas das contas de domínio, bem como as senhas de administrador local da máquina virtual. Certifique-se de nunca deixar este arquivo desprotegido em compartilhamentos de arquivos ou outros locais compartilhados. Recomendamos descartar esse arquivo depois de concluir a implantação das máquinas virtuais.
>

Depois que a implantação for concluída com êxito, sua máquina virtual do Windows recém-provisionada estará ingressada no domínio gerenciado.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opção 2: Unir uma VM do Windows Server existente em um domínio gerenciado
**Modelo de Início Rápido**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Execute as seguintes etapas para ingressar uma máquina virtual do Windows Server em um domínio gerenciado:
1. Navegue até o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Clique em **Implantar no Azure**.
3. Na página **Implantação Personalizada**, forneça as informações necessárias para provisionar a máquina virtual.
4. Selecione a **Assinatura do Azure** na qual você deseja provisionar a máquina virtual. Escolha a mesma assinatura do Azure em que você habilitou o Azure AD Domain Services.
5. Escolha um **Grupo de recursos** existente ou crie um novo.
6. Escolha um **Local** no qual implantar a nova máquina virtual.
7. No campo **Lista de VMs**, especifique os nomes das máquinas virtuais existentes a serem ingressadas em um domínio gerenciado. Use uma vírgula para separar os nomes VM individuais. Por exemplo: **contoso-web, contoso-api**.
8. Em **Nome de usuário de ingresso no domínio**, especifique o nome da conta do usuário no domínio gerenciado que será usado para ingressar a VM no domínio gerenciado.
9. Em **Senha de usuário de ingresso no domínio**, especifique a senha da conta de usuário de domínio referida pelo parâmetro “domainUsername”.
10. Em **FQDN do Domínio**, especifique o nome de domínio do DNS do seu domínio gerenciado.
11. Opcional: É possível especificar um **Caminho de UO** para uma UO personalizada, à qual a máquina virtual será adicionada. Se você não especificar um valor para esse parâmetro, a máquina virtual será adicionada à UO de **Computadores AAD DC** no domínio gerenciado.
12. Clique em **Concordo com os termos e condições declarados acima**.
13. Clique em **Comprar** para provisionar a máquina virtual.

> [!WARNING]
> **Trate as senhas com cuidado.**
> O arquivo de parâmetro de modelo contém as senhas das contas de domínio, bem como as senhas de administrador local da máquina virtual. Certifique-se de nunca deixar este arquivo desprotegido em compartilhamentos de arquivos ou outros locais compartilhados. Recomendamos descartar esse arquivo depois de concluir a implantação das máquinas virtuais.
>

Depois que a implantação for concluída com êxito, as máquinas virtuais do Windows serão ingressadas no domínio gerenciado.


## <a name="related-content"></a>Conteúdo relacionado
* [Visão geral do Azure PowerShell](/powershell/azure/overview)
* [Modelo de início rápido do Azure – Ingresso no domínio de uma nova VM](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Modelo de início rápido do Azure – Ingresso no domínio de uma VM existente](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
