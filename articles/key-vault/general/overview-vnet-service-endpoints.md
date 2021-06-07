---
title: Pontos de extremidade de serviço de rede virtual para o Azure Key Vault
description: Saiba como os pontos de extremidade de serviço de rede virtual para Azure Key Vault permitem restringir o acesso a uma rede virtual especificada, incluindo cenários de uso.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: ae22f07a70f3317b62776e5024b7a3d1084516a1
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643489"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Pontos de extremidade de serviço de rede virtual para o Azure Key Vault

Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.

Há uma exceção importante para essa restrição. Se um usuário tiver optado por permitir serviços confiáveis da Microsoft, as conexões a partir desses serviços serão permitidas através do firewall. Por exemplo, esses serviços incluem o Office 365 Exchange Online, Office 365 SharePoint Online, Computação do Azure, Azure Resource Manager e o Backup do Azure. Esses usuários ainda devem apresentar um token do Azure Active Directory válido e ter permissões (configuradas como políticas de acesso) para executar a operação solicitada. Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Cenários de uso

Por padrão, é possível configurar [redes virtuais e firewalls do Key Vault](network-security.md) para negar acesso ao tráfego de todas as redes (incluindo o tráfego de Internet). É possível conceder acesso ao tráfego de redes virtuais específicas do Azure e intervalos de endereços IP públicos de Internet, permitindo criar um limite de rede seguro para os aplicativos.

> [!NOTE]
> As regras da rede virtual e os firewalls do Key Vault aplicam-se somente ao [plano de dados](secure-your-key-vault.md#data-plane-access-control) do Key Vault. Key Vault operações de plano de controle (como operações de criação, exclusão e modificação, definição de políticas de acesso, configuração de firewalls e regras de rede virtual e implantação de segredos ou chaves por meio de modelos de ARM) não são afetadas por firewalls e regras de rede virtual.

Seguem alguns exemplos de como é possível usar pontos de extremidade de serviço:

* Se você estiver usando o Key Vault para armazenar chaves de criptografia, segredos do aplicativo e certificados, e quer bloquear o acesso ao seu cofre de chaves da Internet pública.
* Você quer bloquear o acesso ao seu cofre de chaves para que apenas o seu aplicativo ou uma pequena lista de hosts designados possa conectar o cofre de chaves.
* Você tem um aplicativo executando na rede virtual do Azure e essa rede virtual é bloqueada para todo o tráfego de entrada e saída. O aplicativo ainda precisa conectar ao Key Vault para efetuar fetch de segredos ou certificados ou usar chaves de criptografia.

## <a name="trusted-services"></a>Serviços confiáveis

Segue uma lista de serviços confiáveis que poderão acessar um cofre de chaves se a opção **Permitir serviços confiáveis** estiver habilitada.

|Serviço confiável|Cenários de uso com suporte|
| --- | --- |
|Serviço de implantação de Máquinas Virtuais do Microsoft Azure|[Implanta certificados para VMs a partir do Key Vault gerenciado pelo cliente](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Serviço de implantação de modelo do Azure Resource Manager|[Transmite valores seguros durante a implantação](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Serviço de criptografia de volume do Azure Disk Encryption|Permite acesso à Chave do BitLocker (VM do Windows) ou à Frase Secreta de DM (VM do Linux) e à Chave de Criptografia de Chave durante a implantação da máquina virtual. Isso habilita o [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Serviço de Backup do Azure|Permite backup e restauração de chaves e segredos relevantes durante o backup de Máquinas Virtuais do Azure, usando o [Backup do Azure](../../backup/backup-overview.md).|
|Exchange Online e SharePoint Online|Permite acesso à chave de cliente para Criptografia do Serviço de Armazenamento do Azure com [Chave de Cliente](/microsoft-365/compliance/customer-key-overview).|
|Proteção de Informações do Azure|Permitir acesso à chave de locatário para [Proteção de Informações do Azure.](/azure/information-protection/what-is-information-protection)|
|Serviço de aplicativo do Azure|[Implanta o Certificado do Aplicativo Web do Azure por meio do Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Banco de Dados SQL do Azure|[Transparent Data Encryption com suporte de Bring your own Key para o banco de dados SQL do Azure e o Azure Synapse Analytics](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Armazenamento do Azure|[Criptografia do serviço de armazenamento usando chaves gerenciadas pelo cliente no Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Repositório Azure Data Lake|[Criptografia de dados no Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) com uma chave gerenciada pelo cliente.|
|Azure Synapse Analytics|[Criptografia de dados usando chaves gerenciadas pelo cliente no Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Serviço de análise rápida, fácil e colaborativa, com base no Apache Spark](/azure/databricks/scenarios/what-is-azure-databricks)|
|Gerenciamento de API do Azure|[Implantar certificados para o domínio personalizado de Key Vault usando o MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Fábrica de dados do Azure|[Buscar credenciais de armazenamento de dados no Key Vault de Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Hubs de eventos do Azure|[Permitir o acesso a um cofre de chaves para o cenário de chaves gerenciadas pelo cliente](../../event-hubs/configure-customer-managed-key.md)|
|Barramento de Serviço do Azure|[Permitir o acesso a um cofre de chaves para o cenário de chaves gerenciadas pelo cliente](../../service-bus-messaging/configure-customer-managed-key.md)|
|Importação/Exportação do Azure| [Usar chaves gerenciadas pelo cliente no Azure Key Vault para o serviço de importação/exportação](../../import-export/storage-import-export-encryption-key-portal.md)
|Registro de Contêiner do Azure|[Criptografia do registro usando chaves gerenciadas pelo cliente](../../container-registry/container-registry-customer-managed-keys.md)
|Gateway de Aplicativo do Azure |[Usando certificados Key Vault para ouvintes habilitados para HTTPS](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> Você deve configurar as políticas relevantes de acesso ao cofre de chaves para permitir que os serviços correspondentes tenham acesso ao Key Vault.

## <a name="next-steps"></a>Próximas etapas

- Para obter as instruções passo a passo, consulte [configurar Azure Key Vault firewalls e redes virtuais](network-security.md)
- consulte a [visão geral de segurança do Azure Key Vault](security-overview.md)
