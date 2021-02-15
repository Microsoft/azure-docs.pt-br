---
title: Proteger o acesso a um cofre de chaves
description: O modelo de acesso para Azure Key Vault, incluindo os pontos de extremidade de autenticação e recursos de Active Directory.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: f82c7060f703aff6c19f0082454779b8fea1ba76
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526248"
---
# <a name="secure-access-to-a-key-vault"></a>Proteger o acesso a um cofre de chaves

O Azure Key Vault é um serviço de nuvem que protege segredos e chaves de criptografia, como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus cofres de chaves permitindo apenas aplicativos e usuários autorizados. Este artigo fornece uma visão geral do modelo de acesso do Key Vault. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus cofres de chaves.

Para obter mais informações sobre o Key Vault, confira [Sobre o Azure Key Vault](overview.md); para obter mais informações sobre o que pode ser armazenado em um cofre de chaves, confira [Sobre chaves, segredos e certificados](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre de chaves é controlado por meio de duas interfaces: o **plano de gerenciamento** e o **plano de dados**. É no plano de gerenciamento que você administra o cofre de chaves em si. As operações nesse plano incluem criar e excluir cofres de chaves, recuperar propriedades do Key Vault e atualizar as políticas de acesso. No plano de dados, você trabalha com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Ambos os planos usam [Azure Active Directory (AD do Azure)](../../active-directory/fundamentals/active-directory-whatis.md) para autenticação. Para autorização, o plano de gerenciamento usa o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) e o plano de dados usa uma [política de acesso Key Vault](./assign-access-policy-portal.md) e o [RBAC do Azure para Key Vault operações do plano de dados](./rbac-guide.md).

Para acessar um cofre de chaves em qualquer dos planos, todos os chamadores (usuários ou aplicativos) devem ter a autenticação e a autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais operações o chamador pode executar. A autenticação com o Key Vault funciona em conjunto com o [Azure AD (Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md), que é responsável por autenticar a identidade de qualquer **entidade de segurança**.

Uma entidade de segurança é um objeto que representa um usuário, grupo, serviço ou aplicativo que está solicitando acesso aos recursos do Azure. O Azure atribui uma **ID do objeto** exclusiva a cada entidade de segurança.

* Uma entidade de segurança de **usuário** identifica um indivíduo que tem um perfil no Azure Active Directory.

* Uma entidade de segurança de **grupo** identifica um conjunto de usuários criado no Azure Active Directory. Todas as funções ou permissões atribuídas ao grupo são concedidas a todos os usuários dentro do grupo.

* Uma **entidade de serviço** é um tipo de entidade de segurança que identifica um aplicativo ou serviço, ou seja, um trecho de código, em vez de um usuário ou grupo. A ID do objeto de uma entidade de serviço é conhecida como a **ID do cliente** dessa entidade de serviço e atua como o nome de usuário dela. O **certificado** ou **segredo do cliente** da entidade de serviço atua como sua senha. Muitos serviços do Azure dão suporte à atribuição de [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md) com o gerenciamento automatizado da **ID do cliente** e do **certificado**. A identidade gerenciada é a opção mais segura e recomendada para autenticação no Azure.

Para obter mais informações sobre autenticação para Key Vault, consulte [autenticar para Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Opções de autenticação Key Vault

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Todos os chamadores em ambos os planos devem se registrar neste locatário e se autenticar para acessar o cofre de chaves. Em ambos os casos, os aplicativos podem acessar Key Vault de três maneiras:

- **Somente aplicativo**: o aplicativo representa uma entidade de serviço ou uma identidade gerenciada. Essa identidade é o cenário mais comum para aplicativos que periodicamente precisam acessar certificados, chaves ou segredos do cofre de chaves. Para que esse cenário funcione, o `objectId` do aplicativo deve ser especificado na política de acesso e `applicationId` _não_ deve ser especificado ou deve ser `null` .
- **Somente usuário**: o usuário acessa o cofre de chaves de qualquer aplicativo registrado no locatário. Exemplos deste tipo de acesso incluem o Azure PowerShell e o portal do Azure. Para que esse cenário funcione, o `objectId` do usuário deve ser especificado na política de acesso e `applicationId` _não_ deve ser especificado ou deve ser `null` .
- **Application-Plus-User** (às vezes chamado de _identidade composta_): o usuário é solicitado a acessar o cofre de chaves de um aplicativo específico _e_ o aplicativo deve usar o fluxo de autenticação em nome de (obo) para representar o usuário. Para que esse cenário funcione, `applicationId` e `objectId` deve ser especificado na política de acesso. O `applicationId` identifica o aplicativo necessário e o `objectId` identifica o usuário. Atualmente, essa opção não está disponível para o Azure RBAC (visualização) do plano de dados.

Em todos os tipos de acesso, o aplicativo é autenticado com o Azure AD. O aplicativo usa qualquer [método de autenticação compatível](../../active-directory/develop/authentication-vs-authorization.md) com base no tipo de aplicativo. O aplicativo adquire um token para um recurso no plano para conceder acesso. O recurso é um ponto de extremidade no plano de gerenciamento ou de dados, com base no ambiente do Azure. O aplicativo usa esse token e envia uma solicitação da API REST ao Key Vault. Para saber mais, examine o [fluxo de autenticação completo](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

O modelo de mecanismo único para autenticação em ambos os planos tem vários benefícios:

- As organizações podem controlar centralmente o acesso a todos os cofres de chaves em sua organização.
- Se um usuário parte, ele perde instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure Active Directory, como habilitar a autenticação multifator para obter maior segurança.

## <a name="resource-endpoints"></a>Pontos de extremidade do recurso

Os aplicativos acessam os planos por meio de pontos de extremidade. Os controles de acesso dos dois planos trabalham de forma independente. Para conceder a um aplicativo acesso para usar chaves em um cofre de chaves, você concede acesso ao plano de dados usando uma política de acesso Key Vault ou RBAC do Azure (versão prévia). Para conceder a um usuário acesso de leitura a Key Vault Propriedades e marcas, mas não ao acesso a dados (chaves, segredos ou certificados), você concede acesso ao plano de gerenciamento com o RBAC do Azure.

A tabela a seguir mostra os pontos de extremidade para os planos de gerenciamento e de dados.

| Plano de&nbsp;acesso | Pontos de extremidade de acesso | Operações | Mecanismo de controle de&nbsp;acesso |
| --- | --- | --- | --- |
| Plano de gerenciamento | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e excluir cofres de chaves<br><br>Definir políticas de acesso do Key Vault<br><br>Definir marcas do Key Vault | RBAC do Azure |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governo dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: criptografar, descriptografar, wrapKey, unwrapKey, assinar, verificar, obter, listar, criar, atualizar, importar, excluir, recuperar, fazer backup, restaurar, limpar<br><br> Certificados: managecontacts, getemissores, listissuers, setemissores, deleteissuers, manageissuers, obter, listar, criar, importar, atualizar, excluir, recuperar, backup, restaurar, limpar<br><br>  Segredos: obter, listar, definir, excluir, recuperar, fazer backup, restaurar, limpar | Política de acesso de Key Vault ou RBAC do Azure (versão prévia)|

## <a name="management-plane-and-azure-rbac"></a>Plano de gerenciamento e RBAC do Azure

No plano de gerenciamento, use o [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) para autorizar as operações que um chamador pode executar. No modelo RBAC do Azure, cada assinatura do Azure tem uma instância do Azure AD. Você pode conceder acesso a usuários, grupos e aplicativos desse diretório. O acesso é concedido para gerenciar recursos na assinatura do Azure que usa o modelo de implantação do Azure Resource Manager.

Crie um cofre de chaves em um grupo de recursos e gerencie o acesso usando o Azure Active Directory. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Conceda o acesso em um nível de escopo específico atribuindo funções apropriadas do Azure. Para conceder acesso a um usuário para gerenciar cofres de chaves, você atribui uma função predefinida de [Key Vault colaborador](../../role-based-access-control/built-in-roles.md#key-vault-contributor) para o usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função do Azure:

- **Assinatura**: uma função do Azure atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função do Azure atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: uma função do Azure atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, veja [Funções internas do Azure](../../role-based-access-control/built-in-roles.md). 

Você precisa ter `Microsoft.Authorization/roleAssignments/write` permissões e `Microsoft.Authorization/roleAssignments/delete` , como administrador de [acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [proprietário](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados definindo a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Políticas de acesso e plano de dados

Você pode conceder acesso ao plano de dados definindo Key Vault políticas de acesso para um cofre de chaves. Para definir essas políticas de acesso, o usuário, grupo ou aplicativo deve ter permissões `Key Vault Contributor` para o plano de gerenciamento desse cofre de chaves.

Conceda acesso a um usuário, grupo ou aplicativo para executar operações específicas para chaves ou segredos em um cofre de chaves. O Key Vault dá suporte a até 1.024 entradas de política de acesso para um cofre de chaves. Para conceder acesso ao plano de dados a vários usuários, crie um grupo de segurança do Azure Active Directory e adicione usuários a esse grupo.

Você pode ver a lista completa de operações de cofre e segredo aqui: [referência de operação de Key Vault](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault políticas de acesso concedem permissões separadamente a chaves, segredos e certificados.  As permissões de acesso para chaves, segredos e certificados estão no nível do cofre. 

Para obter mais informações sobre como usar as políticas de acesso do cofre de chaves, consulte [atribuir uma política de acesso Key Vault](assign-access-policy-portal.md)

> [!IMPORTANT]
> As políticas de acesso ao cofre de chaves aplicam-se no nível do cofre. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.
As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Plano de dados e RBAC do Azure (versão prévia)

O controle de acesso baseado em função do Azure é um modelo de permissão alternativo para controlar o acesso ao Azure Key Vault plano de dados, que pode ser habilitado em cofres de chaves individuais. O modelo de permissão RBAC do Azure é exclusivo e, uma vez definido, as políticas de acesso do cofre se tornaram inativas. Azure Key Vault define um conjunto de funções internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar chaves, segredos ou certificados.

Quando uma função do Azure é atribuída a uma entidade de segurança do Azure AD, o Azure concede acesso a esses recursos para essa entidade de segurança. O acesso pode ser definido para o nível da assinatura, o grupo de recursos, o cofre de chaves ou uma chave, segredo ou certificado individual. Uma entidade de segurança do Azure AD pode ser um usuário, um grupo, uma entidade de serviço de aplicativo ou uma [identidade gerenciada para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Os principais benefícios do uso da permissão RBAC do Azure em políticas de acesso do cofre são o gerenciamento de controle de acesso centralizado e sua integração com o [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md). O Privileged Identity Management fornece ativação de função baseada em tempo e aprovação para atenuar os riscos de permissões de acesso excessivas, desnecessárias ou que foram indevidamente utilizadas em recursos importantes.

Para obter mais informações sobre Key Vault plano de dados com o RBAC do Azure, consulte [Key Vault chaves, certificados e segredos com um controle de acesso baseado em função do Azure](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais

Para obter uma camada adicional de segurança, você pode configurar firewalls e regras de rede virtual. Por padrão, é possível configurar redes virtuais e firewalls do Key Vault para negar acesso ao tráfego de todas as redes (incluindo o tráfego de Internet). Você pode conceder acesso ao tráfego de [redes virtuais do Azure](../../virtual-network/virtual-networks-overview.md) específicas e intervalos de endereços IP públicos da Internet, permitindo que você crie um limite de rede seguro para seus aplicativos.

Seguem alguns exemplos de como é possível usar pontos de extremidade de serviço:

* Se você estiver usando o Key Vault para armazenar chaves de criptografia, segredos do aplicativo e certificados, e quer bloquear o acesso ao seu cofre de chaves da Internet pública.
* Você quer bloquear o acesso ao seu cofre de chaves para que apenas o seu aplicativo ou uma pequena lista de hosts designados possa conectar o cofre de chaves.
* Você tem um aplicativo executando na rede virtual do Azure e essa rede virtual é bloqueada para todo o tráfego de entrada e saída. O aplicativo ainda precisa conectar ao Key Vault para efetuar fetch de segredos ou certificados ou usar chaves de criptografia.

Para obter mais informações sobre Key Vault firewall e redes virtuais, consulte [configurar Azure Key Vault firewalls e redes virtuais](network-security.md)

> [!NOTE]
> As regras da rede virtual e os firewalls do Key Vault aplicam-se somente ao plano de dados do Key Vault. Operações do plano de controle do Key Vault (como criar, excluir, modificar operações, definir políticas de acesso, configurar firewalls e regras da rede virtual) não são afetadas por regras da rede virtual e firewalls.

## <a name="private-endpoint-connection"></a>Conexão de ponto de extremidade particular

No caso de uma necessidade de bloquear completamente Key Vault exposição ao público, um [ponto de extremidade privado do Azure](../../private-link/private-endpoint-overview.md) pode ser usado. Um Ponto de Extremidade Privado do Azure é um adaptador de rede que conecta você de maneira privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.

Cenários comuns para usar o link privado para os serviços do Azure:

- **Acesse os serviços de forma privada na plataforma Azure**: Conecte sua rede virtual a serviços no Azure sem um endereço IP público na origem ou no destino. Os provedores de serviços podem renderizar seus serviços em sua rede virtual e os consumidores podem acessar esses serviços em sua rede virtual local. A plataforma de Link Privado manipulará a conectividade entre o consumidor e os serviços na rede de backbone do Azure. 
 
- **Redes locais e emparelhadas**: Acesse serviços em execução no Azure do local por meio de emparelhamento privado do ExpressRoute, túneis de VPN e redes virtuais emparelhadas usando pontos de extremidade privados. Não é necessário configurar o emparelhamento público ou atravessar a Internet para acessar o serviço. O Link Privado fornece uma forma segura de migrar cargas de trabalho para o Azure.
 
- **Proteção contra vazamento de dados**: um ponto de extremidade privado é mapeado para uma instância de um recurso de PaaS em vez de todo o serviço. Os consumidores só podem se conectar ao recurso específico. O acesso a qualquer outro recurso no serviço é bloqueado. Esse mecanismo fornece proteção contra riscos de vazamento de dados. 
 
- **Alcance Global**: Conecte-se de maneira privada aos serviços executados em outras regiões. A rede virtual do consumidor pode estar na região A e pode se conectar aos serviços por trás do Link Privado na região B.  
 
- **Estenda para seus próprios serviços**: Habilite a mesma experiência e a mesma funcionalidade para renderizar seu serviço de maneira privada para consumidores no Azure. Colocando seu serviço atrás de um Azure Load Balancer padrão, você pode habilitá-lo para o Link Privado. O consumidor pode então se conectar diretamente ao seu serviço usando um ponto de extremidade privado em sua rede virtual. Você pode gerenciar as solicitações de conexão usando um fluxo de chamada de aprovação. O Link Privado do Azure funciona para consumidores e serviços que pertencem a locatários diferentes do Azure Active Directory. 

Para obter mais informações sobre pontos de extremidade privados, consulte [Key Vault com o link privado do Azure](./private-link-service.md)

## <a name="example"></a>Exemplo

Neste exemplo, estamos desenvolvendo um aplicativo que usa um certificado para TLS/SSL, armazenamento do Azure para armazenar dados e uma chave RSA de 2.048 bits para criptografar dados no armazenamento do Azure. Nosso aplicativo é executado em uma máquina virtual (VM) do Azure (ou em um conjunto de dimensionamento de máquinas virtuais). Podemos usar um cofre de chaves para armazenar os segredos do aplicativo. Podemos armazenar o certificado de inicialização usado pelo aplicativo para se autenticar com o Azure Active Directory.

Precisamos de acesso aos seguintes segredos e chaves armazenados:
- **Certificado TLS/SSL**: Usado para TLS/SSL.
- **Chave de armazenamento**: usada para acessar a conta de armazenamento.
- **Chave RSA de 2.048 bits**: usada para encapsular/desencapsular a chave de criptografia de dados pelo armazenamento do Azure.
- **Identidade gerenciada do aplicativo**: usada para autenticar com o Azure AD. Depois que o acesso ao Key Vault é concedido, o aplicativo pode buscar a chave de armazenamento e o certificado.

É preciso definir as seguintes funções para especificar quem pode gerenciar, implantar e auditar nosso aplicativo:
- **Equipe de segurança**: a equipe de TI do escritório do Diretor de segurança ou colaboradores equivalentes. A equipe de segurança é responsável por guardar adequadamente os segredos. Os segredos podem incluir certificados TLS/SSL, chaves RSA para criptografia, cadeias de conexão e chaves de conta de armazenamento.
- **Desenvolvedores e operadores**: a equipe que desenvolve o aplicativo e o implanta no Azure. Os membros desta equipe não fazem parte da equipe de segurança. Eles não devem ter acesso a dados confidenciais, como certificados TLS/SSL e chaves RSA. Somente o aplicativo que eles implantam deve ter acesso aos dados confidenciais.
- **Auditores**: essa função é para colaboradores que não são membros da equipe de desenvolvimento ou de TI geral. Eles analisam o uso e a manutenção de certificados, chaves e segredos para garantir a conformidade com padrões de segurança.

Há outra função que está fora do escopo do nosso aplicativo: o administrador da assinatura (ou do grupo de recursos). O administrador da assinatura define permissões de acesso inicial para a equipe de segurança. Ele concede acesso à equipe de segurança usando um grupo de recursos que tem os recursos exigidos pelo aplicativo.

É necessário autorizar as seguintes operações para as funções:

**Equipe de segurança**
- Criar cofres de chaves.
- Ativar o registro em log do Key Vault.
- Adicionar chaves e segredos.
- Criar backups de chaves para recuperação de desastre.
- Defina políticas de acesso de Key Vault e atribua funções para conceder permissões a usuários e aplicativos para operações específicas.
- Distribuir periodicamente as chaves e segredos.

**Desenvolvedores e operadores**
- Obtenha referências da equipe de segurança para os certificados Bootstrap e TLS/SSL (impressão digital), chave de armazenamento (URI secreto) e chave RSA (URI de chave) para encapsular/desencapsular.
- Desenvolva e implante o aplicativo para acessar certificados e segredos de forma programática.

**Auditores**
- Examinar os logs do Key Vault para confirmar o uso adequado de chaves e segredos e a conformidade com padrões de segurança de dados.

A tabela a seguir resume as permissões de acesso para as funções e o aplicativo.

| Função | Permissões do plano de gerenciamento | Permissões do plano de dados-políticas de acesso do cofre | Permissões de plano de dados-RBAC do Azure (versão prévia)  |
| --- | --- | --- | --- |
| Equipe de segurança | [Colaborador do Key Vault](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certificados: todas as operações <br> Chaves: todas as operações <br> Segredos: todas as operações | [Key Vault administrador (versão prévia)](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) |
| Desenvolvedores e&nbsp;operadores | Permissão para implantar o Key Vault<br><br> **Observação**: essa permissão possibilita que as VMs implantadas busquem segredos do cofre de chaves. | Nenhum | Nenhum |
| Auditores | Nenhum | Certificados: lista <br> Chaves: lista<br>Segredos: lista<br><br> **Observação**: essa permissão possibilita que os auditores inspecionem os atributos (marcas, datas de ativação, datas de validade) de chaves e segredos que não são emitidos nos logs. | [Leitor de Key Vault (versão prévia)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview) |
| Conta de Armazenamento do Azure | Nenhum | Chaves: obter, lista, wrapKey, unwrapKey <br> | [Key Vault usuário de criptografia do serviço de criptografia](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user-preview) |
| Aplicativo | Nenhum | Segredos: obter, listar <br> Certificados: obter, listar | [Leitor de Key Vault (visualização)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview), [Key Vault usuário de segredo (versão prévia)](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user-preview) |

As três funções da equipe precisam ter acesso a outros recursos e a permissões do Key Vault. Para implantar VMs (ou o recurso aplicativos Web do serviço Azure App), os desenvolvedores e operadores precisam de acesso de implantação. Os auditores precisam de acesso de leitura para a Conta de armazenamento em que os logs do Key Vault estão armazenados.

Este exemplo descreve um cenário simples. Os cenários da vida real podem ser mais complexos. Você pode ajustar as permissões do cofre de chaves com base em suas necessidades. Vamos presumir que a equipe de segurança forneceu as referências de chave e segredo (URIs e impressões digitais), usadas pela equipe de DevOps nos aplicativos. Desenvolvedores e operadores não precisam de nenhum acesso ao plano de dados. Vimos como proteger seu cofre de chaves.

> [!NOTE]
> Esse exemplo mostra como o acesso ao Key Vault é bloqueado na produção. Os desenvolvedores devem ter sua própria assinatura ou grupo de recursos com permissões totais para gerenciar seus cofres, VMs e a conta de armazenamento onde desenvolvem o aplicativo.

## <a name="resources"></a>Recursos

- [Sobre o Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [RBAC do Azure](../../role-based-access-control/overview.md)
- [Link privado](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Autenticar-se no Azure Key Vault](authentication.md)

[Atribuir uma política de acesso de Key Vault](assign-access-policy-portal.md)

[Atribuir função do Azure para acessar chaves, segredos e certificados](rbac-guide.md)

[Configurar Key Vault firewalls e redes virtuais](network-security.md)

[Estabelecer uma conexão de link privado com o Key Vault](private-link-service.md)