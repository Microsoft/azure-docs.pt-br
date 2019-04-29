---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c44b39effdc6d8fcdc144915ec7b51489e3798cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405403"
---
É importante proteger a sua máquina virtual (VM) para os aplicativos que são executados. Proteger suas VMs pode incluir um ou mais serviços do Azure e recursos que abrangem o acesso seguro a suas máquinas virtuais e armazenamento seguro de seus dados. Este artigo fornece informações que permite que você mantenha sua VM e aplicativos seguros.

## <a name="antimalware"></a>Antimalware

O panorama atual de ameaças a ambientes de nuvem é dinâmico, aumentando a pressão para manter uma proteção eficaz e atender aos requisitos de conformidade e segurança na nuvem. O [Microsoft Antimalware para Azure](../articles/security/azure-security-antimalware.md) é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Os alertas podem ser configurados para notificar você quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou ser executado em sua VM.

## <a name="azure-security-center"></a>Central de Segurança do Azure

A [Central de Segurança do Azure](../articles/security-center/security-center-intro.md) ajuda você a evitar, detectar e responder a ameaças às suas VMs. O Centro de Segurança permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

O acesso Just-In-Time da Central de Segurança pode ser aplicado na sua implantação da VM para bloquear o tráfego de entrada às suas VMs do Azure, reduzindo a exposição a ataques enquanto fornece acesso fácil para conectar às VMs quando necessário. Quando o Just-In-Time está habilitado e um usuário solicita acesso a uma VM, a Central de Segurança verifica quais permissões o usuário tem para a VM. Se o usuário tem as permissões corretas, a solicitação é aprovada e a Central de Segurança configura automaticamente os NSGs (Grupos de Segurança de Rede) para permitir o tráfego de entrada às portas selecionadas pelo período limitado. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores. 

## <a name="encryption"></a>Criptografia

Para conformidade e segurança aprimorados da [VM do Windows](../articles/virtual-machines/windows/encrypt-disks.md) e da [VM do Linux](../articles/virtual-machines/linux/encrypt-disks.md), os discos virtuais no Azure podem ser criptografados. Discos virtuais em VMs do Windows são criptografados em repouso usando o BitLocker. Os discos virtuais em VMs do Linux são criptografados em repouso usando dm-crypt. 

Não há nenhuma taxa para criptografar discos virtuais no Azure. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. Uma entidade de serviço do Azure Active Directory fornece um mecanismo seguro para emitir essas chaves de criptografia, enquanto as VMs são ligadas e desligadas.

## <a name="key-vault-and-ssh-keys"></a>Key Vault e chaves SSH

Os certificados e segredos podem ser modelados como recursos e fornecidos pelo [Key Vault](../articles/key-vault/key-vault-whatis.md). Você pode usar o Azure PowerShell para criar os cofres de chaves de [VMs do Windows](../articles/virtual-machines/windows/key-vault-setup.md) e a CLI do Azure para [VMs do Linux](../articles/virtual-machines/linux/key-vault-setup.md). Você também pode criar chaves de criptografia.

As políticas de acesso do cofre de chaves concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, você pode dar a um usuário acesso apenas a chaves, mas nenhuma permissão para segredos. No entanto, as permissões para acessar chaves, segredos ou certificados estão no nível de cofre. Em outras palavras, a [política de acesso de cofre de chaves](../articles/key-vault/key-vault-secure-your-key-vault.md) não dá suporte a permissões em nível de objeto.

Quando você se conectar a máquinas virtuais, deverá usar a criptografia de chave pública para fornecer uma maneira mais segura para entrar neles. Esse processo envolve uma troca de chaves públicas e privadas usando o comando SSH (secure shell) para autenticar a si mesmo em vez de um nome de usuário e uma senha. As senhas são vulneráveis a ataques de força bruta, especialmente em VMs voltadas para a Internet, como os servidores Web. Com um par de chaves SSH (secure shell), você pode criar uma [VM do Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) que usa chaves SSH para autenticação, eliminando a necessidade de senhas para fazer entrar. Você também pode usar as chaves de SSH para conectar-se de uma [VM do Windows](../articles/virtual-machines/linux/ssh-from-windows.md) para uma VM do Linux.

## <a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas dos recursos do Azure

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais no código para autenticar serviços de nuvem. Proteger as credenciais é uma tarefa importante. O ideal é que as credenciais nunca apareçam em estações de trabalho do desenvolvedor e não sejam verificadas no controle de origem. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais, os segredos e outras chaves, mas seu código precisa se autenticar no Key Vault para recuperá-los. 

As identidades de gerenciado para a funcionalidade de recursos do Azure no Azure AD (Azure Active Directory) resolve esse problema. O recurso oferece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para se autenticar em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.  O código em execução na VM pode solicitar um token de dois pontos de extremidade que só podem ser acessados de dentro da VM. Para obter mais informações sobre esse serviço, examine a página de visão geral [Identidades gerenciadas para recursos do Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).   

## <a name="policies"></a>Políticas

As [Políticas do Azure](../articles/azure-policy/azure-policy-introduction.md) podem ser usadas para definir o comportamento desejado para as [VMs do Windows](../articles/virtual-machines/windows/policy.md) e [VMs do Linux](../articles/virtual-machines/linux/policy.md) da sua organização. Usando políticas, uma organização pode impor várias convenções e regras em toda a empresa. A imposição do comportamento desejado pode ajudar a reduzir o risco e contribui para o sucesso da organização.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Com o [RBAC (controle de acesso baseado em função)](../articles/role-based-access-control/overview.md), você pode separar as tarefas dentro de sua equipe e conceder somente a quantidade de acesso que os usuários em sua VM precisam para realizar seus trabalhos. Em vez de apresentar todas as permissões irrestritas na VM, você pode permitir que apenas determinadas ações. Você pode configurar o controle de acesso para a VM no [portal do Azure](../articles/role-based-access-control/role-assignments-portal.md), usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/role) ou o [Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Próximas etapas
- Siga as etapas para monitorar a segurança da máquina virtual usando a Central de Segurança do Azure para [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) ou [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).