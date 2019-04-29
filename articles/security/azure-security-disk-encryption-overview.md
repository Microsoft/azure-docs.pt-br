---
title: Visão geral - Azure Disk Encryption para VMs da IaaS | Microsoft Docs
description: Este artigo fornece uma visão geral do Microsoft Azure Disk Encryption para VMs IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/16/2019
ms.custom: seodec18
ms.openlocfilehash: 66d788aec83e3e57a49b063f2ca80484360f639d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612087"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption para VMs IaaS

O Microsoft Azure está comprometido em garantir a privacidade e a soberania de seus dados. O Azure permite controlar os dados hospedados no Azure por meio de várias tecnologias avançadas para criptografar, controlar, gerenciar chaves de criptografia e controle e auditoria de acesso a dados. Esse controle permite que os clientes do Azure tenham a flexibilidade de escolher a solução que melhor atenda às necessidades de negócios. Este artigo apresenta uma solução de tecnologia: "Azure Disk Encryption para VMs (máquinas virtuais) da IaaS do Windows e Linux." Essa tecnologia ajuda a proteger seus dados e a atender aos compromissos de conformidade e segurança de sua organização. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Visão geral

O Azure Disk Encryption é uma capacidade que ajuda você a criptografar seus discos de VM IaaS Windows e Linux. O Disk Encryption aproveita o recurso padrão da indústria [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudar a controlar e gerenciar os segredos e as chaves de criptografia de disco. A solução também garante que todos os dados em discos da VM sejam criptografados em repouso no armazenamento do Azure.

O Disk Encryption para VMs IaaS do Windows e Linux agora está em Disponibilidade Geral em todas as regiões públicas do Azure e regiões do Microsoft Azure Governamental para VMs Padrão e VMs com Armazenamento Premium do Azure. Ao aplicar a solução de gerenciamento do Disk Encryption, você pode atender às seguintes necessidades de negócios:

* As VMs IaaS ficam protegidas em repouso usando a tecnologia de criptografia padrão da indústria para tratar da segurança organizacional e dos requisitos de conformidade.
* Inicialização de VMs de IaaS em políticas e chaves controladas pelo cliente. Você pode auditar o uso no cofre de chaves.

Se você usar a Central de Segurança do Azure, receberá um alerta se tiver VMs não criptografadas. Esses alertas são mostrados como Alta Gravidade e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco na Central de Segurança do Azure](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação e resultar em custos adicionais de licença ou assinatura.


## <a name="encryption-scenarios"></a>Cenários de criptografia

A solução Disk Encryption dá suporte aos seguintes cenários do cliente:

* Habilite a criptografia em novas VMs IaaS criadas a partir de VHD pré-criptografado e chaves de criptografia.
* Habilite criptografia em novas VMs de IaaS criadas a partir das imagens da Galeria do Azure com suporte.
* Habilite a criptografia em VMs IaaS existentes que são executadas no Azure.
* Habilite a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows.
* Habilite a criptografia em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Desabilite a criptografia em VMs IaaS do Windows.
* Desabilite a criptografia em unidades de dados para VMs IaaS do Linux.
* Desabilite a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows.
* Desabilite a criptografia em unidades de dados para conjuntos de dimensionamento de máquinas virtuais do Linux.
* Habilite a criptografia de VMs de disco gerenciado.
* Atualize configurações de criptografia de uma VM criptografada de Armazenamento Premium e não Premium existente.
* Faça backup e restauração de VMs criptografadas.

A solução dá suporte aos seguintes cenários para VMs IaaS quando habilitadas no Microsoft Azure:

* Integração com o Azure Key Vault.
* VMs da camada padrão: [VMs de IaaS séries A, D, DS, G, GS, F, e assim por diante](https://azure.microsoft.com/pricing/details/virtual-machines/). As [VMs do Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport), dentro dessas camadas, devem atender ao requisito mínimo de memória de 7 GB.
* Habilite a criptografia em VMs de IaaS do Windows e Linux, VMs de disco gerenciado e VMs do conjunto de dimensionamento por meio das imagens da Galeria do Azure com suporte.
* Desabilite a criptografia no SO e nas unidades de dados para VMs IaaS do Windows, VMs de conjunto de dimensionamento e VMs de disco gerenciado.
* Desabilite a criptografia em unidades de dados para VMs IaaS do Linux, VMs de conjunto de dimensionamento e VMs de disco gerenciado.
* Habilite a criptografia em VMs IaaS que são executadas no sistema operacional Windows Client.
* Habilite a criptografia em volumes com caminhos de montagem.
* Habilite a criptografia em VMs do Linux que estão configuradas com distribuição de discos (RAID) usando o mdadm.
* Habilite criptografia no Linux VMs que usam LVM para discos de dados.
* Habilite criptografia no SO e nos discos de dados de VM do Linux.

   > [!NOTE]
   > Não há suporte para criptografia de unidade do sistema operacional para algumas distribuições do Linux. Para obter mais informações, confira o artigo [Perguntas frequentes sobre o Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
   
* Habilite a criptografia em VMs que são configuradas com a partir de espaços de armazenamento do Windows no Windows Server 2016.
* Atualize configurações de criptografia para uma VM criptografada de armazenamento Premium e não Premium existente.
* Faça backup e restauração de VMs criptografada para cenários chave de criptografia de chaves (KEK) e não KEK.
* Todas as regiões do Público do Azure e Azure Governamental têm suporte.

A solução não dá suporte aos seguintes cenários, recursos e tecnologia:

* VMs IaaS da camada Básica.
* Desabilite a criptografia em uma unidade do sistema operacional para VMs IaaS do Linux.
* Desabilite criptografia em uma unidade de dados quando a unidade do SO estiver criptografada para VMs de IaaS do Linux.
* Define a criptografia de unidade do sistema operacional para o dimensionamento de máquinas virtuais do Linux.
* VMs de IaaS que são criadas usando o método de criação de VM clássico.
* Habilite a criptografia em imagens personalizadas do cliente em VMs IaaS do Linux.
* Integração com o sistema de gerenciamento de chaves no local.
* Arquivos do Azure (sistema de arquivo compartilhado).
* NFS (Network File System).
* Volumes dinâmicos.
* VMs do Windows configuradas com sistemas RAID baseados em software.

## <a name="encryption-features"></a>Recursos de criptografia

Quando você habilita e a implanta o Disk Encryption para VMs IaaS do Azure, os seguintes recursos são habilitados, dependendo da configuração fornecida:

* Criptografia do volume do sistema operacional para proteger o volume de inicialização em repouso no armazenamento.
* Criptografia de volumes de dados para proteger os volumes de dados em repouso no armazenamento.
* Desabilite a criptografia em unidades do sistema operacional e de dados para VMs IaaS do Windows.
* Desabilite a criptografia nas unidades de dados para VMs de IaaS do Linux (somente quando a unidade do SO não estiver criptografada).
* Proteja as chaves de criptografia e segredos em sua assinatura do Azure Key Vault.
* Comunique o status de criptografia da VM IaaS criptografada.
* Remova as definições de configuração de criptografia de disco da VM IaaS.
* Backup e restauração das VMs criptografadas usando o serviço de Backup do Azure.

o Azure Disk Encryption para VMS IaaS para a solução Windows e Linux inclui:

* A extensão da criptografia de disco para Windows.
* A extensão da criptografia de disco para Linux.
* Os cmdlets de criptografia de disco do PowerShell.
* Os cmdlets de criptografia de disco da CLI do Azure.
* Os modelos de criptografia de disco do Azure Resource Manager.

Há suporte para a solução do Azure Disk Encryption em VMs IaaS executando o sistema operacional Windows ou Linux. Para obter mais informações sobre os sistemas operacionais com suporte, consulte o artigo [Pré-requisitos](azure-security-disk-encryption-prerequisites.md).

> [!NOTE]
> Não são cobradas taxas adicionais para criptografar discos de VM com o Azure Disk Encryption. O [preço do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) padrão aplica-se ao cofre de chaves que é usado para armazenar as chaves de criptografia. 


## <a name="encryption-workflow"></a>Fluxo de trabalho de criptografia

Para habilitar a criptografia de disco para VMs do Windows e do Linux, faça o seguinte:

1. Escolha um cenário de criptografia nos cenários listados na seção [Cenários de criptografia](#encryption-scenarios).

1. Opte por habilitar a criptografia de disco por meio do modelo do Gerenciador de Recursos de Azure Disk Encryption, cmdlets do PowerShell ou CLI do Azure e especifique a configuração de criptografia.

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM IaaS.
   * Para novas VMs que são criadas com base no Marketplace e VMs existentes que já são executados no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM IaaS.

1. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) de seu cofre de chaves para habilitar a criptografia na VM IaaS.

1. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.

   ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Fluxo de trabalho de descriptografia
Para desabilitar a criptografia de disco para VMs IaaS, conclua as seguintes etapas de alto nível:

1. Opte por desabilitar a criptografia (descriptografia) em uma VM IaaS em execução no Azure e especifique a configuração da descriptografia. É possível por meio do modelo do Resource Manager do Azure Disk Encryption, cmdlets do PowerShell ou da CLI do Azure.

   Esta etapa desabilitará a criptografia do sistema operacional ou o volume de dados ou ambos na VM IaaS do Windows em execução. Como mencionado na seção anterior, não há suporte para desabilitar a criptografia de disco do sistema operacional para Linux. A etapa de descriptografia é permitida apenas para unidades de dados em VMs do Linux, desde que o disco do SO não esteja criptografado.

1. O Azure atualiza o modelo de serviço da VM e a VM IaaS é marcada como descriptografada. O conteúdo da VM não está criptografado em repouso.

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

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM IaaS.
   * Para novas VMs que são criadas com base no Marketplace e VMs existentes que já são executados no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM IaaS.

1. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) de seu cofre de chaves para habilitar a criptografia na VM IaaS.

1. Forneça a identidade de aplicativo do Microsoft Azure Active Directory para gravar o material de chave de criptografia no cofre de chaves. Essa etapa permite a criptografia na VM IaaS para os cenários mencionados na etapa 2.

1. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.


## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados nessa tecnologia:

| Terminologia | Definição |
| --- | --- |
| AD do Azure | Uma conta do [Microsoft Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) é usada para autenticar, armazenar e recuperar segredos de um cofre de chaves. |
| Cofre da Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| BitLocker |O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria e usada para habilitar a criptografia de disco em VMs de IaaS do Windows. |
| BEK | As chaves de criptografia do BitLocker (BEK) são usadas para criptografar o volume de inicialização do sistema operacional e os volumes de dados. As BEKs são protegidas em um cofre de chaves como segredos. |
| CLI do Azure | [A CLI do Azure](/cli/azure/install-azure-cli) é otimizada para gerenciar e administrar os recursos do Azure na linha de comando.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) é o subsistema de criptografia de disco transparente baseado em Linux usado para habilitar a criptografia de disco nas VMs de IaaS do Linux. |
| KEK | A chave de criptografia de chaves (KEK) é a chave assimétrica (RSA 2048) que pode ser usada para proteger ou encapsular o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlets do PowerShell | Para obter mais informações, confira [cmdlets do Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
