---
title: Azure Disk Encryption para VMs IaaS Windows e Linux | Microsoft Docs
description: "Este artigo fornece uma visão geral do Microsoft Azure Disk Encryption para VMs IaaS do Windows e Linux."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2017
ms.author: kakhan
ms.openlocfilehash: ebf3062ab0600b0ae722c78d07095970001a0a23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Azure Disk Encryption para VMs IaaS Windows e Linux
O Microsoft Azure tem o compromisso sério de garantir a privacidade e a soberania dos seus dados e permite que você controle os dados hospedados no Azure usando uma variedade de tecnologias para criptografar, controlar e gerenciar chaves de criptografia, bem como auditar e controlar o acesso aos dados. Isso permite que os clientes do Azure tenham a flexibilidade de escolher a solução que melhor atenda às necessidades de negócios. Neste artigo, apresentaremos a você uma nova solução de tecnologia, "Azure Disk Encryption para VMs IaaS Windows e Linux" para ajudá-lo a proteger seus dados e atender às obrigações de conformidade e segurança da organização. O documento fornece orientações detalhadas sobre como usar os recursos de criptografia de disco do Azure, incluindo os cenários com suporte e as experiências de usuário.

> [!NOTE]
> Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição.

## <a name="overview"></a>Visão geral
o Azure Disk Encryption é um novo recurso que ajuda a criptografar os discos de suas máquinas virtuais IaaS Windows e Linux. A Azure Disk Encryption aproveita o recurso padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) do Windows e o recurso [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o SO e os discos de dados. A solução é integrada ao [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura de cofre de chaves. A solução também garante que todos os dados em discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.

O Azure Disk Encryption para VMs IaaS do Windows e Linux agora está em **Disponibilidade Geral** em todas as regiões públicas do Azure e AzureGov para VMs Standard e VMs com armazenamento premium.

### <a name="encryption-scenarios"></a>Cenários de criptografia
A solução Azure Disk Encryption dá suporte aos seguintes cenários do cliente:

* Habilitar a criptografia em novas VMs IaaS criadas com base em VHD pré-criptografado e chaves de criptografia
* Habilitar criptografia em novas VMs de IaaS criadas a partir das imagens da Galeria do Azure com suporte
* Habilitar a criptografia em VMs IaaS existentes em execução no Azure
* Desabilitar a criptografia em VMs IaaS do Windows
* Desabilitar a criptografia em unidades de dados para VMs IaaS do Linux
* Ativar criptografia de VMs de disco gerenciado
* Atualizar configurações de criptografia de uma VM de armazenamento não premium criptografada existente
* Backup e restauração de VMs criptografadas, criptografadas com chave de critografia de chave

A solução dá suporte aos seguintes cenários para VMs IaaS quando habilitados no Microsoft Azure:

* Integração com o Cofre da Chave do Azure
* VMs de camada padrão: [ A, D, DS, G, GS, F e VMs IaaS ](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Habilitar criptografia em VMs de IaaS do Windows e Linux e VMs do disco gerenciado a partir das imagens da Galeria do Azure com suporte
* Desativar criptografia no SO e nas unidades de dados para VMs da IaaS do Windows e VMs de disco gerenciado
* Desativar criptografia em unidades de dados para Linux Iaas VMs e VMs de disco gerenciado
* Habilitar a criptografia em VMs IaaS executando o sistema operacional Windows Client
* Habilitar a criptografia em volumes com caminhos de montagem
* Habilitar criptografia em VMs do Linux configuradas com disk striping (RAID) usando mdadm
* Habilitar criptografia no Linux VMs utilizando LVM para discos de dados
* Habilitar a criptografia em VMs do Windows configuradas com os Espaços de Armazenamento
* Atualizar configurações de criptografia de uma VM de armazenamento não premium criptografada existente
* Para todas as regiões do Público do Azure e AzureGov há suporte

A solução não oferece suporte para seguintes cenários, recursos e tecnologia:

* VMs IaaS da camada Básica
* Como desabilitar a criptografia em unidades do sistema operacional para VMs IaaS do Linux
* Desabilitar criptografia em uma unidade de dados se a unidade do SO estiver criptografada para VMs de IaaS do Linux
* VMs de IaaS que são criadas usando o método de criação de VM clássico
* Habilitar criptografia em imagens personalizadas do cliente de VMs de IaaS do Linux e Windows NÃO tem suporte. Habilitar criptografia no disco do SO do LVM do Linux, atualmente não tem suporte. Este suporte será disponibilizado em breve.
* Integração com o Serviço de Gerenciamento de Chaves no local
* Arquivos do Azure (sistema de arquivos compartilhados), NFS (Network File System), volumes dinâmicos e VMs do Windows configuradas com Sistemas RAID baseados em software
* Backup e restauração de VMs criptografadas, criptografadas com chave de criptografia de chave.
* Atualize configurações de criptografia de um armazenamento VM existente criptografado premium.

> [!NOTE]
> O backup e a restauração de VMs criptografadas só têm suporte para VMs que são criptografadas com a configuração KEK. Não há suporte em VMs que são criptografados sem KEK. KEK é um parâmetro opcional que habilita a criptografia de VM. Esse suporte será oferecido em breve.
> Atualizar configurações de criptografia de uma VM de armazenamento não premium criptografada existente. Esse suporte será oferecido em breve.

### <a name="encryption-features"></a>Recursos de criptografia
Quando você habilita e a implanta o Azure Disk Encryption para VMs IaaS do Azure, os seguintes recursos são habilitados, dependendo da configuração fornecida:

* Criptografia do volume do sistema operacional para proteger o volume de inicialização em repouso no armazenamento
* Criptografia de volumes de dados para proteger os volumes de dados em repouso no armazenamento
* Como desabilitar a criptografia em unidades do sistema operacional e de dados para VMs IaaS do Windows
* Desabilitar criptografia nas unidades de dados para VMs de IaaS do Linux (somente se a unidade do SO NÃO ESTIVER criptografada)
* Proteger as chaves de criptografia e segredos em sua assinatura de cofre de chaves
* Comunicando o status de criptografia da VM IaaS criptografada
* Remoção de definições de configuração de criptografia de disco da máquina virtual IaaS
* Backup e restauração de VMs criptografadas usando o serviço de Backup do Azure

> [!NOTE]
> O backup e a restauração de VMs criptografadas só têm suporte para VMs que são criptografadas com a configuração KEK. Não há suporte em VMs que são criptografados sem KEK. KEK é um parâmetro opcional que habilita a criptografia de VM.

o Azure Disk Encryption para VMS IaaS para a solução Windows e Linux inclui:

* A extensão de criptografia de disco para Windows.
* A extensão de criptografia de disco para Linux.
* Os cmdlets do PowerShell de criptografia de disco.
* Os cmdlets da CLI (interface de linha de comando) do Azure para criptografia de disco.
* Os modelos do Azure Resource Manager de criptografia de disco.

Há suporte para a solução de Azure Disk Encryption em VMs IaaS executando o Windows ou o sistema operacional Linux. Para obter mais informações sobre os sistemas operacionais com suporte, confira a seção "Pré-requisitos".

> [!NOTE]
> Não são cobradas taxas adicionais para a criptografia de discos de VM com o Azure Disk Encryption.

### <a name="value-proposition"></a>Proposta de valor
Ao aplicar a solução de gerenciamento de Azure Disk Encryption, você pode atender às seguintes necessidades de negócios:

* As VMs IaaS ficam protegidas em repouso, pois você pode usar a tecnologia de criptografia padrão da indústria para tratar da segurança organizacional e de requisitos de conformidade.
* As VMs IaaS são inicializadas por meio de políticas e chaves controladas pelo cliente, e você pode auditar seu uso no cofre de chaves.

### <a name="encryption-workflow"></a>Fluxo de trabalho de criptografia
Para habilitar a criptografia de disco para VMs do Windows e Linux, faça o seguinte:

1. Escolha um cenário de criptografia entre os cenários de criptografia anteriores.
2. Opte por habilitar a criptografia de disco por meio do modelo do Gerenciador de Recursos de Azure Disk Encryption, cmdlets do PowerShell ou comando da CLI e especifique a configuração de criptografia.

   * Para o cenário do VHD criptografado pelo cliente, carregue o VHD criptografado para sua conta de armazenamento e o material de chave de criptografia para o cofre de chaves. Em seguida, forneça a configuração de criptografia para habilitar a criptografia em uma nova VM IaaS.
   * Para novas VMs que são criadas com base no Marketplace e VMs existentes que já estão em execução no Azure, forneça a configuração de criptografia para habilitar a criptografia na VM IaaS.

3. Conceda acesso à plataforma Azure para ler o material de chave de criptografia (chaves de criptografia BitLocker para sistemas Windows e frase secreta para Linux) de seu cofre de chaves para habilitar a criptografia na VM IaaS.

4. Forneça a identidade de aplicativo Azure AD (Azure Active Directory) para gravar o material de chave de criptografia no cofre de chaves. Isso permite a criptografia na VM IaaS para os cenários mencionados na etapa 2.

5. O Azure atualiza o modelo de serviço de VM com a configuração do cofre de chaves e a criptografia e define sua VM criptografada.

 ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Fluxo de trabalho de descriptografia
Para desabilitar a criptografia de disco para VMs IaaS, conclua as seguintes etapas de alto nível:

1. Opte por desabilitar a criptografia (descriptografia) em uma VM IaaS em execução no Azure por meio do modelo do Resource Manager do Azure Disk Encryption ou de cmdlets do PowerShell e especifique a configuração da descriptografia.

 Esta etapa desabilitará a criptografia do sistema operacional ou o volume de dados ou ambos na VM IaaS do Windows em execução. No entanto, como mencionado na seção anterior, não há suporte para desabilitar a criptografia de disco do sistema operacional para Linux. A etapa de descriptografia é permitida apenas para unidades de dados em VMs do Linux, desde que o disco do SO não esteja criptografado.
2. O Azure atualiza o modelo de serviço da VM e a VM IaaS é marcada como descriptografada. O conteúdo da VM não está criptografado em repouso.

> [!NOTE]
> A operação de desabilitação da criptografia não exclui seu cofre de chaves nem o material da chave de criptografia (chaves de criptografia do BitLocker para sistemas Windows ou a Frase secreta para Linux).
 > Não há suporte para desativação de criptografia de disco de OS para Linux. A etapa de descriptografia é permitida somente para unidades de dados em VMs Linux.
Desabilitar criptografia do disco de dados para Linux não tem suporte se a unidade do SO estiver criptografada.

## <a name="prerequisites"></a>Pré-requisitos
Antes de habilitar o Azure Disk Encryption em VMs IaaS do Azure para os cenários com suporte que foram discutidos na seção "Visão geral", confira os seguintes pré-requisitos:

* Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.
* A Azure Disk Encryption tem suporte nas seguintes versões do Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.
* O Azure Disk Encryption tem suporte nas seguintes versões de cliente Windows: cliente Windows 8 e cliente Windows 10.

> [!NOTE]
> Para o Windows Server 2008 R2, você deve ter o .NET Framework 4.5 instalado antes de habilitar a criptografia no Azure. Você pode instalá-lo com o Windows Update, instalando a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas Windows Server 2008 R2 baseados em x64 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* O Azure Disk Encryption tem suporte nas seguintes versões e distribuições do servidor Linux baseado na Galeria do Azure:

| Distribuição Linux | Versão | Tipo de Volume com Suporte para Criptografia|
| --- | --- |--- |
| Ubuntu | 16.04-LTS-DIÁRIO | SO e Disco de Dados |
| Ubuntu | 14.04.5-LTS-DIÁRIO | SO e Disco de Dados |
| Ubuntu | 12.10 | Disco de dados |
| Ubuntu | 12.04 | Disco de dados |
| RHEL | 7.3 | SO e Disco de Dados |
| RHEL | 7,2 | SO e Disco de Dados |
| RHEL | 6,8 | SO e Disco de Dados |
| RHEL | 6.7 | Disco de dados |
| CentOS | 7.3 | SO e Disco de Dados |
| CentOS | 7.2n | SO e Disco de Dados |
| CentOS | 6,8 | SO e Disco de Dados |
| CentOS | 7.1 | Disco de dados |
| CentOS | 7.0 | Disco de dados |
| CentOS | 6.7 | Disco de dados |
| CentOS | 6.6 | Disco de dados |
| CentOS | 6.5 | Disco de dados |
| openSUSE | 13.2 | Disco de dados |
| SLES | 12 SP1 | Disco de dados |
| SLES | 12-SP1 (Premium) | Disco de dados |
| SLES | HPC 12 | Disco de dados |
| SLES | 11-SP4 (Premium) | Disco de dados |
| SLES | 11 SP4 | Disco de dados |

* O Azure Disk Encryption exige que seu cofre de chaves e as VMs residam na mesma região e assinatura do Azure.

> [!NOTE]
> Configurá os recursos em regiões separadas causa uma falha na habilitação do recurso de Azure Disk Encryption.

* Para instalar e configurar o cofre de chaves para o Azure Disk Encryption, consulte a seção **Definir e configurar o cofre de chaves para o Azure Disk Encryption** na seção *Pré-requisitos* deste artigo.
* Para instalar e configurar o aplicativo Azure AD no Azure Active Directory para Azure Disk Encryption, consulte a seção **Configurar o aplicativo Azure AD no Azure Active Directory** na seção de *Pré-requisitos* deste artigo.
* Para instalar e configurar a política de acesso do cofre de chaves para o aplicativo Azure AD, consulte a seção **Configurando a política de acesso ao cofre de chaves para o aplicativo Azure AD** na seção de *Pré-requisitos* deste artigo.
* Para preparar um VHD do Windows criptografado previamente, consulte a seção  **Preparar um VHD do Windows criptografado previamente**  no  *Apêndice* .
* Para preparar um VHD do Linux criptografado previamente, consulte a seção  **Preparar um VHD do Linux criptografado previamente**  no  *Apêndice* .
* A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para torná-los disponíveis para a máquina virtual quando ela for inicializada e descriptografar o volume de sistema operacional da máquina virtual. Para conceder permissões para a plataforma Windows Azure, defina a propriedade **EnabledForDiskEncryption** no cofre de chaves. Para obter mais infprmações, consulte **Definir e configurar o cofre de chaves de Azure Disk Encryption** no Apêndice.
* O segredo do cofre de chaves e as URLs KEK devem ter controle de versão. O Azure impõe essa restrição de controle de versão. Para um segredo válido e URLs KEK, confira os seguintes exemplos:

  * Exemplo de URL de segredo válida: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de URL KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* o Azure Disk Encryption não dá suporte à especificação de números de portas como parte de segredos do cofre de chaves e URLs KEK. Para exemplos de URLs do Key Vault com e sem suporte, consulte:

  * URL do cofre de chaves inaceitável *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves aceitável: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Para habilitar o recurso de Azure Disk Encryption, as VMs IaaS devem atender aos seguintes requisitos de configuração do ponto de extremidade de rede:
  * Para obter um token para se conectar ao seu cofre de chaves, a VM IaaS deve ser capaz de se conectar a um ponto de extremidade do Azure Active Directory, \[login.microsoftonline.com\].
  * Para gravar as chaves de criptografia no cofre de chaves, a VM IaaS deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  * A VM IaaS deve ser capaz de se conectar a um ponto de extremidade do armazenamento do Azure que hospeda o repositório de extensão do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.

  > [!NOTE]
  > Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs.
  >
  >Para configurar e acessar Azure Key Vault protegido por um firewall (https://docs.microsoft.com/en-us/azure/key-vault/key-vault-access-behind-firewall)

* Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Não há suporte para o Azure Disk Encryption no [SDK do Azure PowerShell versão 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se estiver recebendo um erro relacionado ao uso do PowerShell 1.1.0, confira [Erro do Azure Disk Encryption relacionado ao Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Para executar qualquer comando da CLI do Azure e associá-lo à sua assinatura do Azure, primeiro você deve instalar a CLI do Azure:
  * Para instalar a CLI do Azure e associá-la à sua assinatura do Azure, confira [Como instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
  * Para usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager, confira [Comandos da CLI do Azure no modo do Gerenciador de Recursos](../virtual-machines/azure-cli-arm-commands.md).

* Ao criptografar um disco gerenciado, é um pré-requisito obrigatório tirar um instantâneo do disco gerenciado ou então fazer um backup do disco fora do Azure Disk Encryption antes de habilitar a criptografia.  Sem um backup em vigor, qualquer falha inesperada durante a criptografia pode tornar o disco e a VM inacessíveis e sem uma opção de recuperação.  Set-AzureRmVMDiskEncryptionExtension atualmente não faz backup de Managed Disks e gerará um erro se usado em um disco gerenciado, a menos que o parâmetro -skipVmBackup tenha sido especificado.  O uso desse parâmetro não é seguro, a menos que um backup já tenha sido feito fora do Azure Disk Encryption.   Quando o parâmetro -skipVmBackup é especificado, o cmdlet não faz um backup do disco gerenciado antes da criptografia.  Por esse motivo, ele é considerado um pré-requisito obrigatório para verificar se de um backup da VM com disco gerenciado está em vigor antes de habilitar o Azure Disk Encryption, caso uma recuperação posterior seja necessária.  
> [!NOTE]
 > O parâmetro -skipVmBackup nunca deve ser usado, a menos que um instantâneo ou backup já tenham sido feitos fora do Azure Disk Encryption. 

* A solução de Azure Disk Encryption usa o protetor de chave externa BitLocker para VMs IaaS do Windows. Para VMs ingressadas no domínio, NÃO envie nenhuma política de grupo que imponha protetores de TPM. Para obter informações sobre a política de grupo "Permitir BitLocker sem um TPM compatível", confira [Referência de política de grupo do BitLocker](https://technet.microsoft.com/library/ee706521).
* A política do BitLocker para máquinas virtuais ingressadas no domínio com a política de grupo personalizado deve incluir a seguinte configuração: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` o Azure Disk Encryption falhará quando as configurações de política de grupo personalizadas para o Bitlocker forem incompatíveis. Em computadores que não tinham a configuração de política correta, pode ser necessário aplicar a nova política, forçar a atualização da nova política (gpupdate.exe /force) e, em seguida, reiniciar.  
* Para criar um aplicativo Azure AD, criar um cofre de chaves ou configurar um cofre de chaves existente e habilitar a criptografia, confira o [script do PowerShell de pré-requisito de Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Para configurar os pré-requisitos de criptografia de disco usando a CLI do Azure, confira [este script Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Para usar o serviço de Backup do Azure para fazer backup e restaurar VMs criptografadas quando a criptografia estiver habilitada com o Azure Disk Encryption, criptografe as VMs usando a configuração da chave de Azure Disk Encryption. O serviço de Backup dá suporte a VMs que são criptografadas usando apenas a configuração KEK. Consulte [Como fazer backup e restaurar máquinas virtuais criptografadas com criptografia do Backup do Azure ](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).

* Ao criptografar um volume do sistema operacional Linux, observe que atualmente uma reinicialização de VM é necessária no final do processo. Isso pode ser feito por meio do portal, do PowerShell ou da CLI.   Para acompanhar o progresso de criptografia, sonde periodicamente a mensagem de status retornada por Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Uma vez concluída a criptografia, a mensagem de status retornada por este comando indicará isso.  Por exemplo, "ProgressMessage: disco do sistema operacional criptografado com êxito, reinicialize a VM", Nesse ponto, a VM pode ser reiniciada e usada.  

* O Azure Disk Encryption para Linux requer que os discos de dados tenham um sistema de arquivos montado em Linux antes da criptografia

* Discos de dados montados recursivamente não têm suporte pelo Azure Disk Encryption para Linux. Por exemplo, se o sistema de destino tiver montado um disco em foo/bar e, em seguida, outro em /foo/bar/baz, a criptografia de /foo/bar/baz terá êxito, mas haverá falha na criptografia de foo/bar. 

* Somente haverá suporte para o Azure Disk Encryption nas imagens com suporte da galeria do Azure que atenderem aos pré-requisitos acima mencionados. Imagens personalizadas do cliente não têm suporte devido a esquemas de partição personalizados e comportamentos de processo que podem existir nessas imagens. Além disso, até mesmo VMs baseadas em imagem de galeria que inicialmente atendiam aos pré-requisitos, mas que foram modificadas após a criação, podem ser incompatíveis.  Por esse motivo, o procedimento sugerido para criptografar uma VM Linux é iniciar de uma imagem da galeria limpa, criptografar a VM e, em seguida, adicionar software personalizado ou dados à VM conforme necessário.  

* Volume de dados local do Azure Disk Encryption – Volume Bek para Windows e /mnt/azure_bek_disk para VMs IaaS do Linux para guardar a chave de criptografia com segurança. Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS. O arquivo LEIAME incluído no volume contém detalhes adicionais.

> [!NOTE]
> O backup e a restauração de VMs criptografadas só têm suporte para VMs que são criptografadas com a configuração KEK. Não há suporte em VMs que são criptografados sem KEK. KEK é um parâmetro opcional que permite VM.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Configurar o aplicativo Azure AD no Azure Active Directory
Quando você precisa habilitar a criptografia em uma VM em execução no Azure, o Azure Disk Encryption gera e grava as chaves de criptografia no cofre de chaves. O gerenciamento de chaves de criptografia no cofre de chaves requer a autenticação do Azure AD.

Para essa finalidade, crie um aplicativo Azure AD. Você pode encontrar etapas detalhadas para registrar um aplicativo na seção "Obter uma identidade para o aplicativo" da postagem de blog [Azure Key Vault - passo a passo](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Esta postagem também contém vários exemplos úteis para configurar o cofre de chaves. Para fins de autenticação, você pode usar a autenticação baseada em segredo do cliente ou a autenticação Azure AD baseada em certificado do cliente.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Autenticação do Azure AD baseada em segredo do cliente
As seções a seguir podem ajudá-lo a configurar uma autenticação baseada em segredo do cliente para o Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Criar um aplicativo Azure AD usando o Azure PowerShell
Use o seguinte cmdlet do PowerShell para criar um aplicativo Azure AD:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId é o ClientID do Azure AD e $aadClientSecret é o segredo do cliente que deve ser usado posteriormente para habilitar o Azure Disk Encryption. Proteja adequadamente o segredo do cliente no Azure AD.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-classic-portal"></a>Configurando a ID do cliente do Azure AD e o segredo do portal clássico do Azure
Você também pode configurar a ID de cliente do Azure AD e o segredo usando o [portal clássico do Azure]( https://manage.windowsazure.com). Para executar essa tarefa, faça o seguinte:

1. Clique na guia **Active Directory**.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Clique em **Adicionar Aplicativo** e digite o nome do aplicativo.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Clique no botão de seta e configure as propriedades do aplicativo.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Clique na marca de seleção no canto inferior esquerdo para concluir. A página de configuração de aplicativo é exibida, e a ID de cliente do Azure AD é exibida na parte inferior da página.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. 5. Salve o segredo do cliente do Azure AD clicando no botão **Salvar**. Observe o segredo do cliente do Azure AD na caixa de texto de chaves. Proteja-o adequadamente.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)

 > [!NOTE]
 > O fluxo anterior não tem suporte no portal clássico do Azure.

##### <a name="use-an-existing-application"></a>Usar um aplicativo existente
Para executar os seguintes comandos, obtenha e use o [módulo do Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Os comandos a seguir devem ser executados em uma nova janela do PowerShell. Não use o Azure PowerShell ou a janela do Azure Resource Manager para executar os comandos. Recomendamos essa abordagem porque esses cmdlets estão no módulo MSOnline ou no Azure PowerShell AD.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Autenticação baseada em certificado do Azure AD
> [!NOTE]
> No momento, não há suporte para a autenticação baseada em certificado do Azure AD em VMs do Linux.

As seções a seguir mostram como configurar uma autenticação baseada em certificado para o Azure AD.

##### <a name="create-an-azure-ad-application"></a>Criar um aplicativo Azure AD
Para criar um aplicativo Azure AD, execute os seguintes cmdlets do PowerShell:

> [!NOTE]
> Substitua a cadeia de caracteres `yourpassword` a seguir por sua senha segura e proteja a senha.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Depois de concluir essa etapa, carregue um arquivo PFX no cofre de chaves e habilite a política de acesso necessária para implantar esse certificado em uma máquina virtual.

##### <a name="use-an-existing-azure-ad-application"></a>Usar um aplicativo existente do Azure AD
Se estiver configurando a autenticação baseada em certificado para um aplicativo existente, use os cmdlets do PowerShell mostrados aqui. Execute-os em uma nova janela do PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Depois de concluir esta etapa, carregue um arquivo PFX para o cofre de chaves e habilite a política de acesso necessária para implantar o certificado em uma VM.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Carregar um arquivo PFX no cofre de chaves
Para obter uma explicação detalhada desse processo, confira o [Blog oficial da equipe do Azure Key Vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). No entanto, os seguintes cmdlets do PowerShell são tudo de que você precisa para a tarefa. Execute-os no console do Azure PowerShell.

> [!NOTE]
> Substitua a cadeia de caracteres `yourpassword` a seguir por sua senha segura e proteja a senha.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Implante um certificado no cofre de chaves em uma VM existente
Depois de terminar de carregar o PFX, implante um certificado no cofre de chaves em uma VM existente com o seguinte:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Configurar a política de acesso do cofre de chaves para o aplicativo Azure AD
Seu aplicativo Azure AD precisa de direitos para acessar as chaves ou os segredos no cofre. Use o cmdlet [`Set-AzureKeyVaultAccessPolicy`](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) para conceder permissões para o aplicativo usando a ID do cliente (que foi gerada quando o aplicativo foi registrado) como o valor do parâmetro _–ServicePrincipalName_. Para saber mais, confira a postagem de blog [Azure Key Vault - passo a passo](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Aqui está um exemplo de como executar essa tarefa por meio do PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> o Azure Disk Encryption exige que você configure as seguintes políticas de acesso ao aplicativo de cliente do Azure AD: permissões _WrapKey_ e _Set_.

## <a name="terminology"></a>Terminologia
Para entender alguns dos termos comuns usados por essa tecnologia, use a seguinte tabela de terminologia:

| Terminologia | Definição |
| --- | --- |
| AD do Azure | Azure AD significa [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Uma conta do Azure AD é um pré-requisito para autenticar, armazenar e recuperar segredos do cofre de chaves. |
| Cofre da Chave do Azure | O Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards), que ajuda a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, confira a documentação do [Key Vault](https://azure.microsoft.com/services/key-vault/). |
| ARM | Gerenciador de Recursos do Azure |
| BitLocker |O [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pela indústria e usada para habilitar a criptografia de disco em VMs de IaaS do Windows. |
| BEK | As chaves de criptografia do BitLocker são usadas para criptografar o volume de inicialização do sistema operacional e os volumes de dados. As chaves do BitLocker são protegidas em um cofre de chaves como segredos. |
| CLI | Confira [Interface de linha de comando do Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) é o subsistema de criptografia de disco transparente baseado em Linux usado para habilitar a criptografia de disco nas VMs de IaaS do Linux. |
| KEK | A criptografia de chave é a chave assimétrica (RSA 2048) que pode ser usada para proteger ou encapsular o segredo. Você pode fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais detalhes, confira a [documentação do Azure Key Vault](https://azure.microsoft.com/services/key-vault/). |
| Cmdlets de DNS | Confira [Cmdlets do Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Instalar e configurar o cofre de chaves de Azure Disk Encryption
o Azure Disk Encryption ajuda a proteger as chaves e os segredos da criptografia de disco no cofre de chaves. Para configurar o cofre de chaves para o Azure Disk Encryption, conclua as etapas em cada uma das seções a seguir.

#### <a name="create-a-key-vault"></a>Criar um cofre de chave
Para criar um cofre de chaves, use uma das seguintes opções:

* [Modelo "101-Key-Vault-Create" do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Cmdlets do cofre de chaves do Azure PowerShell](/powershell/module/azurerm.keyvault/#key_vault)
* Gerenciador de Recursos do Azure
* Como [ Proteger o key vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Se já tiver configurado um cofre de chaves para sua assinatura, vá para a próxima seção.

![Cofre da Chave do Azure](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Configurar uma chave de criptografia de chave (opcional)
Se quiser usar um KEK para uma camada adicional de segurança para as chaves de criptografia do BitLocker, adicione o KEK ao cofre de chaves. Use o cmdlet [`Add-AzureKeyVaultKey`](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) para criar uma chave de criptografia de chave no cofre de chaves. Você também pode importar a KEK do HSM do gerenciamento de chaves local. Para obter mais detalhes, confira a [Documentação do Key Vault](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Você pode adicionar o KEK indo para o Azure Resource Manager ou usando a interface do cofre de chaves.

![Cofre da Chave do Azure](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Definir permissões de cofre de chaves
A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para disponibilizá-los para a máquina virtual para inicialização e descriptografar os volumes. Para conceder permissões para a plataforma Windows Azure, defina a propriedade **EnabledForDiskEncryption** no cofre de chaves usando o cmdlet do PowerShell do cofre de chaves:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Você também pode definir a propriedade **EnabledForDiskEncryption** acessando o [Explorador de Recursos do Azure](https://resources.azure.com).

Como mencionado anteriormente, você deve definir a propriedade **EnabledForDiskEncryption** em seu cofre de chaves. Caso contrário, a implantação falhará.

Você pode configurar políticas de acesso para o aplicativo Azure AD na interface do cofre de chaves, como mostrado aqui:

![Cofre da Chave do Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Cofre da Chave do Azure](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Na guia **Políticas de acesso avançado**, verifique se o cofre de chaves está habilitado para o Azure Disk Encryption:

![Cofre de chaves do Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Cenários de implantação de criptografia de disco e experiências de usuário
Você pode habilitar muitos cenários de criptografia de disco, e as etapas podem variar de acordo com o cenário. As seções a seguir abordam os cenários mais detalhadamente.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Habilitar a criptografia em novas VMs IaaS criadas por meio do Marketplace
Você pode habilitar a criptografia de disco em uma nova VM IaaS Windows por meio do Marketplace no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**, insira a configuração de criptografia na folha **Parâmetros** e clique em **OK**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique em **Criar** para habilitar a criptografia na VM IaaS nova.

> [!NOTE]
> Esse modelo cria uma nova VM do Windows criptografada que usa a imagem de galeria do Windows Server 2012.

Você pode habilitar a criptografia de disco em uma nova VM IaaS do RedHat Linux 7.2 com uma matriz RAID 0 de 200 GB usando esse [modelo do Gerenciador de Recursos](https://aka.ms/fde-rhel). Após implantar o modelo, verifique o status de criptografia da VM usando o cmdlet `Get-AzureRmVmDiskEncryptionStatus`, conforme descrito em [Criptografando unidade do SO em uma VM do Linux em execução](#encrypting-os-drive-on-a-running-linux-vm). Quando o computador retornar um status _VMRestartPending_, reinicie a VM.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos para novas VMs do cenário do Marketplace usando a ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| adminUserName | Especifique um nome de usuário para a máquina virtual. |
| adminPassword | Senha de usuário administrador para a máquina virtual. |
| newStorageAccountName | Nome da conta de armazenamento para armazenar VHDs do sistema operacional e de dados. |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G. |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. |
| subnetName | Nome da sub-rede na VNet à qual a NIC da VM deve pertencer. |
| AADClientID | A ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| keyVaultURL | URL do cofre de chaves no qual a chave do BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL da chave de criptografia de chave que é usada para criptografar a chave gerada do BitLocker (opcional). |
| keyVaultResourceGroup | Grupo de recursos do cofre de chaves. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |

> [!NOTE]
> _KeyEncryptionKeyURL_ é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (frase secreta) no cofre de chaves.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Habilite a criptografia na nova VM IaaS criada usando VHD criptografado pelo cliente e chaves de criptografia
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam detalhadamente o modelo do Gerenciador de Recursos e comandos da CLI.

Siga as instruções em uma dessas seções para preparar as imagens pré-criptografadas que podem ser usadas no Azure. Depois que a imagem for criada, você poderá usar as etapas na próxima seção para criar uma VM do Azure criptografada.

* [Preparar um VHD do Windows previamente criptografado](#preparing-a-pre-encrypted-windows-vhd)
* [Preparar um VHD Linux previamente criptografado](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Usando o modelo do Gerenciador de Recursos
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**, insira a configuração de criptografia na folha **Parâmetros** e clique em **OK**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique no botão **Criar** para habilitar a criptografia na VM IaaS nova.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos para seu VHD criptografado:

| Parâmetro | Descrição |
| --- | --- |
| newStorageAccountName | Nome da conta de armazenamento para armazenar o VHD de sistema operacional. Esta conta de armazenamento já deve ter sido criada no mesmo grupo de recursos e no mesmo local da VM. |
| osVhdUri | URI do VHD do sistema operacional da conta de armazenamento. |
| osType | Tipo de produto do sistema operacional (Windows/Linux). |
| virtualNetworkName | Nome da VNet à qual a NIC da VM deve pertencer. O nome já deve ter sido criado no mesmo grupo de recursos e no mesmo local que a VM. |
| subnetName | O nome da sub-rede na VNet à qual a VM NIC deve pertencer. |
| vmSize | O tamanho da VM. Atualmente, há suporte somente para séries Standard A, D e G. |
| keyVaultResourceID | O ResourceID que identifica o recurso de cofre de chaves no Azure Resource Manager. Você pode obtê-lo usando o cmdlet do PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | URL da chave de criptografia de disco que é configurada no cofre de chaves. |
| keyVaultKekUrl | URL da chave de criptografia de chave para criptografar a chave de criptografia de disco gerada. |
| vmName | Nome da VM IaaS. |

#### <a name="using-powershell-cmdlets"></a>Usando os cmdlets do PowerShell
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [`Set-AzureRmVMOSDisk`](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Usando comandos da CLI
Para habilitar a criptografia de disco para esse cenário usando os comandos da CLI, faça o seguinte:

1. Definir políticas de acesso no cofre de chaves:

   * Defina o sinalizador **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Defina permissões para o aplicativo Azure AD para gravar segredos no cofre de chaves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Para habilitar a criptografia em uma VM existente/em execução, digite:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obtenha o status de criptografia:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Para habilitar a criptografia em uma nova VM de seu VHD criptografado, use os seguintes parâmetros com o comando `azure vm create`:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Habilitar a criptografia na VM IaaS do Windows existente ou em execução no Azure
Nesse cenário, você pode habilitar a criptografia usando o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As seções a seguir explicam detalhadamente como habilitá-lo usando o modelo do Gerenciador de Recursos e comandos da CLI.

#### <a name="using-the-resource-manager-template"></a>Usando o modelo do Gerenciador de Recursos
Você pode habilitar a criptografia de disco em VMs Windows IaaS existentes ou em execução no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**, insira a configuração de criptografia na folha **Parâmetros** e clique em **OK**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique em **Criar** para habilitar a criptografia em VM IaaS existente ou em execução.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos existente ou VMs em execução que usam uma ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| keyVaultName | Nome do cofre de chaves no qual a chave do BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL da chave de criptografia de chaves que é usada para criptografar a chave gerada do BitLocker. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |

> [!NOTE]
> _KeyEncryptionKeyURL_ é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (segredo de criptografia BitLocker) no cofre de chaves.

#### <a name="using-powershell-cmdlets"></a>Usando os cmdlets do PowerShell
Para obter informações sobre como habilitar a criptografia com o Azure Disk Encryption usando cmdlets do PowerShell, confira as postagens de blog [Explorar Azure Disk Encryption com o Azure PowerShell - parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [Explorar Azure Disk Encryption com o Azure PowerShell - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Usando comandos da CLI
Para habilitar a criptografia em uma VM IaaS do Windows existente ou em execução no Azure usando os comandos da CLI, faça o seguinte:

1. Para definir políticas de acesso no cofre de chaves:
   * Defina o sinalizador **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Defina permissões para o aplicativo Azure AD para gravar segredos no cofre de chaves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Para habilitar a criptografia em uma VM existente ou em execução:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Para obter o status de criptografia:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Para habilitar a criptografia em uma nova VM de seu VHD criptografado, use os seguintes parâmetros com o comando `azure vm create`:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Habilitar a criptografia em uma VM IaaS do Linux existente ou em execução no Azure
Você pode habilitar a criptografia de disco em uma VM Linux IaaS existente ou em execução no Azure usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Clique em **Implantar no Azure** no modelo de início rápido do Azure, insira a configuração de criptografia na folha **Parâmetros** e clique em **OK**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique em **Criar** para habilitar a criptografia em VM IaaS existente ou em execução.

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos existente ou VMs em execução que usam uma ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID do cliente do aplicativo Azure AD que tem permissões para gravar segredos no cofre de chaves. |
| AADClientSecret | Segredo do cliente do aplicativo AD do Azure que tem permissões para gravar segredos para o cofre de chaves. |
| keyVaultName | Nome do cofre de chaves no qual a chave do BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL da chave de criptografia de chaves que é usada para criptografar a chave gerada do BitLocker. Esse parâmetro será opcional se você selecionar **nokek** na lista suspensa UseExistingKek. Se selecionar **kek** na lista suspensa UseExistingKek, você deverá inserir o valor _keyEncryptionKeyURL_. |
| volumeType | Tipo de volume em que a operação de criptografia é executada. Os valores válidos com suporte são _OS_ ou _Todos_ (para RHEL 7.2, CentOS 7.2 e Ubuntu 16.04) e _Dados_ (para todas as outras distribuições). |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão sempre que uma operação de criptografia de disco for executada na mesma VM. |
| vmName | Nome da VM em que a operação de criptografia deve ser executada. |
| Senha | Digite uma frase secreta forte como chave de criptografia de dados. |

> [!NOTE]
> _KeyEncryptionKeyURL_ é um parâmetro opcional. Você pode usar seu próprio KEK para proteger ainda mais a chave de criptografia de dados (frase secreta) no cofre de chaves.

#### <a name="cli-commands"></a>Comandos de CLI
Você pode habilitar a criptografia de disco em seu VHD criptografado instalando e usando o [comando da CLI](../cli-install-nodejs.md). Para habilitar a criptografia em VMs IaaS Linux existentes ou em execução no Azure usando os comandos da CLI, faça o seguinte:

1. Definir políticas de acesso no cofre de chaves:

 * Defina o sinalizador **EnabledForDiskEncryption**:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Defina permissões para o aplicativo Azure AD para gravar segredos no cofre de chaves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Para habilitar a criptografia em uma VM existente ou em execução:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obtenha o status de criptografia:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Para habilitar a criptografia em uma nova VM de seu VHD criptografado, use os seguintes parâmetros com o comando `azure vm create`:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Obter o status da criptografia de uma VM IaaS criptografada
É possível obter o status da criptografia usando o Azure Resource Manager, [cmdlets do PowerShell](/powershell/azure/overview) ou comandos da CLI. As seções a seguir explicam como usar o portal clássico do Azure e os comandos da CLI para obter o status de criptografia.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Obter o status da criptografia de uma VM do Windows criptografada usando o Azure Resource Manager
Você pode obter o status de criptografia da VM IaaS do Azure Resource Manager fazendo o seguinte:

1. Entre no [portal clássico do Azure](https://portal.azure.com/) e clique em **Máquinas virtuais** no painel esquerdo para ver uma exibição resumida das máquinas virtuais em sua assinatura. Você pode filtrar a exibição de máquinas virtuais selecionando o nome da assinatura na lista suspensa **Assinatura**.

2. Na parte superior da página **Máquinas virtuais**, clique em **Colunas**.

3. Na folha **Escolher coluna**, selecione **Criptografia de Disco**e clique em **Atualizar**. Você deve ver a coluna de criptografia de disco mostrando o estado de criptografia _Habilitado_ ou _Desabilitado_ para cada VM conforme mostrado na seguinte figura:

 ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Obter o status de criptografia de uma VM IaaS (Windows/Linux) criptografada usando o cmdlet do PowerShell de criptografia de disco
Você pode obter o status de criptografia da VM IaaS por meio do cmdlet do PowerShell de criptografia de disco `Get-AzureRmVMDiskEncryptionStatus`. Para obter as configurações de criptografia para sua VM, digite o seguinte:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Você pode inspecionar a saída de _Get-AzureRmVMDiskEncryptionStatus_ para obter URLs de criptografia de chave.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

O valor das configurações OSVolumeEncrypted e DataVolumesEncrypted é definido como _Encrypted_, mostrando que ambos os volumes são criptografados com o Azure Disk Encryption. Para obter informações sobre como habilitar a criptografia com o Azure Disk Encryption usando cmdlets do PowerShell, confira as postagens de blog [Explorar Azure Disk Encryption com o Azure PowerShell - parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [Explorar Azure Disk Encryption com o Azure PowerShell - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Em VMs Linux, leva três ou quatro minutos para que o cmdlet `Get-AzureRmVMDiskEncryptionStatus` relate o status de criptografia.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Obter status da criptografia da VM IaaS do comando CLI de criptografia de disco
Você pode obter o status de criptografia da VM IaaS usando o comando da CLI de criptografia de disco `azure vm show-disk-encryption-status`. Para obter as configurações de criptografia de sua VM, digite na sessão do Azure CLI:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Desabilitar a criptografia em VMs IaaS do Windows em execução
Você pode desabilitar a criptografia em uma VM IaaS do Windows ou Linux em execução por meio do modelo do Resource Manager do Azure Disk Encryption ou de cmdlets do PowerShell e especificar a configuração da descriptografia.

##### <a name="windows-vm"></a>VM Windows
A etapa de desabilitar a criptografia desabilita a criptografia do sistema operacional ou volume de dados na VM IaaS do Windows em execução. Você não pode desabilitar o volume do sistema operacional e deixar o volume de dados criptografado. Quando a etapa de desabilitação da criptografia for executada, o modelo de implantação clássico do Azure atualizará o modelo de serviço da VM e a VM IaaS do Windows é marcada como descriptografada. O conteúdo da VM não está criptografado em repouso. A descriptografia não exclui o cofre de chaves e o material da chave de criptografia (chaves de criptografia do BitLocker para Windows e frase secreta para Linux).

##### <a name="linux-vm"></a>VM Linux
A etapa de desabilitação da criptografia desabilita a criptografia do volume de dados na VM IaaS do Linux em execução. Esta etapa somente funciona se o disco do OS não estiver criptografado.

> [!NOTE]
> Não é permitido desabilitar a criptografia no disco do sistema operacional em VMs do Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Desabilitar a criptografia em uma VM IaaS existente ou em execução
Você pode desabilitar a criptografia de disco em VMs IaaS do Windows em execução usando o [modelo do Gerenciador de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**, insira a configuração de descriptografia na folha **Parâmetros** e clique em **OK**.

2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, os termos legais e o contrato e clique em **Criar** para habilitar a criptografia na VM IaaS nova.

Para VMs do Linux, você pode desabilitar a criptografia usando o modelo [Desabilitar a criptografia em uma VM do Linux em execução](https://aka.ms/decrypt-linuxvm).

A tabela a seguir lista os parâmetros de modelo do Gerenciador de Recursos para desabilitar a criptografia em uma VM IaaS em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome da VM em que a operação de criptografia deve ser executada.
| volumeType | Tipo de volume em que uma operação de descriptografia é executada. Os valores válidos são _OS_, _Data_ e _All_. Não é possível desabilitar a criptografia no volume de inicialização/sistema operacional da VM IaaS do Windows em execução sem desabilitar a criptografia no volume _Data_. Observe também que não é permitido desabilitar a criptografia no disco do sistema operacional em VMs Linux. |
| sequenceVersion | Versão de sequência da operação de BitLocker. Aumente esse número de versão cada vez que uma operação de descriptografia de disco for executada na mesma VM. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Desabilitar a criptografia em uma VM IaaS existente ou em execução
Para desabilitar a criptografia em uma VM IaaS existente ou em execução usando o cmdlet do PowerShell, confira [`Disable-AzureRmVMDiskEncryption`](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Esse cmdlet dá suporte a VMs do Linux e do Windows. Para desabilitar a criptografia, ele instala uma extensão na máquina virtual. Se o parâmetro _Name_ não for especificado, será criada uma extensão com o nome padrão _AzureDiskEncryption for Windows VMs_.

Em VMs do Linux, a extensão AzureDiskEncryptionForLinux é usada.

> [!NOTE]
> Este cmdlet reinicia a máquina virtual.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Habilitar criptografia na VM da IaaS criptografada previamente com o Azure Managed Disk
Use o modelo ARM do Azure Managed Disk para criar uma VM criptografada a partir de um VHD criptografado previamente usando o modelo ARM localizado em   
[Criar um novo disco gerenciado criptografado a partir de um blob de armazenamento/VHD criptografado] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Habilitar criptografia em uma nova VM de IaaS Linux com o Azure Managed Disk
Use o modelo ARM do Azure Managed para criar uma nova VM de IaaS Linux criptografada usando o modelo ARM localizado em   
[Implantação do RHEL 7.2 com criptografia de disco completo] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Habilitar criptografia em uma nova VM de IaaS do Windows com o Azure Managed Disk
 Use o modelo ARM do Azure Managed para criar uma nova VM de IaaS Linux criptografada usando o modelo ARM localizado em   
 [Criar uma nova VM do Disco Gerenciado de IaaS do Windows a partir da imagem da galeria] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >É obrigatório criar um instantâneo e/ou fazer backup de uma instância VM baseada em disco gerenciado fora do Azure Disk Encryption e antes de habilitá-lo.  Um instantâneo do disco gerenciado pode ser criado do portal ou usando o Backup do Azure.  Backups asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a criptografia.  Depois que um backup é feito, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser usado para criptografar Managed Disks especificando o parâmetro -skipVmBackup.  Este comando falhará em VMs baseadas em disco gerenciado até que um backup tenha sido feito e esse parâmetro tenha sido especificado.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Atualizar configurações de criptografia de uma VM de armazenamento não premium criptografada existente
  Use interfaces com suporte de criptografia de disco do Azure existentes para executar VM [modelos ARM, PS cmdlets ou CLI] para atualizar as configurações de criptografia como segredo/ID de cliente AAD, chave de criptografia chave [KEK], chave de criptografia BitLocker para VM do Windows ou frase secreta para VM do Linux, etc. A configuração de criptografia de atualização oferece suporte apenas para VMs com suporte de armazenamento não premium. Não oferece suporte para VMs com suporte de armazenamento premium.

## <a name="appendix"></a>Apêndice
### <a name="connect-to-your-subscription"></a>Conecte-se as suas assinaturas
Antes de prosseguir, revise a seção  *Pré-requisitos*  neste artigo. Depois de garantir que todos os pré-requisitos foram atendidos, conecte-se à sua assinatura fazendo o seguinte:

1. Inicie uma sessão do Azure PowerShell e entre em sua conta do Azure com o seguinte comando:

    `Login-AzureRmAccount`

2. Se tiver várias assinaturas e quiser especificar uma a ser usada, digite o seguinte para ver as assinaturas da sua conta:

    `Get-AzureRmSubscription`

3. Para especificar a assinatura que você quer usar, digite:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Para verificar se a assinatura configurada está correta, digite:

    `Get-AzureRmSubscription`

5. Para confirmar que os cmdlets de Azure Disk Encryption estão instalados, digite:

    `Get-command *diskencryption*`

6. A saída a seguir confirma a instalação do PowerShell de Azure Disk Encryption:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparar um VHD do Windows previamente criptografado
As seções a seguir são necessárias para preparar um VHD do Windows previamente criptografado para implantação como um VHD criptografado no Azure IaaS. Use as informações para preparar e inicializar uma nova VHD (VM do Windows) no Azure Site Recovery ou no Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política de grupo para permitir não TPM na proteção do sistema operacional
Configure a política de grupo do BitLocker **Criptografia de Unidade de Disco BitLocker**, que você encontrará em **Política de Computador Local** > **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows**. Altere essa configuração para: **Unidades do Sistema Operacional** > **Exigir autenticação adicional na inicialização** > **Permitir BitLocker sem TPM compatível**, como mostrado na seguinte figura:

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Instalar componentes de recursos do BitLocker
Para o Windows Server 2012 e versões posteriores, use o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, use o seguinte comando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparar o volume do sistema operacional para o BitLocker usando `bdehdcfg`
Para compactar a partição do sistema operacional e preparar o computador para o BitLocker, execute o seguinte comando:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume do sistema operacional usando o BitLocker
Use o comando [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) para habilitar a criptografia no volume de inicialização usando um protetor de chave externo. Também coloque a chave externa (arquivo .bek) na unidade ou no volume. A criptografia é habilitada no volume de inicialização/sistema após a próxima reinicialização.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare a VM com um VHD de dados/recursos separado para obter a chave externa usando o BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Criptografando uma unidade do sistema operacional em uma VM do Linux em execução
Há suporte para a criptografia da unidade do sistema operacional em uma VM do Linux em execução nas seguintes distribuições:

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para a criptografia de disco do sistema operacional

* A VM deve ser criada com base na imagem do Marketplace no Azure Resource Manager.
* VM do Azure com, no mínimo, 4 GB de RAM (o tamanho recomendável é de 7 GB).
* (Para RHEL e CentOS) Desabilite o SELinux. Para desabilitar SELinux, confira "4.4.2. Desabilitando o SELinux" no [Guia do Administrador e Usuário do SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na VM.
* Depois de desabilitar o SELinux, reinicialize a VM pelo menos uma vez.

##### <a name="steps"></a>Etapas
1. Crie uma VM usando uma das distribuições especificadas anteriormente.

 Para o CentOS 7.2, há suporte para a criptografia de disco do sistema operacional por meio de uma imagem especial. Para usar essa imagem, especifique "7.2n" como o SKU quando você criar a VM:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configure a VM de acordo com suas necessidades. Se for criptografar todas as unidades (sistema operacional + dados), as unidades de dados precisarão ser especificadas e deverão ser montadas em /etc/fstab.

 > [!NOTE]
 > Use UUID=... para especificar unidades de dados em/etc/fstab em vez de especificar o nome do dispositivo de blocos (por exemplo, /dev/sdb1). Durante a criptografia, a ordem das é alterada na VM. Se a VM se basear em uma ordem específica de dispositivos de blocos, ela não conseguirá montá-los após a criptografia.

3. Saia das sessões do SSH.

4. Para criptografar o sistema operacional, especifique volumeType como **All** ou **OS** ao [habilitar a criptografia](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Todos os processos de espaço de usuário que não estão sendo executados como serviços `systemd` deverão ser encerrados com um `SIGKILL`. Reinicialize a VM. Ao habilitar a criptografia de disco do sistema operacional em uma VM em execução, planeje o tempo de inatividade da VM.

5. Periodicamente, monitore o progresso da criptografia usando as instruções da [próxima seção](#monitoring-os-encryption-progress).

6. Depois que Get-AzureRmVmDiskEncryptionStatus mostrar "VMRestartPending", reinicie a VM entrando ou usando o portal, o PowerShell ou a CLI.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Antes de reinicializar, recomendamos que você salve os [diagnósticos de inicialização](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) da VM.

#### <a name="monitoring-os-encryption-progress"></a>Monitorando o progresso da criptografia do sistema operacional
Você pode monitorar o progresso de criptografia do sistema operacional de três maneiras:

* Use o cmdlet `Get-AzureRmVmDiskEncryptionStatus` e verifique o campo ProgressMessage:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Depois que a VM atingir "Criptografia de disco do sistema operacional iniciada", levará cerca de 40 a 50 minutos em uma VM com suporte do armazenamento Premium.

 Devido ao [problema 388](https://github.com/Azure/WALinuxAgent/issues/388) no WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` são mostrados como `Unknown` em algumas distribuições. Com a versão 2.1.5 WALinuxAgent e posterior, esse problema é corrigido automaticamente. Caso você veja `Unknown` na saída, poderá verificar o status da criptografia de disco usando o Explorador de Recursos do Azure.

 Vá para [Explorador de Recursos do Azure](https://resources.azure.com/) e expanda essa hierarquia no painel de seleção à esquerda:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 Em InstanceView, role a tela para baixo para ver o status da criptografia das unidades.

 ![Exibição de instância VM](./media/azure-security-disk-encryption/vm-instanceview.png)

* Examine o [diagnóstico de inicialização](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). As mensagens da extensão ADE devem ser prefixadas com `[AzureDiskEncryption]`.

* Entre na VM via SSH e obtenha o log de extensão de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 É recomendável que você não entre na máquina virtual enquanto a criptografia de sistema operacional estiver em andamento. Copie os logs apenas quando os outros dois métodos falharem.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparar um VHD Linux previamente criptografado
##### <a name="ubuntu-16"></a>Ubuntu 16
Configure a criptografia durante a instalação da distribuição fazendo o seguinte:

1. Selecione **Configurar volumes criptografados** ao particionar os discos.

 ![Instalação do Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de inicialização separada que não deverá ser criptografada. Criptografe a unidade de inicialização.

 ![Instalação do Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma senha. Essa é a frase secreta que você carrega no cofre de chaves.

 ![Instalação do Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Conclua o particionamento.

 ![Instalação do Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando você inicializar a VM e precisar fornecer uma frase secreta, use a frase secreta que forneceu na etapa 3.

 ![Instalação do Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare a VM para upload no Azure usando [estas instruções](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não execute a última etapa (desprovisionamento da VM) ainda.

Configure a criptografia para trabalhar com o Azure fazendo o seguinte:

1. Crie um arquivo em /usr/local/sbin/azure_crypt_key.sh com o conteúdo no script a seguir. Preste atenção ao KeyFileName, pois ele é o nome do arquivo de frase secreta usado pelo Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Altere a configuração de criptografia em */etc/crypttab*. O resultado deve ser assim:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Se você estiver editando *azure_crypt_key.sh* no Windows e o tiver copiado para o Linux, execute `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Adicione permissões executáveis ao script:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Edite */etc/initramfs-tools/modules* acrescentando linha:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Execute `update-initramfs -u -k all` para atualizar o initramfs para fazer com que o `keyscript` entre em vigor.

7. Agora, você poderá desprovisionar a VM.

 ![Instalação do Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continue na próxima etapa e [carregue o VHD](#upload-encrypted-vhd-to-an-azure-storage-account) no Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Para configurar a criptografia durante a instalação de distribuição, faça o seguinte:
1. Ao particionar os discos, selecione **Criptografar o Grupo de Volumes** e digite uma senha. Esta é a senha que você carregará no cofre de chaves.

 ![Instalação do openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Inicialize a VM usando uma senha.

 ![Instalação do openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare a VM para carregamento no Azure seguindo as instruções em [Preparar uma máquina virtual do SLES ou openSUSE para o Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não execute a última etapa (desprovisionamento da VM) ainda.

Para configurar a criptografia para trabalhar com o Azure, faça o seguinte:
1. Edite o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Comente essas linhas ao final do arquivo /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Acrescente a seguinte linha no início do arquivo /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```
    DRACUT_SYSTEMD=0
 ```
E altere todas as ocorrências de:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
para:
```
    if [ 1 ]; then
```
4. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescente-o depois de “# Open LUKS device”:

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Execute `/usr/sbin/dracut -f -v` para atualizar o initrd.

6. Agora você pode desprovisionar a VM e [carregar o VHD](#upload-encrypted-vhd-to-an-azure-storage-account) no Azure.

##### <a name="centos-7"></a>CentOS 7
Para configurar a criptografia durante a instalação de distribuição, faça o seguinte:
1. Selecione **Criptografar meus dados** ao particionar os discos.

 ![Instalação do CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Verifique se a opção **Criptografar** está selecionada para a partição raiz.

 ![Instalação do CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase secreta. Essa é a frase secreta que você carregará no cofre de chaves.

 ![Instalação do CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Quando você inicializar a VM e precisar fornecer uma frase secreta, use a senha que forneceu na etapa 3.

 ![Instalação do CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Prepare a VM para carregamento no Azure usando as instruções de "CentOS 7.0+" em [Preparar uma máquina virtual baseada em CentOS para o Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não execute a última etapa (desprovisionamento da VM) ainda.

6. Agora você pode desprovisionar a VM e [carregar o VHD](#upload-encrypted-vhd-to-an-azure-storage-account) no Azure.

Para configurar a criptografia para trabalhar com o Azure, faça o seguinte:

1. Edite o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Comente essas linhas ao final do arquivo /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Acrescente a seguinte linha no início do arquivo /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```
    DRACUT_SYSTEMD=0
```
E altere todas as ocorrências de:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
para
```
    if [ 1 ]; then
```
4. Edite /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescente-o depois de “# Open LUKS device”:
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Execute o “/usr/sbin/dracut -f -v” para atualizar o initrd.

![Instalação do CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carregue o VHD criptografado para uma conta de armazenamento do Azure
Depois que a criptografia BitLocker ou criptografia DM-Crypt estiver habilitada, o VHD criptografado local precisará ser carregado para a conta de armazenamento.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Carregar o segredo de criptografia de disco da VM previamente criptografada no cofre de chaves
O segredo de criptografia de disco que você obteve anteriormente deve ser carregado como um segredo no cofre de chaves. O cofre de chaves deve ter criptografia de disco e permissões habilitadas para o cliente do Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de criptografia de disco não criptografado com uma KEK
Para configurar o segredo no cofre de chaves, use [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Se você tem uma máquina virtual do Windows, o arquivo bek é codificado como uma cadeia de caracteres em base64 e carregado no cofre de chaves usando o cmdlet `Set-AzureKeyVaultSecret`. Para o Linux, a frase secreta é codificada como uma cadeia de caracteres base64 e carregada para o cofre de chaves. Além disso, verifique se as seguintes marcas estão definidas ao criar o segredo no cofre de chaves.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Use o `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional sem usar KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de criptografia de disco criptografado com uma KEK
Antes de carregar o segredo no cofre de chaves, opcionalmente, você pode criptografá-lo usando uma chave de criptografia de chave. Use a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamento para primeiro criptografar o segredo usando a chave de criptografia de chave. A saída dessa operação wrap é uma cadeia de caracteres de URL codificada em base64, que você pode carregar como um segredo usando o cmdlet [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret).

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Use `$KeyEncryptionKey` e `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional usando KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especificar uma URL secreta ao anexar um disco do sistema operacional
#### <a name="without-using-a-kek"></a>Sem usar uma KEK
Ao anexar o disco do sistema operacional, você precisa passar `$secretUrl`. A URL foi gerada na seção "Segredo de criptografia de disco não criptografado com uma KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Usando uma KEK
Ao anexar o disco do sistema operacional, passe `$KeyEncryptionKey` e `$secretUrl`. A URL foi gerada na seção "Segredo de criptografia de disco não criptografado com uma KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Baixar este guia
Você pode baixar este guia na [Galeria do TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## <a name="for-more-information"></a>Para obter mais informações
[Explorar a Azure Disk Encryption com o Azure PowerShell - Parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Explorar a Azure Disk Encryption com o Azure PowerShell - Parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
