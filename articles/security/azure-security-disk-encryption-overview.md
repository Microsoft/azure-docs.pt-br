---
title: O que é Azure Disk Encryption?
description: Este artigo fornece uma visão geral de Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/17/2019
ms.custom: seodec18
ms.openlocfilehash: a67f19f0823827dad74e7aba15a92d696fbf580b
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304280"
---
# <a name="azure-disk-encryption-overview"></a>Visão geral de Azure Disk Encryption

Azure Disk Encryption ajuda a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade da organização. Ele usa o recurso [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure. Ele também é integrado com [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco e garante que todos os dados nos discos de VM sejam criptografados em repouso no armazenamento do Azure. Azure Disk Encryption para VMs Windows e Linux está em disponibilidade geral em todas as regiões públicas do Azure e regiões do Azure governamental para VMs padrão e VMs com o armazenamento Premium do Azure. 

Se você usar a Central de Segurança do Azure, receberá um alerta se tiver VMs não criptografadas. Esses alertas são mostrados como Alta Gravidade e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco na Central de Segurança do Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação e resultar em custos adicionais de licença ou assinatura.


## <a name="encryption-scenarios"></a>Cenários de criptografia

Com o Azure Disk Encryption, você pode atender aos requisitos de segurança e conformidade organizacionais protegendo suas VMs do Azure em repouso usando a tecnologia de criptografia padrão do setor. Você também pode configurar VMs para inicialização em chaves e políticas controladas pelo cliente (BYOK) e auditar o uso dessas chaves em seu cofre de chaves.

O Azure Disk Encryption dá suporte aos seguintes cenários de cliente:

* Habilitar e desabilitar a criptografia em novas VMs criadas com base nas imagens do Azure Gallery com suporte.
* Habilitando e desabilitando a criptografia em VMs existentes que são executadas no Azure.
* Habilitar e desabilitar a criptografia em novas VMs do Windows criadas com base em VHD e chaves de criptografia previamente criptografadas.
* Habilitando e desabilitando a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows.
* Habilitando e desabilitando a criptografia em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Habilitando e desabilitando a criptografia de VMs de disco gerenciado.
* Atualizando as configurações de criptografia de uma VM de armazenamento existente criptografada Premium e não Premium.
* Fazendo backup e restaurando VMs criptografadas.
* BYOE (traga sua própria criptografia) e cenários de traga sua própria chave (BYOK), em que os clientes usam suas próprias chaves de criptografia e as armazenam em um cofre de chaves do Azure.

Ele também dá suporte aos seguintes cenários para VMs quando eles estão habilitados no Microsoft Azure:

* Integração com o Azure Key Vault.
* As [VMs da camada Standard](https://azure.microsoft.com/pricing/details/virtual-machines/) que atendem ao [requisito mínimo de memória](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Habilitando a criptografia em VMs do Windows e Linux, disco gerenciado e VMs do conjunto de dimensionamento das imagens com suporte da galeria do Azure.
* Desabilitar a criptografia no sistema operacional e nas unidades de dados para VMs do Windows, VMs do conjunto de dimensionamento e VMs de disco gerenciado.
* Desabilitar a criptografia em unidades de dados para VMs do Linux, VMs do conjunto de dimensionamento e VMs de disco gerenciado.
* Habilitando a criptografia em VMs que executam o sistema operacional cliente Windows.
* Habilitando a criptografia em volumes com caminhos de montagem.
* Habilitando a criptografia em VMs Linux configuradas com a distribuição de disco (RAID) usando o mdadm.
* Habilitando a criptografia em VMs Linux que usam o LVM para discos de dados.
* Habilitando a criptografia no so da VM Linux e nos discos de dados.

   > [!NOTE]
   > Não há suporte para criptografia de unidade do sistema operacional para algumas distribuições do Linux. Para obter mais informações, consulte [os Azure Disk Encryption sistemas operacionais com suporte: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Habilitando a criptografia em VMs configuradas com espaços de armazenamento do Windows a partir do Windows Server 2016. Espaços de Armazenamento Diretos (S2D) ainda não tem suporte.
* Backup e restauração de VMs criptografadas para KEK (chave de criptografia de chave) e cenários não KEK.

Azure Disk Encryption não funciona para os seguintes cenários, recursos e tecnologia:

* Criptografia de VM de camada básica ou VMs criadas por meio do método de criação de VM clássico.
* Desabilitar a criptografia em uma unidade do sistema operacional ou unidade de dados de uma VM do Linux quando a unidade do sistema operacional é criptografada.
* Criptografando a unidade do sistema operacional para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Criptografia de VMs do Windows configuradas com sistemas RAID baseados em software.
* Criptografar imagens personalizadas em VMs do Linux.
* Integração com um sistema de gerenciamento de chaves local.
* Arquivos do Azure (sistema de arquivo compartilhado).
* NFS (Network File System).
* Volumes dinâmicos.

## <a name="encryption-features"></a>Recursos de criptografia

Ao habilitar e implantar Azure Disk Encryption para VMs do Azure, você pode configurar os seguintes recursos para serem habilitados:

* Criptografar o volume do sistema operacional para proteger o volume de inicialização em repouso no armazenamento.
* Criptografar volumes de dados para proteger os volumes de dados em repouso em seu armazenamento.
* Desabilitar a criptografia no sistema operacional e nas unidades de dados para VMs do Windows.
* Desabilitar a criptografia nas unidades de dados para VMs do Linux (somente quando a unidade do sistema operacional não está criptografada).
* Proteger as chaves de criptografia e os segredos em sua assinatura do Azure Key Vault.
* Relatar o status de criptografia da VM criptografada.
* Removendo as definições de configuração de criptografia de disco da VM.
* Fazer backup e restaurar as VMs criptografadas usando o serviço de backup do Azure.

O Azure Disk Encryption para VMs para Windows e Linux inclui:

* [A extensão de criptografia de disco para Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [A extensão de criptografia de disco para Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Os cmdlets de criptografia de disco do PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Os cmdlets de criptografia de disco CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Os modelos de criptografia de disco Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Não são cobradas taxas adicionais para criptografar discos de VM com o Azure Disk Encryption. O [preço do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) padrão aplica-se ao cofre de chaves que é usado para armazenar as chaves de criptografia. 

## <a name="encryption-workflow"></a>Fluxo de trabalho de criptografia

Para habilitar a criptografia de disco para VMs do Windows e do Linux, faça o seguinte:

1. Opte por habilitar a criptografia de disco por meio do modelo do Gerenciador de Recursos de Azure Disk Encryption, cmdlets do PowerShell ou CLI do Azure e especifique a configuração de criptografia.

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM.
   * Para novas VMs criadas a partir de imagens da galeria com suporte e VMs existentes que já são executadas no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM.

1. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) do cofre de chaves para habilitar a criptografia na VM.

1. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.

   ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Fluxo de trabalho de descriptografia
Para desabilitar a criptografia de disco para VMs, conclua as seguintes etapas de alto nível:

1. Opte por desabilitar a criptografia (descriptografia) em uma VM em execução no Azure e especifique a configuração de descriptografia. É possível por meio do modelo do Resource Manager do Azure Disk Encryption, cmdlets do PowerShell ou da CLI do Azure.

   Esta etapa desabilita a criptografia do sistema operacional ou o volume de dados ou ambos na VM do Windows em execução. Como mencionado na seção anterior, não há suporte para desabilitar a criptografia de disco do sistema operacional para Linux. A etapa de descriptografia é permitida apenas para unidades de dados em VMs do Linux, desde que o disco do SO não esteja criptografado.

1. O Azure atualiza o modelo de serviço de VM e a VM é marcada como descriptografada. O conteúdo da VM não está criptografado em repouso.

   > [!NOTE]
   > A operação de desabilitação da criptografia não exclui seu cofre de chaves nem o material da chave de criptografia (chaves de criptografia do BitLocker para sistemas Windows ou a Frase secreta para Linux).
   >
   > Não há suporte para desativação de criptografia de disco de OS para Linux. A etapa de descriptografia é permitida somente para unidades de dados em VMs Linux.
   >
   > Desabilitar criptografia do disco de dados para Linux não tem suporte se a unidade do SO estiver criptografada.


## <a name="encryption-workflow-previous-release"></a>Fluxo de trabalho de criptografia (versão anterior)

A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Microsoft Azure Active Directory (Azure AD) para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa fornecer uma credencial do Microsoft Azure Active Directory durante a etapa de habilitação de criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Microsoft Azure Active Directory ao usar a nova versão. As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do Microsoft Azure Active Directory. Para habilitar a criptografia de disco para VMs do Windows e do Linux (versão anterior), faça o seguinte:

1. Escolha um cenário de criptografia nos cenários listados na seção [Cenários de criptografia](#encryption-scenarios).

1. Opte por habilitar a criptografia de disco por meio do modelo do Gerenciador de Recursos de Azure Disk Encryption, cmdlets do PowerShell ou CLI do Azure e especifique a configuração de criptografia.

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM.
   * Para novas VMs que são criadas a partir do Marketplace e VMs existentes que já são executadas no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM.

1. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) do cofre de chaves para habilitar a criptografia na VM.

1. Forneça a identidade de aplicativo do Microsoft Azure Active Directory para gravar o material de chave de criptografia no cofre de chaves. Esta etapa habilita a criptografia na VM para os cenários mencionados na etapa 2.

1. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.


## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| AD do Azure | Uma conta do [Microsoft Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) é usada para autenticar, armazenar e recuperar segredos de um cofre de chaves. |
| Cofre da Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria que é usada para habilitar a criptografia de disco em VMs do Windows. |
| BEK | As chaves de criptografia do BitLocker (BEK) são usadas para criptografar o volume de inicialização do sistema operacional e os volumes de dados. As BEKs são protegidas em um cofre de chaves como segredos. |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) é otimizada para gerenciar e administrar os recursos do Azure na linha de comando.|
| DM-Crypt |[DM-cript](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de criptografia de disco transparente, baseado em Linux, usado para habilitar a criptografia de disco em VMs do Linux. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou encapsular o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlets do PowerShell | Para obter mais informações, confira [cmdlets do Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Próximas etapas

Para começar, consulte os [pré-requisitos de Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

