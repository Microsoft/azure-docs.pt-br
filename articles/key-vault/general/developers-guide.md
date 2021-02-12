---
title: Guia do desenvolvedor do Cofre da Chave do Azure
description: Os desenvolvedores podem usar o Cofre da Chave do Azure para gerenciar chaves de criptografia no ambiente do Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 40ce5f55b4109fee0099110c17f02b33c3eff808
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791417"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor do Cofre da Chave do Azure

O Key Vault permite acessar com segurança informações confidenciais nos aplicativos:

- Chaves, segredos e certificados são protegidos sem precisar escrever o código por conta própria e você pode usá-los facilmente de seus aplicativos.
- Você permite que os clientes tenham e gerenciem suas próprias chaves, segredos e certificados para que você possa se concentrar em fornecer os principais recursos de software. Dessa forma, seus aplicativos não terão a responsabilidade ou a responsabilidade potencial das chaves de locatário, dos segredos e dos certificados dos seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas mantém o gerenciamento de chaves externo do seu aplicativo. Para obter mais informações sobre chaves, consulte [sobre chaves](../keys/about-keys.md)
- Você pode gerenciar credenciais como senhas, chaves de acesso e tokens SAS armazenando-as em Key Vault como segredos, consulte [sobre segredos](../secrets/about-secrets.md)
- Gerenciar certificados. Para obter mais informações, consulte [sobre certificados](../certificates/about-certificates.md)

Para obter mais informações gerais sobre o Cofre de Chaves do Azure, confira [O que é o Cofre de Chaves](overview.md).

## <a name="public-previews"></a>Visualizações públicas

Periodicamente, lançamos uma visualização pública de um novo recurso do Key Vault. Experimente os recursos de visualização pública e informe-nos o que você imagina por meio azurekeyvault@microsoft.com de nosso endereço de email de comentários.

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando Cofres da Chave

O gerenciamento de Key Vault, semelhante a outros serviços do Azure, é feito por meio do serviço Azure Resource Manager. O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua conta do Azure. Para obter mais informações, consulte [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

O acesso à camada de gerenciamento é controlado pelo [controle de acesso baseado em função do Azure](../../role-based-access-control/overview.md). No Key Vault, a camada de gerenciamento, também conhecida como plano de gerenciamento ou controle, permite criar e gerenciar cofres de chaves e seus atributos, incluindo políticas de acesso, mas não chaves, segredos e certificados, que são gerenciados no plano de dados. Você pode usar `Key Vault Contributor` a função predefinida para conceder acesso de gerenciamento a Key Vault.     

**APIs e SDKs para o gerenciamento do Key Vault:**

| CLI do Azure | PowerShell | API REST | Gerenciador de Recursos | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault)<br>[Início rápido](quick-create-cli.md)|[Referência](/powershell/module/az.keyvault)<br>[Início rápido](quick-create-powershell.md)|[Referência](/rest/api/keyvault/)|[Referência](/azure/templates/microsoft.keyvault/vaults)<br>[Início rápido](https://docs.microsoft.com/azure/key-vault/general/vault-create-template)|[Referência](/dotnet/api/microsoft.azure.management.keyvault)|[Referência](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referência](/java/api/com.microsoft.azure.management.keyvault)|[Referência](/javascript/api/@azure/arm-keyvault)|

Consulte [bibliotecas de cliente](client-libraries.md) para pacotes de instalação e código-fonte.

Para obter mais informações sobre Key Vault plano de gerenciamento, consulte [Key Vault plano de gerenciamento](./secure-your-key-vault.md#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Autenticar para Key Vault no código

Key Vault está usando a autenticação do Azure AD que requer que a entidade de segurança do Azure AD conceda acesso. Uma entidade de segurança do Azure AD pode ser um usuário, uma entidade de serviço de aplicativo, uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md)ou um grupo de qualquer tipo de entidades de segurança.

### <a name="authentication-best-practices"></a>Práticas recomendadas de autenticação

É recomendável usar a identidade gerenciada para aplicativos implantados no Azure. Se você usar os serviços do Azure, que não dão suporte à identidade gerenciada ou se os aplicativos forem implantados no local, a [entidade de serviço com um certificado](../../active-directory/develop/howto-create-service-principal-portal.md) será uma alternativa possível. Nesse cenário, o certificado deve ser armazenado em Key Vault e girado com frequência. A entidade de serviço com segredo pode ser usada para ambientes de desenvolvimento e teste e localmente ou em Cloud Shell usando a entidade de usuário é recomendada.

Entidades de segurança recomendadas por ambiente:
- **Ambiente de produção**:
  - Identidade gerenciada ou entidade de serviço com um certificado
- **Ambientes de teste e desenvolvimento**:
  - Identidade gerenciada, entidade de serviço com certificado ou entidade de serviço com segredo
- **Desenvolvimento local**:
  - Entidade de usuário ou entidade de serviço com segredo

Os cenários de autenticações acima têm suporte da **biblioteca de clientes de identidade do Azure** e são integrados a SDKs de Key Vault. A biblioteca de identidades do Azure pode ser usada em diferentes ambientes e plataformas sem alterar seu código. A identidade do Azure também recuperará automaticamente o token de autenticação do conectado ao usuário do Azure com CLI do Azure, Visual Studio, Visual Studio Code e outros. 

Para obter mais informações sobre o Libarary do cliente de identidade do Azure, consulte:

**Bibliotecas de cliente de identidade do Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[SDK .NET do Azure Identity](/dotnet/api/overview/azure/identity-readme)|[Python do SDK do Azure Identity](/python/api/overview/azure/identity-readme)|[SDK do Azure Identity do Java](/java/api/overview/azure/identity-readme)|[JavaScript do SDK de identidade do Azure](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Biblioteca de autenticação de aplicativo](/dotnet/api/overview/azure/service-to-service-authentication) recomendada para Key Vault SDK do .NET versão 3, que atualmente é preterida. Siga as [diretrizes de migração do AppAuthentication para o Azure. Identity](/dotnet/api/overview/azure/app-auth-migration) para migrar para Key Vault o SDK do .NET versão 4.

Para obter tutoriais sobre como se autenticar no Key Vault em aplicativos, consulte:
- [Autenticar para Key Vault no aplicativo hospedado na VM no .NET](./tutorial-net-virtual-machine.md)
- [Autenticar para Key Vault no aplicativo hospedado na VM no Python](./tutorial-python-virtual-machine.md)
- [Autenticar para Key Vault com o serviço de aplicativo](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Gerenciar chaves, certificados e segredos

O acesso a chaves, segredos e certificados é controlado pelo plano de dados. O controle de acesso do plano de dados pode ser feito usando políticas de acesso do cofre local ou RBAC do Azure (versão prévia).

**APIs e SDKs de chaves**

| CLI do Azure | PowerShell | API REST | Gerenciador de Recursos | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/key)<br>[Início rápido](../keys/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault/)<br>[Início rápido](../keys/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#key-operations)|[Referência](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Início rápido](../keys/quick-create-template.md)|[Referência](/dotnet/api/azure.security.keyvault.keys)<br>[Início rápido](../keys/quick-create-net.md)|[Referência](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Início rápido](../keys/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Início rápido](../keys/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-keys/)<br>[Início rápido](../keys/quick-create-node.md)|

**APIs e SDKs de certificados**

| CLI do Azure | PowerShell | API REST | Gerenciador de Recursos | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/certificate)<br>[Início rápido](../certificates/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault)<br>[Início rápido](../certificates/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#certificate-operations)|N/D|[Referência](/dotnet/api/azure.security.keyvault.certificates)<br>[Início rápido](../certificates/quick-create-net.md)|[Referência](/python/api/overview/azure/keyvault-certificates-readme)<br>[Início rápido](../certificates/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Início rápido](../certificates/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-certificates/)<br>[Início rápido](../certificates/quick-create-node.md)|

**APIs e SDKs dos segredos**

| CLI do Azure | PowerShell | API REST | Gerenciador de Recursos | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/secret)<br>[Início rápido](../secrets/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault/)<br>[Início rápido](../secrets/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#secret-operations)|[Referência](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Início rápido](../secrets/quick-create-template.md)|[Referência](/dotnet/api/azure.security.keyvault.secrets)<br>[Início rápido](../secrets/quick-create-net.md)|[Referência](/python/api/overview/azure/keyvault-secrets-readme)<br>[Início rápido](../secrets/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Início rápido](../secrets/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-secrets/)<br>[Início rápido](../secrets/quick-create-node.md)|

Consulte [bibliotecas de cliente](client-libraries.md) para pacotes de instalação e código-fonte.

Para obter mais informações sobre Key Vault segurança do plano de dados, consulte [Key Vault plano de dados e políticas de acesso](./secure-your-key-vault.md#data-plane-and-access-policies) e o [plano de dados do Key Vault e o RBAC do Azure (versão prévia)](./secure-your-key-vault.md#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos sobre como usar o Cofre de Chaves com seus aplicativos, confira:

- [Exemplos de código do Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -exemplos de código para o Azure Key Vault. 

## <a name="how-tos"></a>Instruções

Os artigos e cenários a seguir fornecem diretrizes específicas da tarefa para trabalhar com o Azure Key Vault:

- [Acessando o Cofre de Chaves por trás do firewall](access-behind-firewall.md) - para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves deve ser capaz de acessar vários pontos de extremidade para várias funcionalidades.
- Como implantar certificados em VMs de Key Vault- [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) -um aplicativo de nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado para essa VM atualmente?
- [Implantar Certificado do Aplicativo Web do Azure por meio do Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Atribuir uma política de acesso (Portal do PowerShell da[CLI](assign-access-policy-cli.md)  |  [](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md)). 
- [Como usar a exclusão reversível do Key Vault com CLI](./key-vault-recovery.md) orienta pela utilização e ciclo de vida de um cofre de chaves e de vários objetos de cofre de chaves com a exclusão reversível habilitada.
- [Como transmitir valores seguros (como senhas) durante a implantação](../../azure-resource-manager/templates/key-vault-parameter.md) - Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um Cofre de Chaves do Azure e fazer referência ao valor em outros modelos do Resource Manager.

## <a name="integrated-with-key-vault"></a>Introdução ao Cofre de Chaves

Estes artigos abordam outros cenários e serviços que usam ou se integram ao Key Vault.

- A [criptografia em repouso](../../security/fundamentals/encryption-atrest.md) permite a codificação (criptografia) de dados quando ela é persistente. As chaves de criptografia de dados geralmente são criptografadas com uma chave de criptografia de chave em Azure Key Vault para limitar ainda mais o acesso.
- [Proteção de Informações do Azure](/azure/information-protection/plan-implement-tenant-key) permite a você gerenciar sua própria chave de locatário. Por exemplo, em vez de a Microsoft gerenciar sua chave de locatário (o padrão), você pode gerenciá-la para cumprir os regulamentos específicos que se aplicam à sua organização. Gerenciar sua própria chave de locatário também é conhecido como "traga sua própria chave", ou BYOK.
- O [serviço de vínculo privado do Azure](private-link-service.md) permite que você acesse os serviços do Azure (por exemplo, Azure Key Vault, armazenamento do azure e Azure Cosmos DB) e os serviços hospedados de cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual.
- A integração do Key Vault com a [grade de eventos](../../event-grid/event-schema-key-vault.md)  permite que os usuários sejam notificados quando o status de um segredo armazenado no cofre de chaves for alterado. Você pode distribuir novas versões de segredos para aplicativos ou girar segredos de expiração próximos para evitar interrupções.
- Você pode proteger seus segredos do [DevOps do Azure](/azure/devops/pipelines/release/azure-key-vault) contra acesso indesejado no Key Vault.
- [Usar o segredo armazenado em Key Vault no databricks para se conectar ao armazenamento do Azure](./integrate-databricks-blob-storage.md)
- Configurar e executar o provedor de Azure Key Vault para o [Driver de armazenamento de segredos do CSI](./key-vault-integrate-kubernetes.md) no kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Visões gerais e conceitos do Key Vault

- [Comportamento de exclusão reversível do Key Vault](soft-delete-overview.md) descreve um recurso que permite a recuperação de objetos excluídos, seja a exclusão acidental ou intencional.
- [Limitação de cliente do Key Vault](overview-throttling.md) orienta você sobre os conceitos básicos de limitação e oferece uma abordagem para seu aplicativo.
- [Mundos de segurança do Key Vault](overview-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Social

- [Blog do Cofre de Chaves](/archive/blogs/kv/)
- [Fórum do Cofre de Chaves](https://aka.ms/kvforum)