---
title: Azure Disk Encryption em uma rede isolada
description: Neste artigo, saiba mais sobre dicas de solução de problemas para Microsoft Azure a criptografia de disco em VMs do Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: 8d8d2b88251f837a23c4e82a90eb4d4eb0043702
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553044"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure Disk Encryption em uma rede isolada

Quando a conectividade estiver restrita por requisitos de proxy, firewall ou NSG (grupo de segurança de rede), a capacidade da extensão para executar as tarefas necessárias poderá ser interrompida. Essa interrupção pode resultar em mensagens de status como "Status da extensão não disponível na VM".

## <a name="package-management"></a>Gerenciamento de pacotes

Azure Disk Encryption depende de vários componentes, que são normalmente instalados como parte da habilitação de ADE, se ainda não estiverem presentes. Quando por trás de um firewall ou de outra forma isolada da Internet, esses pacotes devem ser pré-instalados ou disponíveis localmente.

Aqui estão os pacotes necessários para cada distribuição. Para obter uma lista completa dos tipos de volume e distribuições com suporte, consulte [VMs e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems).

- **Ubuntu 14, 4, 16, 4, 18, 4**: lsscsi, psmisc, at, cryptsetup-bin, Python-in, Python-seis, procps, grub-pc-bin
- **CentOS 7,2-7,7**: lsscsi, psmisc, lvm2, UUID, em, patch, cryptsetup, cryptsetup-recriptografar, pyparted, procps-ng, util-linux
- **CentOS 6,8**: lsscsi, psmisc, lvm2, UUID, at, cryptsetup – recriptografar, pyparted, Python-seis
- **RedHat 7,2-7,7**: lsscsi, psmisc, lvm2, UUID, em, patch, cryptsetup, cryptsetup-recriptografar, procps-ng, util-linux
- **RedHat 6,8**: lsscsi, psmisc, lvm2, UUID, at, patch, cryptsetup – recriptografar
- **openSUSE 42,3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

No Red Hat, quando um proxy é necessário, é preciso garantir que o gerenciador de assinaturas e o yum sejam configurados corretamente. Para saber mais, confira [How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533) (Como solucionar problemas do gerenciador de assinaturas e do yum).  

Quando os pacotes são instalados manualmente, eles também devem ser atualizados manualmente conforme novas versões são lançadas.

## <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração do grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos pré-requisitos da configuração de rede documentada para criptografia de disco.  Consulte [Azure Disk Encryption: requisitos de rede](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption com o Azure AD (versão anterior)

Se você estiver usando [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md), a [biblioteca de Azure Active Directory](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) precisará ser instalada manualmente para todos os distribuições (além dos pacotes apropriados para o distribuição, conforme [listado acima](#package-management)).

Quando a criptografia é habilitada com [credenciais do Azure AD](disk-encryption-linux-aad.md), a VM de destino deve permitir a conectividade com pontos de extremidade do Azure Active Directory e pontos de extremidade do Key Vault. Os pontos de extremidade de autenticação Azure Active Directory atuais são mantidos nas seções 56 e 59 da documentação [Microsoft 365 URLs e intervalos de endereços IP](/microsoft-365/enterprise/urls-and-ip-address-ranges) . As instruções do Key Vault são fornecidas na documentação sobre como [Acessar o Azure Key Vault por trás de um firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 

A máquina virtual deve ser capaz de acessar o ponto de extremidade do [serviço de metadados de instância do Azure](instance-metadata-service.md) , que usa um endereço IP não roteável conhecido ( `169.254.169.254` ) que pode ser acessado somente de dentro da VM.  Não há suporte para as configurações de proxy que alteram o tráfego HTTP local para esse endereço (por exemplo, a adição de um cabeçalho X-Forwarded-For).

## <a name="next-steps"></a>Próximas etapas

- Veja mais etapas para a [solução de problemas de criptografia de disco do Azure](disk-encryption-troubleshooting.md)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
