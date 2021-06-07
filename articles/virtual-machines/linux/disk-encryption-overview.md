---
title: Habilitar o Azure Disk Encryption para VMs do Linux
description: Este artigo fornece instruções sobre como habilitar as VMs do Microsoft Azure Disk Encryption para Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 81c026893c3185c6c9f960cdb6acb2d0c2d49cc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580344"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>VMs do Azure Disk Encryption para Linux 

O Azure Disk Encryption ajuda a proteger seus dados e a atender aos compromissos de conformidade e segurança da sua organização. Ele usa o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure e é integrado ao [Azure Key Vault](../../key-vault/index.yml) para ajudar você a controlar e gerenciar as chaves de criptografia de disco e os segredos.

Azure Disk Encryption é resiliente de zona, da mesma forma que as máquinas virtuais. Para obter detalhes, consulte [Serviços do Azure que dão suporte a zonas de disponibilidade](../../availability-zones/az-region.md).

Se você usar a [Central de Segurança do Azure](../../security-center/index.yml), receberá um alerta se tiver VMs não criptografadas. Esses alertas são mostrados como Alta Gravidade e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco na Central de Segurança do Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se você já tiver usado o Azure Disk Encryption com o Azure AD anteriormente para criptografar uma VM, deverá continuar usando essa opção para criptografar a VM. Confira [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md) para detalhes. 
> - Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição. Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.

Você pode aprender os conceitos básicos do Azure Disk Encryption para Linux em apenas alguns minutos com o [Início rápido: criar e criptografar uma VM do Linux com CLI do Azure](disk-encryption-cli-quickstart.md) ou o [Início rápido: criar e criptografar uma VM do Linux com o Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Sistemas operacionais e VMs com suporte

### <a name="supported-vms"></a>VMs com suporte

As VMs do Linux estão disponíveis em uma [série de tamanhos](../sizes.md). Azure Disk Encryption tem suporte em VMs de geração 1 e geração 2. O Azure Disk Encryption também está disponível para VMs com armazenamento premium.

Confira [tamanhos de VM do Azure sem disco temporário local](../azure-vms-no-temp-disk.md).

O Azure Disk Encryption também não está disponível em [VMs básicas da série A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não atendem a esses requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs do Linux ao criptografar apenas volumes de dados| 2 GB |
| VMs do Linux ao criptografar volumes de dados e do sistema operacional e onde o uso do sistema de arquivos raiz (/) é de 4 GB ou menos | 8 GB |
| VMs do Linux ao criptografar volumes de dados e do sistema operacional e onde o uso do sistema de arquivos raiz (/) é maior que 4 GB | Uso do sistema de arquivos raiz *2. Por exemplo, os 16 GB de uso do sistema de arquivos raiz exigem pelo menos 32 GB de RAM |

Quando o processo de criptografia de disco do sistema operacional for concluído nas máquinas virtuais do Linux, a VM poderá ser configurada para executar com menos memória.

Para obter mais exceções, consulte [Azure Disk Encryption: Cenários sem suporte](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

O Azure Disk Encryption é compatível com um subconjunto de [distribuições Linux endossadas pelo Azure](endorsed-distros.md) que, na verdade, é um subconjunto de todas as distribuições possíveis do servidor Linux.

![Diagrama Venn das distribuições de servidor Linux compatíveis com o Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

As distribuições do servidor Linux que não são endossadas pelo Azure não são compatíveis com o Azure Disk Encryption; daqueles que são endossados, somente as seguintes distribuições e versões são compatíveis com o Azure Disk Encryption:


| Publicador | Oferta | SKU | URN | Tipo de volume suportado para criptografia |
| --- | --- |--- | --- |
| Canônico | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | SO e disco de dados |
| Canônico | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | SO e disco de dados |
| Canônico | Ubuntu 16.04 | 16.04-LTS-DIÁRIO | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | SO e disco de dados |
| Canônico | Ubuntu 14.04.5</br>[com kernel ajustado para Azure atualizado para 4.15 ou posterior](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | SO e disco de dados |
| Canônico | Ubuntu 14.04.5</br>[com kernel ajustado para Azure atualizado para 4.15 ou posterior](disk-encryption-troubleshooting.md) | 14.04.5-LTS-DIÁRIO | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | SO e disco de dados |
| RedHat | RHEL 8-LVM | 8-LVM | RedHat: RHEL: 8-LVM: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 8,2 | 8.2 | RedHat: RHEL: 8.2: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 8.1 | 8.1 | RedHat: RHEL: 8.1: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7-LVM | 7-LVM | RedHat: RHEL: 7-LVM: 7.8.2020111201 | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7,8 | 7.8 | RedHat: RHEL: 7.8: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:latest | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.5 | 7.5 | RedHat:RHEL:7.5:latest | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 6.8 | 6,8 | RedHat:RHEL:6.8:latest | Disco de dados (consulte a observação abaixo) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | Disco de dados (consulte a observação abaixo) |
| OpenLogic | CentOS 8 – LVM | 8-LVM | OpenLogic: CentOS-LVM: 8-LVM: mais recente | SO e disco de dados |
| OpenLogic | CentOS 8,2 | 8_2 | OpenLogic: CentOS: 8_2: mais recente | SO e disco de dados |
| OpenLogic | CentOS 8,1 | 8_1 | OpenLogic: CentOS: 8_1: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7 – LVM | 7-LVM | OpenLogic: CentOS-LVM: 7-LVM: 7.8.2020111100 | SO e disco de dados |
| OpenLogic | CentOS 7,8 | 7.8 | OpenLogic: CentOS: 7_8: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:latest | SO e disco de dados |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | SO e disco de dados |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic:CentOS:7.5:latest | SO e disco de dados |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | SO e disco de dados |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | SO e disco de dados |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | SO e disco de dados |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | Somente o disco de dados |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | Somente o disco de dados |
| OpenLogic | CentOS 6.8 | 6,8 | OpenLogic:CentOS:6.8:latest | Somente o disco de dados |
| SUSE | OpenSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | Somente o disco de dados |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | Somente o disco de dados |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | Somente o disco de dados |

> [!NOTE]
> A nova implementação do Azure Disk Encryption é compatível com o SO RHEL e com disco de dados para imagens RHEL7 com Pagamento Conforme o Uso.  
>
> O ADE também é compatível com imagens Gold do tipo traga sua própria assinatura (Bring-Your-Own-Subscription) da RHEL, mas somente **depois** que a assinatura tiver sido registrada. Para obter mais informações, consulte [Imagens Gold do tipo traga sua própria assinatura da Red Hat Enterprise Linux no Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images).

## <a name="additional-vm-requirements"></a>Requisitos adicionais da VM

O Azure Disk Encryption requer que os módulos dm-crypt e vfat estejam presentes no sistema. Remover ou desabilitar o vfat da imagem padrão impedirá o sistema de ler o volume de chaves e obter a chave necessária para desbloquear os discos em reinicializações subsequentes. As etapas de proteção do sistema que removem o módulo vfat do sistema ou impõem a expansão das montagem/pastas do sistema operacional em unidades de dados não são compatíveis com Azure Disk Encryption. 

Antes de habilitar a criptografia, os discos de dados a serem criptografados precisam ser listados corretamente em /etc/fstab. Use a opção "nofail" ao criar entradas e escolha um nome de dispositivo de bloqueio persistente (já que os nomes de dispositivo no formato "/dev/sdX" podem não estar associados ao mesmo disco entre reinicializações, especialmente após a criptografia; para obter mais detalhes sobre esse comportamento, consulte: [Solucionar problemas com alterações no nome de dispositivo da VM do Linux](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)).

Verifique se as configurações de /etc/fstab estão definidas corretamente para a montagem. Para definir essas configurações, execute o comando mount - a ou reinicie a VM e disparar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar se a unidade ainda está montada. 

- Se o arquivo /etc/fstab não montar a unidade corretamente antes de habilitar a criptografia, o Azure Disk Encryption não será capaz de montá-la corretamente.
- O processo de criptografia de disco do Azure se moverá as informações de montagem fora /etc/fstab e em seu próprio arquivo de configuração como parte do processo de criptografia. Não se assuste para ver a entrada ausente do /etc/fstab após a criptografia de unidade de dados é concluída.
- Antes de iniciar a criptografia, interrompa todos os serviços e processos que podem estar gravando em discos de dados montados e desabilite-os para que não sejam reiniciados automaticamente após uma reinicialização. Eles poderiam manter os arquivos abertos nessas partições, impedindo o procedimento de criptografia de remontá-los, causando a falha da criptografia. 
- Após a reinicialização, ela levará tempo para o processo de criptografia de disco do Azure montar os discos criptografados recentemente. Eles não estarão disponíveis imediatamente após uma reinicialização. O processo precisa de tempo para iniciar, desbloquear e, em seguida, montar as unidades criptografadas antes de estar disponível para ser acessado por outros processos. Esse processo pode levar mais de um minuto após a reinicialização, dependendo das características do sistema.

Aqui está um exemplo dos comandos usados para montar os discos de dados e criar as entradas /etc/fstab necessárias:

```bash
UUID0="$(blkid -s UUID -o value /dev/sda1)"
UUID1="$(blkid -s UUID -o value /dev/sda2)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Requisitos de rede

Para habilitar o recurso de Azure Disk Encryption, as VMs do Linux devem atender aos seguintes requisitos de configuração do ponto de extremidade de rede:
  - Para obter um token para se conectar ao seu cofre de chaves, a VM do Linux deve ser capaz de se conectar a um ponto de extremidade do Azure Active Directory, \[login.microsoftonline.com\].
  - Para gravar as chaves de criptografia no cofre de chaves, a VM do Linux deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM do Linux deve ser capaz de se conectar a um ponto de extremidade do armazenamento do Azure que hospede o repositório de extensão do Azure e a uma conta de armazenamento do Azure que hospede os arquivos VHD.
  -  Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia  

O Azure Disk Encryption requer um Azure Key Vault para ajudar você a controlar e gerenciar os segredos e chaves de criptografia de disco. Seu cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para saber detalhes, confira [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia

A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Cofre de Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) é otimizada para gerenciar e administrar os recursos do Azure na linha de comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de criptografia de disco transparente baseado em Linux usado para habilitar a criptografia de disco nas VMs do Linux. |
| Chave com criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que pode ser usada para proteger ou encapsular o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, confira [cmdlets do Azure PowerShell](/powershell/azure/). |


## <a name="next-steps"></a>Próximas etapas

- [Início Rápido - criar e criptografar uma VM do Linux com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Início rápido-criar e criptografar uma VM Linux com Azure PowerShell](disk-encryption-powershell-quickstart.md) 
- [Cenários de Azure Disk Encryption em VMs Linux](disk-encryption-linux.md)
- [Script da CLI dos pré-requisitos do Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell dos pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)