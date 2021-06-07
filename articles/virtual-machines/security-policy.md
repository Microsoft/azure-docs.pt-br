---
title: Políticas seguras e de uso
description: Saiba mais sobre segurança e políticas para máquinas virtuais no Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 291b9a210bf5f8cc18ccf8a523e282d3fc85aa28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673346"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Proteger e usar políticas em máquinas virtuais no Azure

É importante manter sua VM (máquina virtual) segura para os aplicativos que você executa. Proteger suas VMs pode incluir um ou mais serviços do Azure e recursos que abrangem o acesso seguro a suas máquinas virtuais e armazenamento seguro de seus dados. Este artigo fornece informações que permite que você mantenha sua VM e aplicativos seguros.

## <a name="antimalware"></a>Antimalware

O panorama atual de ameaças a ambientes de nuvem é dinâmico, aumentando a pressão para manter uma proteção eficaz e atender aos requisitos de conformidade e segurança na nuvem. O [Microsoft Antimalware para Azure](../security/fundamentals/antimalware.md) é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Os alertas podem ser configurados para notificar você quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou ser executado em sua VM. Não há suporte para ele em VMs que executam o Linux ou o Windows Server 2008.

## <a name="azure-security-center"></a>Central de Segurança do Azure

A [Central de Segurança do Azure](../security-center/security-center-introduction.md) ajuda você a evitar, detectar e responder a ameaças às suas VMs. O Centro de Segurança permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com uma enorme variedade de soluções de segurança.

O acesso just-in-time da central de segurança pode ser aplicado em sua implantação de VM para bloquear o tráfego de entrada para suas VMs do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. Quando o Just-In-Time está habilitado e um usuário solicita acesso a uma VM, a Central de Segurança verifica quais permissões o usuário tem para a VM. Se o usuário tem as permissões corretas, a solicitação é aprovada e a Central de Segurança configura automaticamente os NSGs (Grupos de Segurança de Rede) para permitir o tráfego de entrada às portas selecionadas pelo período limitado. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores. 

## <a name="encryption"></a>Criptografia

Dois métodos de criptografia são oferecidos para discos gerenciados. Criptografia no nível do sistema operacional, que é Azure Disk Encryption e criptografia no nível da plataforma, que é a criptografia do lado do servidor.

### <a name="server-side-encryption"></a>Criptografia no servidor

Os discos gerenciados do Azure criptografam automaticamente os dados por padrão ao enviá-los para a nuvem. A criptografia do lado do servidor protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Os dados nos discos gerenciados são criptografados de maneira transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis, e são compatíveis com o FIPS 140-2.

A criptografia não afeta o desempenho dos discos gerenciados. Não há nenhum custo adicional para a criptografia.

Você pode contar com chaves gerenciadas pela plataforma para a criptografia do disco gerenciado ou gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, pode especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os dados nos discos gerenciados. 

Para saber mais sobre a criptografia do lado do servidor, consulte os artigos para [Windows](./disk-encryption.md) ou [Linux](./disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Para conformidade e segurança aprimorados da [VM do Windows](windows/disk-encryption-overview.md) e da [VM do Linux](linux/disk-encryption-overview.md), os discos virtuais no Azure podem ser criptografados. Discos virtuais em VMs do Windows são criptografados em repouso usando o BitLocker. Os discos virtuais em VMs do Linux são criptografados em repouso usando dm-crypt. 

Não há nenhuma taxa para criptografar discos virtuais no Azure. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. Uma entidade de serviço do Azure Active Directory fornece um mecanismo seguro para emitir essas chaves de criptografia, enquanto as VMs são ligadas e desligadas.

## <a name="key-vault-and-ssh-keys"></a>Key Vault e chaves SSH

Os certificados e segredos podem ser modelados como recursos e fornecidos pelo [Key Vault](../key-vault/general/basic-concepts.md). Você pode usar o Azure PowerShell para criar os cofres de chaves de [VMs do Windows](windows/key-vault-setup.md) e a CLI do Azure para [VMs do Linux](linux/key-vault-setup.md). Você também pode criar chaves de criptografia.

As políticas de acesso do cofre de chaves concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, você pode dar a um usuário acesso apenas a chaves, mas nenhuma permissão para segredos. No entanto, as permissões para acessar chaves, segredos ou certificados estão no nível de cofre. Em outras palavras, a [política de acesso de cofre de chaves](../key-vault/general/secure-your-key-vault.md) não dá suporte a permissões em nível de objeto.

Quando você se conectar a máquinas virtuais, deverá usar a criptografia de chave pública para fornecer uma maneira mais segura para entrar neles. Esse processo envolve uma troca de chaves públicas e privadas usando o comando SSH (secure shell) para autenticar a si mesmo em vez de um nome de usuário e uma senha. As senhas são vulneráveis a ataques de força bruta, especialmente em VMs voltadas para a Internet, como os servidores Web. Com um par de chaves SSH (secure shell), você pode criar uma [VM do Linux](linux/mac-create-ssh-keys.md) que usa chaves SSH para autenticação, eliminando a necessidade de senhas para fazer entrar. Você também pode usar as chaves de SSH para conectar-se de uma [VM do Windows](linux/ssh-from-windows.md) para uma VM do Linux.

## <a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas para os recursos do Azure

Um desafio comum ao criar aplicativos de nuvem é como gerenciar as credenciais no código para autenticar serviços de nuvem. Proteger as credenciais é uma tarefa importante. O ideal é que as credenciais nunca apareçam em estações de trabalho do desenvolvedor e não sejam verificadas no controle de origem. O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais, os segredos e outras chaves, mas seu código precisa se autenticar no Key Vault para recuperá-los. 

As identidades de gerenciado para a funcionalidade de recursos do Azure no Azure AD (Azure Active Directory) resolve esse problema. O recurso oferece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar a identidade para se autenticar em qualquer serviço que dê suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter credenciais em seu código.  O código em execução na VM pode solicitar um token de dois pontos de extremidade que só podem ser acessados de dentro da VM. Para obter mais informações sobre esse serviço, examine a página de visão geral [Identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).   

## <a name="policies"></a>Políticas

As [Políticas do Azure](../governance/policy/overview.md) podem ser usadas para definir o comportamento desejado para as [VMs do Windows](./windows/policy.md) e [VMs do Linux](./linux/policy.md) da sua organização. Usando políticas, uma organização pode impor várias convenções e regras em toda a empresa. A imposição do comportamento desejado pode ajudar a reduzir o risco e contribui para o sucesso da organização.

## <a name="azure-role-based-access-control"></a>Controle de acesso baseado em função do Azure

Usando o Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md), você pode separar as tarefas dentro de sua equipe e conceder apenas a quantidade de acesso aos usuários em sua VM que eles precisam para executar seus trabalhos. Em vez de apresentar todas as permissões irrestritas na VM, você pode permitir que apenas determinadas ações. Você pode configurar o controle de acesso para a VM no [portal do Azure](../role-based-access-control/role-assignments-portal.md), usando a [CLI do Azure](/cli/azure/role) ou o [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Próximas etapas
- Siga as etapas para monitorar a segurança da máquina virtual usando a Central de Segurança do Azure para [Linux](../security/fundamentals/overview.md) ou [Windows](/previous-versions/azure/virtual-machines/tutorial-azure-security).