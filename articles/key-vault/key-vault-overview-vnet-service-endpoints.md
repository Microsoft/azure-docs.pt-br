---
title: Pontos de extremidade de serviço de rede virtual para Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Visão geral dos pontos de extremidade de serviço de rede Virtual para Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: barbkess
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: 45499dac3cc50e2b6e79f9ebcb1bc3e7b4330beb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165844"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos de extremidade de serviço de rede virtual para o Azure Key Vault

Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.

Há uma exceção importante para essa restrição. Se um usuário tiver optado por permitir serviços confiáveis da Microsoft, as conexões a partir desses serviços serão permitidas através do firewall. Por exemplo, esses serviços incluem o Office 365 Exchange Online, Office 365 SharePoint Online, Computação do Azure, Azure Resource Manager e o Backup do Azure. Esses usuários ainda devem apresentar um token do Azure Active Directory válido e ter permissões (configuradas como políticas de acesso) para executar a operação solicitada. Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Cenários de uso

Por padrão, é possível configurar [redes virtuais e firewalls do Key Vault](key-vault-network-security.md) para negar acesso ao tráfego de todas as redes (incluindo o tráfego de Internet). É possível conceder acesso ao tráfego de redes virtuais específicas do Azure e intervalos de endereços IP públicos de Internet, permitindo criar um limite de rede seguro para os aplicativos.

> [!NOTE]
> As regras da rede virtual e os firewalls do Key Vault aplicam-se somente ao [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) do Key Vault. Operações do plano de controle do Key Vault (como criar, excluir, modificar operações, definir políticas de acesso, configurar firewalls e regras da rede virtual) não são afetadas por regras da rede virtual e firewalls.

Seguem alguns exemplos de como é possível usar pontos de extremidade de serviço:

* Se você estiver usando o Key Vault para armazenar chaves de criptografia, segredos do aplicativo e certificados, e quer bloquear o acesso ao seu cofre de chaves da Internet pública.
* Você quer bloquear o acesso ao seu cofre de chaves para que apenas o seu aplicativo ou uma pequena lista de hosts designados possa conectar o cofre de chaves.
* Você tem um aplicativo executando na rede virtual do Azure e essa rede virtual é bloqueada para todo o tráfego de entrada e saída. O aplicativo ainda precisa conectar ao Key Vault para efetuar fetch de segredos ou certificados ou usar chaves de criptografia.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurar redes virtuais e firewalls do Key Vault

Aqui estão as etapas necessárias para configurar redes virtuais e firewalls. Essas etapas se aplicam se você estiver usando o PowerShell, a CLI do Azure ou o portal do Azure.

1. Habilite [registro em log do Key Vault](key-vault-logging.md) para ver os logs de acesso detalhados. Isso ajuda nos diagnósticos, quando as regras da rede virtual e firewalls impedirem o acesso a um cofre de chaves. (Esta etapa é opcional, mas altamente recomendada.)
2. Habilite **pontos de extremidade de serviço para o cofre de chaves** para redes virtuais e sub-redes de destino.
3. Defina regras da rede virtual e firewalls para um cofre de chaves para restringir o acesso a esse cofre de chaves a partir de redes virtuais, sub-redes e intervalos de endereços IPv4 específicos.
4. Se esse cofre de chaves precisar estar acessível por qualquer serviço confiável da Microsoft, habilite a opção para permitir que **Serviços Confiáveis do Azure** conectem o cofre de chaves.

Para obter mais informações, consulte [Configurar redes virtuais e firewalls do Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Depois que as regras de firewall estiverem ativas, os usuários podem realizar apenas operações de [plano de dados](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.


> [!NOTE]
> Esteja ciente das seguintes limitações de configuração:
> * Um máximo de 127 regras da rede virtual e 127 regras de IPv4 são permitidas. 
> * Intervalos de endereços pequenos que usam tamanhos de prefixo "/31" ou "/32" não têm suporte. Em vez disso, esses intervalos devem ser configurados usando regras de endereço IP individuais.
> * Regras de rede IP somente são permitidas para endereços IP públicos. Os intervalos de endereços IP reservados para redes privadas (conforme definido na RFC 1918) não são permitidos em regras de IP. Redes privadas incluem endereços que começam com **10.** , **172.16 31**, e **192.168.** . 
> * Atualmente, somente há suporte para endereços IPv4.

## <a name="trusted-services"></a>Serviços confiáveis

Segue uma lista de serviços confiáveis que poderão acessar um cofre de chaves se a opção **Permitir serviços confiáveis** estiver habilitada.

|Serviço confiável|Cenários de uso|
| --- | --- |
|Serviço de implantação de Máquinas Virtuais do Microsoft Azure|[Implanta certificados para VMs a partir do Key Vault gerenciado pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Serviço de implantação de modelo do Azure Resource Manager|[Transmite valores seguros durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Serviço de criptografia de volume do Azure Disk Encryption|Permite acesso à Chave do BitLocker (VM do Windows) ou à Frase Secreta de DM (VM do Linux) e à Chave de Criptografia de Chave durante a implantação da máquina virtual. Isso habilita o [Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Serviço de Backup do Azure|Permite backup e restauração de chaves e segredos relevantes durante o backup de Máquinas Virtuais do Azure, usando o [Backup do Azure](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online e SharePoint Online|Permite acesso à chave de cliente para Criptografia do Serviço de Armazenamento do Azure com [Chave de Cliente](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Proteção de Informações do Azure|Permitir acesso à chave de locatário para [Proteção de Informações do Azure.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Serviço de aplicativo do Azure|[Implanta o Certificado do Aplicativo Web do Azure por meio do Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Banco de Dados SQL do Azure|[Transparent Data Encryption com suporte Bring Your Own Key para Data Warehouse e Banco de Dados SQL do Azure](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Armazenamento do Azure|[Criptografia do Serviço de Armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Repositório Azure Data Lake|[Criptografia de dados no Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) com uma chave gerenciada pelo cliente.|
|Azure Databricks|[Serviço de análise rápida, fácil e colaborativa, com base no Apache Spark](../azure-databricks/what-is-azure-databricks.md)|
|Gerenciamento de API do Azure|[Implantar certificados para o domínio personalizado do Key Vault usando o MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|



> [!NOTE]
> Você deve configurar as políticas relevantes de acesso ao cofre de chaves para permitir que os serviços correspondentes tenham acesso ao Key Vault.

## <a name="next-steps"></a>Próximas etapas

* [Proteger seu cofre de chaves](key-vault-secure-your-key-vault.md)
* [Configurar redes virtuais e firewalls do Azure Key Vault](key-vault-network-security.md)
