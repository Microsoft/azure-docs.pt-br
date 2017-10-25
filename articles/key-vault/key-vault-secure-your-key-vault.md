---
title: Proteger seu cofre de chaves | Microsoft Docs
description: "Gerencie permissões de acesso para o cofre de chaves para gerenciar cofres, chaves e segredos. Modelo de autenticação e autorização para o cofre de chaves e como proteger o cofre de chaves"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: b81791f0bce7e6f57782dfe7bc5fb5fc21369e7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="secure-your-key-vault"></a>Proteger seu cofre de chaves
O Cofre de Chaves do Azure é um serviço de nuvem que protege chaves de criptografia e segredos (como certificados, cadeias de conexão e senhas) para aplicativos de nuvem. Como esses dados são confidenciais e críticos para os negócios, convém proteger o acesso aos cofres de chaves para que apenas aplicativos e usuários autorizados possam acessar o cofre de chaves. Este artigo fornece uma visão geral do modelo de acesso do cofre de chaves, explica a autenticação e a autorização e descreve como proteger o acesso ao cofre de chaves para aplicativos de nuvem com um exemplo.

## <a name="overview"></a>Visão geral
O acesso a um cofre de chaves é controlado por meio de duas interfaces separadas: plano de gerenciamento e plano de dados. Para ambos os planos, a autorização e a autenticação adequadas são necessárias para que um chamador (um usuário ou um aplicativo) possa obter acesso ao cofre de chaves. A autenticação estabelece a identidade do chamador, enquanto a autorização determina quais operações o chamador tem permissão para executar.

Para a autenticação, o plano de gerenciamento e o plano de dados usam o Azure Active Directory. Para a autorização, o plano de gerenciamento usa RBAC (controle de acesso baseado em função), enquanto o plano de dados usa a política de acesso do cofre de chaves.

Aqui está uma breve visão geral dos tópicos abordados:

[Autenticação usando o Azure Active Directory](#authentication-using-azure-active-directory) - esta seção explica como um chamador autenticado com o Azure Active Directory acessa um cofre de chaves por meio do plano de gerenciamento e do plano de dados. 

[Plano de gerenciamento e plano de dados](#management-plane-and-data-plane) - o plano de gerenciamento e o plano de dados são dois planos de acesso usados para acessar o cofre de chaves. Cada plano de acesso dá suporte a operações específicas. Esta seção descreve os pontos de extremidade de acesso, as operações com suporte e o método de controle de acesso usado por cada plano. 

[Controle de acesso do plano de gerenciamento](#management-plane-access-control) - nesta seção, veremos como permitir o acesso às operações do plano de gerenciamento usando o controle de acesso baseado em função.

[Controle de acesso do plano de dados](#data-plane-access-control) - esta seção descreve como usar a política de acesso do cofre de chaves para controlar o acesso do plano de dados.

[Exemplo](#example) - Este exemplo descreve como configurar o controle de acesso para o cofre de chaves para permitir que três equipes diferentes (equipe de segurança, desenvolvedores/operadores e auditores) executem tarefas específicas para desenvolver, gerenciar e monitorar um aplicativo no Azure.

## <a name="authentication-using-azure-active-directory"></a>Autenticação usando o Azure Active Directory
Quando você cria um cofre de chaves em uma assinatura do Azure, ele é associado automaticamente ao locatário do Azure Active Directory da assinatura. Todos os chamadores (usuários e aplicativos) devem ser registrados neste locatário para acessar este cofre de chaves. Um aplicativo ou um usuário deve se autenticar com o Azure Active Directory para acessar o cofre de chaves. Isso se aplica ao plano de gerenciamento e ao acesso do plano de dados. Em ambos os casos, um aplicativo pode acessar o cofre de chaves de duas maneiras:

* **acesso de usuário+aplicativo** - geralmente isso é para aplicativos que acessam o cofre de chaves em nome de um usuário conectado. O Azure PowerShell e o Portal do Azure são exemplos desse tipo de acesso. Há duas maneiras de conceder acesso a usuários: uma delas consiste em conceder acesso aos usuários para que eles possam acessar o cofre de chaves por meio de qualquer aplicativo e a outra forma é conceder a um usuário acesso ao cofre de chaves somente quando ele usa um aplicativo específico (conhecido como identidade composta). 
* **Acesso somente de aplicativo** - para aplicativos que executam serviços de daemon, trabalhos em segundo plano etc. A identidade do aplicativo recebe acesso ao cofre de chaves.

Em ambos os tipos de aplicativos, o aplicativo é autenticado com o Azure Active Directory usando qualquer um dos [métodos de autenticação com suporte](../active-directory/active-directory-authentication-scenarios.md) e adquire um token. O método de autenticação usado depende do tipo de aplicativo. Em seguida, o aplicativo usa esse token e envia a solicitação de API REST ao cofre de chaves. No caso de acesso do plano de gerenciamento, as solicitações são roteadas por meio do ponto de extremidade do Azure Resource Manager. Ao acessar o plano de dados, os aplicativos falam diretamente com um ponto de extremidade do cofre de chaves. Veja mais detalhes sobre o [fluxo de autenticação completo](../active-directory/active-directory-protocols-oauth-code.md). 

O nome do recurso para o qual o aplicativo solicita um token é diferente, dependendo de o aplicativo acessar o plano de gerenciamento ou o plano de dados. Portanto, o nome do recurso é um ponto de extremidade de plano de gerenciamento ou plano de dados descrito na tabela em uma seção posterior, dependendo do ambiente do Azure.

Um mecanismo de autenticação único para o plano de gerenciamento e o plano de dados tem suas próprias vantagens:

* As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização
* Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização
* As organizações podem personalizar a autenticação por meio de opções no Azure Active Directory (por exemplo, habilitando a autenticação multifator para aumentar a segurança)

## <a name="management-plane-and-data-plane"></a>Plano de gerenciamento e plano de dados
O Cofre de Chaves do Azure é um serviço do Azure disponível por meio do modelo de implantação do Azure Resource Manager. Ao criar um cofre de chaves, você obtém um contêiner virtual no qual pode criar outros objetos, como chaves, segredos e certificados. Em seguida, você acessa o cofre de chaves usando o plano de gerenciamento e o plano de dados para executar operações específicas. A interface do plano de gerenciamento é usada para gerenciar o cofre de chaves em si, por exemplo, para criar, excluir, atualizar atributos do cofre de chaves e definir políticas de acesso para o plano de dados. A interface do plano de dados é usada para adicionar, excluir, modificar e usar as chaves, os segredos e os certificados armazenados no cofre de chaves.

As interfaces do plano de gerenciamento e do plano de dados são acessadas por meio de pontos de extremidade diferentes (confira a tabela). A segunda coluna da tabela descreve os nomes DNS para esses pontos de extremidade em diferentes ambientes do Azure. A terceira coluna descreve as operações que você pode executar em cada plano de acesso. Cada plano de acesso também tem seu próprio mecanismo de controle de acesso: para o plano de gerenciamento, o controle de acesso é definido usando o RBAC do Azure Resource Manager (Controle de Acesso Baseado em Função), enquanto para o plano de dados, o controle de acesso é definido usando a política de acesso do cofre de chaves.

| Plano de acesso | Pontos de extremidade de acesso | Operações | Mecanismo de controle de acesso |
| --- | --- | --- | --- |
| Plano de gerenciamento |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 |Criar/Ler/Atualizar/Excluir cofre de chaves <br> Definir políticas de acesso para o cofre de chaves<br>Definir marcas para o cofre de chaves |RBAC (Controle de Acesso Baseado em Função) do Azure Resource Manager |
| Plano de dados |**Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governo dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |Para chaves: Descriptografar, Criptografar, UnwrapKey, WrapKey, Verificar, Entrar, Obter, Listar, Atualizar, Criar, Importar, Excluir, Backup, Restauração<br><br> Para segredos: Obter, Listar, Definir, Excluir |Política de acesso do cofre de chaves |

Os controles de acesso do plano de gerenciamento e do plano e dados funcionam de forma independente. Por exemplo, se desejar conceder a um aplicativo acesso para usar as chaves em um cofre de chaves, você só precisará conceder permissões de acesso ao plano de dados usando políticas de acesso do cofre de chaves, e nenhum acesso do plano de gerenciamento será necessário para o aplicativo. Por outro lado, se quiser que um usuário possa ler propriedades de cofre e marcas, mas não tenha acesso a chaves, segredos ou certificados, você poderá conceder ao usuário acesso de 'leitura' usando o RBAC, e nenhum acesso a dados será necessário.

## <a name="management-plane-access-control"></a>Controle de acesso do plano de gerenciamento
O plano de gerenciamento consiste em operações que afetam o cofre de chaves em si. Por exemplo, você pode criar ou excluir um cofre de chaves. Você pode obter uma lista de cofres em uma assinatura. Você pode recuperar as propriedades do cofre de chaves (como SKU e marcas) e definir políticas de acesso de cofre de chaves que controlam os usuários e os aplicativos que podem acessar chaves e segredos no cofre de chaves. O controle de acesso do plano de gerenciamento usa RBAC. Confira a lista completa das operações do cofre de chaves que podem ser executadas por meio do plano de gerenciamento na tabela da seção anterior. 

### <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
Cada assinatura do Azure tem um Azure Active Directory. Os usuários, grupos e aplicativos desse diretório podem receber acesso para gerenciar recursos na assinatura do Azure que usam o modelo de implantação do Azure Resource Manager. Esse tipo de controle de acesso é chamado de RBAC (Controle de Acesso Baseado em função). Para gerenciar esse acesso, é possível usar o [Portal do Azure](https://portal.azure.com/), as [ferramentas da CLI do Azure](../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Com o modelo do Azure Resource Manager, você pode criar o cofre de chaves em um grupo de recursos e controlar o acesso do plano de gerenciamento ao cofre de chaves usando o Azure Active Directory. Por exemplo, você pode conceder a usuários ou a um grupo a capacidade de gerenciar cofres de chaves em um grupo de recursos específico.

Você pode conceder acesso a usuários, grupos e aplicativos em um escopo específico atribuindo funções RBAC apropriadas. Por exemplo, para conceder acesso a um usuário para gerenciar cofres de chaves, atribuaq uma função predefinida 'Colaborador de cofres de chaves' ao usuário em um escopo específico. Nesse caso, o escopo seria uma assinatura, um grupo de recursos ou apenas um cofre de chaves específico. Uma função atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos na assinatura. Uma função atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos. Uma função atribuída a um recurso específico só se aplica a esse recurso. Há várias funções predefinidas (confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md)) e, se as funções predefinidas não atendem às suas necessidades, você também pode definir suas próprias funções.

> [!IMPORTANT]
> Observe que, se uma usuária tiver permissões de Colaborador (RBAC) para um plano de gerenciamento de cofre de chaves, poderá conceder a si mesma acesso ao plano de dados, definindo a política de acesso do cofre de chaves, que controla o acesso ao plano de dados. Portanto, é recomendável controlar exatamente quem tem o acesso de 'Colaborador' a seu cofres de chaves para garantir que somente pessoas autorizadas possam acessar e gerenciar cofres de chaves, chaves, segredos e certificados.
> 
> 

## <a name="data-plane-access-control"></a>Controle de acesso do plano de dados
O plano de dados de cofre de chaves consiste em operações que afetam os objetos em um cofre de chaves, como chaves, segredos e certificados.  Isso inclui operações-chave, como criar, importar, atualizar, listar, fazer backup de chaves e restaurá-las, operações criptográficas como entrar, verificar, criptografar, descriptografar, encapsular e desencapsular e definir marcas e outros atributos para chaves. Da mesma forma, para segredos, isso inclui obter, definir, listar e excluir.

O acesso do dados do plano é concedido definindo políticas de acesso para um cofre de chaves. Um usuário, grupo ou aplicativo deve ter permissões de Colaborador (RBAC) para o plano de gerenciamento para um cofre de chaves para poder definir políticas de acesso para esse cofre de chaves. Um usuário, grupo ou aplicativo pode receber acesso para executar operações específicas para chaves ou segredos em um cofre de chaves. o cofre de chaves dá suporte a até 16 entradas da política de acesso para um cofre de chaves. Crie um grupo de segurança do Azure Active Directory e adicione usuários a esse grupo para conceder acesso do plano de dados para vários usuários em um cofre de chaves.

### <a name="key-vault-access-policies"></a>Políticas de Acesso do cofre de chaves
As políticas de acesso do cofre de chaves concedem permissões a chaves, segredos e certificados separadamente. Por exemplo, você pode dar a um usuário acesso apenas a chaves, mas nenhuma permissão para segredos. No entanto, as permissões para acessar chaves, segredos ou certificados estão no nível de cofre. Em outras palavras, a política de acesso de cofre de chaves não dá suporte a permissões em nível de objeto. Você pode usar o [portal do Azure](https://portal.azure.com/), as [Ferramentas de CLI do Azure](../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST de Gerenciamento de cofre de chaves](https://msdn.microsoft.com/library/azure/mt620024.aspx) para definir políticas de acesso para um cofre de chaves.

> [!IMPORTANT]
> Observe que as políticas de acesso de cofre de chaves se aplicam no nível de cofre. Por exemplo, quando uma usuária tem permissão para criar e excluir chaves, ela pode executar essas operações em todas as chaves no cofre de chaves.
> 
> 

## <a name="example"></a>Exemplo
Digamos que você esteja desenvolvendo um aplicativo que usa um certificado para SSL, o armazenamento do Azure para armazenar dados e uma chave RSA de 2048 bits para operações de entrada. Digamos que esse aplicativo seja executado em uma VM (ou um Conjunto de Dimensionamento de VM). Você pode usar um cofre de chaves para armazenar todos os segredos do aplicativo e usar o cofre de chaves para armazenar o certificado de inicialização que é usado pelo aplicativo para se autenticar com o Azure Active Directory.

Portanto, aqui está um resumo de todas as chaves e segredos a serem armazenados em um cofre de chaves.

* **Certificado SSL** - usado para SSL
* **Chave de Armazenamento** - usada para obter acesso à conta de Armazenamento
* **Chave de 2048 bits RSA** - usada para operações de entrada
* **Certificado de inicialização** - usado para autenticação no Azure Active Directory, para obter acesso ao cofre de chaves para buscar a chave de armazenamento e usar a chave RSA para assinatura.

Agora, vamos conhecer as pessoas que estão gerenciando, implantando e auditando esse aplicativo. Vamos usar três funções neste exemplo.

* **Equipe de segurança** - normalmente, a equipe de TI do 'escritório do CSO (Diretor de Segurança)' ou equivalente, responsável pela segurança adequada de segredos, como certificados SSL, chaves RSA usadas para assinatura, cadeias de conexão para bancos de dados e chaves de conta de armazenamento.
* **Desenvolvedores/operadores** - as pessoas que desenvolvem o aplicativo e o implantam no Azure. Normalmente, não fazem parte da equipe de segurança e, assim, não devem ter acesso a dados confidenciais, como certificados SSL e chaves RSA, mas o aplicativo que implantam deve ter acesso a esses itens.
* **Auditores** - geralmente esse é um conjunto diferente de pessoas, isoladas dos desenvolvedores e da equipe de TI geral. Sua responsabilidade é analisar o uso adequado e a manutenção de certificados, chaves etc. e garantir a conformidade com padrões de segurança de dados. 

Há mais uma função que está fora do escopo desse aplicativo, mas é relevante para ser mencionada aqui: o administrador de assinatura (ou grupo de recursos). O administrador de assinatura define permissões de acesso iniciais para a equipe de segurança. Estamos supondo que o administrador de assinatura concedeu acesso à equipe de segurança para um grupo de recursos no qual residem todos os recursos necessários para esse aplicativo.

Agora, vamos ver as ações que cada função executa no contexto do aplicativo.

* **Equipe de segurança**
  * Criar cofres de chaves
  * Ativar o registro em log do cofre de chaves
  * Adicionar chaves/segredos
  * Criar o backup de chaves de recuperação de desastre
  * Definir a política de acesso de cofre de chaves para conceder permissões a usuários e aplicativos para executar operações específicas
  * Distribuir periodicamente chaves/segredos
* **Desenvolvedores/operadores**
  * Obter referências para inicialização e certificados SSL (impressões digitais), chave de armazenamento (URI de segredo) e chave de assinatura (URI de chave) da equipe de segurança
  * Desenvolver e implantar o aplicativo que acessa chaves e segredos de forma programática
* **Auditores**
  * Examinar logs de uso para confirmar o uso adequado de chaves/segredos e a conformidade com padrões de segurança de dados

Agora, vamos ver quais permissões de acesso ao cofre de chaves são necessárias para cada função (e o aplicativo) para realizar suas tarefas atribuídas. 

| Função de Usuário | Permissões do plano de gerenciamento | Permissões do plano de dados |
| --- | --- | --- |
| Equipe de Segurança |Colaborador do cofre de chaves |Chaves: fazer backup, criar, excluir, obter, importar, listar, restaurar <br> Segredos: todos |
| Desenvolvedores/Operador |permissão para implantar o cofre de chaves para que as VMs que eles implantam possam buscar segredos do cofre de chaves |Nenhum |
| Auditores |Nenhum |Chaves: lista<br>Segredos: lista |
| Aplicativo |Nenhum |Chaves: assinar<br>Segredos: obter |

> [!NOTE]
> Os auditores precisam de permissão de lista para chaves e segredos para que possam inspecionar atributos de chaves e segredos que não são emitidos nos logs, como marcas, datas de ativação e expiração.
> 
> 

Além de permissão para o cofre de chaves, todas as três funções também precisam acessar outros recursos. Por exemplo, para poder implantar VMs (ou aplicativos Web etc.) Os Desenvolvedores/Operadores também precisam de acesso de 'Colaborador' a esses tipos de recursos. Os auditores precisam de acesso de leitura à conta de armazenamento em que os logs do cofre de chaves são armazenados.

Como o foco deste artigo é proteger o acesso ao seu cofre de chaves, só ilustramos as partes relevantes referentes a esse tópico e ignoramos detalhes sobre implantação de certificados, acesso a chaves e segredos de forma programática etc. Esses detalhes já são abrangidos em outro lugar. A implantação de certificados armazenados no cofre de chaves em VMs é abordada em uma [postagem de blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) e não há [código de exemplo](https://www.microsoft.com/download/details.aspx?id=45343) disponível para ilustrar como usar o certificado de inicialização para se autenticar no Azure AD e obter acesso ao cofre de chaves.

A maioria das permissões de acesso pode ser concedida usando o portal do Azure, mas, para conceder permissões granulares, talvez seja necessário usar o Azure PowerShell (ou a CLI do Azure) para obter o resultado desejado. 

Os seguintes trechos de código do PowerShell pressupõem que:

* O administrador do Azure Active Directory criou grupos de segurança que representam as três funções, ou seja, Equipe de Segurança da Contoso, Desenvolvedores e Operadores de Aplicativos da Contoso e Auditores de Aplicativos da Contoso. O administrador também adicionou usuários aos grupos aos quais pertencem.
* **ContosoAppRG** é o grupo de recursos em que residem todos os recursos. **contosologstorage** é onde os logs são armazenados. 
* O cofre de chaves **ContosoKeyVault** e a conta de armazenamento usada para logs de cofre de chaves **contosologstorage** devem estar no mesmo local no Azure

Primeiro, o administrador de assinatura atribui funções de 'Colaborador do cofre de chaves' e 'Administrador de Acesso de Usuário' à equipe de segurança. Isso permite que a equipe de segurança gerencie o acesso a outros recursos e gerencie cofres de chaves no grupo de recursos ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

O script a seguir ilustra como a equipe de segurança pode criar um cofre de chaves, configurar o registro em log e definir permissões de acesso para outras funções e o aplicativo. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

A função personalizada definida é atribuível somente à assinatura em que o grupo de recursos ContosoAppRG é criado. Se as mesmas funções personalizadas forem usadas para outros projetos em outras assinaturas, seu escopo poderá ter mais assinaturas adicionadas.

A atribuição de função personalizada para desenvolvedores/operadores para a permissão "implantar/ação" está no escopo do grupo de recursos. Assim, somente as VMs criadas no grupo de recursos 'ContosoAppRG' obterão os segredos (certificado SSL e certificado de inicialização). As VMs que um membro da equipe de desenvolvedores/operadores criar em outro grupo de recursos não poderão obter esses segredos, mesmo que eles saibam os URIs do segredo.

Este exemplo ilustra um cenário simples. Cenários da vida real podem ser mais complexos, e talvez seja necessário ajustar as permissões para o cofre de chaves com base em suas necessidades. Por exemplo, em nosso exemplo, supomos que a equipe de segurança fornecerá as referências de chave e segredo (URIs e impressões digitais) que a equipe de desenvolvedores/operadores precisa referenciar em seus aplicativos. Portanto, eles não precisam conceder aos desenvolvedores/operadores acesso do plano de dados. Além disso, observe que esse exemplo enfoca a proteção do cofre de chaves. Considerações semelhantes também devem ser levadas em conta para proteger [suas VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamento](../storage/common/storage-security-guide.md) e outros recursos do Azure.

> [!NOTE]
> Observação: esse exemplo mostra como o acesso ao chave de cofres será bloqueado na produção. Os desenvolvedores devem ter sua própria assinatura ou grupo de recursos em que têm permissões completas para gerenciar seus cofres, VMs e conta de armazenamento onde desenvolvem o aplicativo.
> 
> 

## <a name="resources"></a>Recursos
* [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md)
  
  Esse artigo explica o Controle de Acesso Baseado em Função do Azure Active Directory.
* [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md)
  
  Esse artigo detalha as funções disponíveis internas no RBAC.
* [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Esse artigo explica os modelos de implantação clássica e implantação Resource Manager, além de explicar os benefícios de usar o Resource Manager e os grupos de recursos.
* [Gerenciar o Controle de Acesso baseado em função com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  
  Este artigo explica como gerenciar o controle de acesso baseado em função com o Azure PowerShell
* [Gerenciar o controle de acesso com base em função com a API REST](../active-directory/role-based-access-control-manage-access-rest.md)
  
  Esse artigo mostra como usar a API REST para gerenciar o RBAC.
* [Role-Based Access Control for Microsoft Azure from Ignite (Controle de Acesso Baseado em Função do Microsoft Azure do Ignite)](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Este é um link para um vídeo da conferência MS Ignite de 2015, no Channel 9. Nessa sessão, falam sobre gerenciamento de acesso e recursos de relatório no Azure, além de explorar práticas recomendadas em torno de proteção do acesso às assinaturas do Azure usando o Azure Active Directory.
* [Autorizar o acesso aos aplicativos Web usando o OAuth 2.0 e o Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)
  
  Este artigo descreve o fluxo completo do OAuth 2.0 para autenticação com o Azure Active Directory.
* [APIs REST de Gerenciamento de cofre de chaves](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Este documento é a referência para as APIs REST para gerenciar o cofre de chaves de forma programática, incluindo a configuração de política de acesso do cofre de chaves.
* [APIs REST do cofre de chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Link para a documentação de referência de API REST do cofre de chaves.
* [Controle de acesso a chave](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Vincular à documentação de referência de controle de acesso de Segredo.
* [Controle de acesso a segredo](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Vincular à documentação de referência de controle de acesso de Chave.
* [Definir](https://msdn.microsoft.com/library/mt603625.aspx) e [Remover](https://msdn.microsoft.com/library/mt619427.aspx) política de acesso do cofre de chaves usando o PowerShell
  
  Links para a documentação de referência de cmdlets do PowerShell para gerenciar a política de acesso do cofre de chaves.

## <a name="next-steps"></a>Próximas etapas
Para ver um tutorial de introdução para um administrador, confira [Introdução ao cofre de chaves do Azure](key-vault-get-started.md).

Para saber mais sobre o log de uso do cofre de chaves, confira [Log do cofre de chaves do Azure](key-vault-logging.md).

Para saber mais sobre o uso de chaves e segredos com o cofre de chaves do Azure, confira [Sobre Chaves e Segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se você tiver dúvidas sobre o cofre de chaves, acesse os [Fóruns do cofre de chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

