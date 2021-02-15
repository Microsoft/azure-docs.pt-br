---
title: Visão geral da segurança do Azure Key Vault
description: Uma visão geral dos recursos de segurança e das práticas recomendadas para Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071555"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

Você usa Azure Key Vault para proteger chaves de criptografia e segredos como certificados, cadeias de conexão e senhas na nuvem. Ao armazenar dados confidenciais e críticos para os negócios, você precisa tomar medidas para maximizar a segurança de seus cofres e os dados armazenados neles.

Este artigo fornece uma visão geral dos recursos de segurança e das práticas recomendadas para Azure Key Vault. 

> [!NOTE]
> Para obter uma lista abrangente de Azure Key Vault recomendações de segurança, consulte a [linha de base de segurança para Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Segurança de rede

Reduza a exposição de seus cofres especificando quais endereços IP têm acesso a eles. Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo de Internet versão 4). Qualquer usuário que conecte o cofre de chaves de fora dessas fontes terá acesso negado.  Para obter detalhes completos, consulte [pontos de extremidade de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md)

Depois que as regras de firewall estiverem ativas, os usuários podem apenas ler os dados do Key Vault quando as solicitações deles originarem-se de redes virtuais ou intervalos de endereços IPv4 permitidos. Isso também aplica-se ao acessar o Key Vault a partir do portal do Azure. Embora os usuários possam navegar em um cofre de chaves a partir do portal do Azure, eles talvez não consigam listar chaves, segredos ou certificados se o computador cliente deles não estiver na lista permitida. Isso também afeta o Seletor do Cofre de Chaves de outros serviços do Azure. Os usuários poderão ver a lista de cofres de chaves, mas não listar chaves, se as regras de firewall impedirem o computador cliente.  Para obter as etapas de implementação, consulte [configurar Azure Key Vault firewalls e redes virtuais](network-security.md)

O Serviço de Link Privado do Azure permite que você acesse o Azure Key Vault e os serviços de parceiros/clientes hospedados no Azure em um ponto de extremidade privado na sua rede virtual. Um Ponto de Extremidade Privado do Azure é um adaptador de rede que conecta você de maneira privada e segura a um serviço com tecnologia do Link Privado do Azure. O ponto de extremidade privado usa um endereço IP privado de sua VNet, colocando efetivamente em sua VNet. Todo o tráfego para o serviço pode ser roteado por meio do ponto de extremidade privado; assim, nenhum gateway, nenhum dispositivo NAT, nenhuma conexão ExpressRoute ou VPN e nenhum endereço IP público é necessário. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Você pode se conectar a uma instância de um recurso do Azure, fornecendo o nível mais alto de granularidade no controle de acesso.  Para obter as etapas de implementação, consulte [integrar Key Vault com o link privado do Azure](private-link-service.md)

## <a name="tls-and-https"></a>TLS e HTTPS

- O front-end de Key Vault (plano de dados) é um servidor de vários locatários. Isso significa que os cofres de chaves de diferentes clientes podem compartilhar o mesmo endereço IP público. Para alcançar o isolamento, cada solicitação HTTP é autenticada e autorizada independentemente de outras solicitações.
- Você pode identificar versões mais antigas do TLS para relatar vulnerabilidades, mas como o endereço IP público é compartilhado, não é possível que a equipe de serviço do cofre de chaves desabilite versões antigas do TLS para cofres de chaves individuais no nível de transporte.
- O protocolo HTTPS permite que o cliente participe da negociação TLS. Os **clientes podem impor a versão mais recente do TLS** e sempre que um cliente faz isso, a conexão inteira usará a proteção de nível correspondente. O fato de que Key Vault ainda oferece suporte a versões mais antigas do TLS não prejudicará a segurança das conexões usando versões mais recentes do TLS.
- Apesar de vulnerabilidades conhecidas no protocolo TLS, não há nenhum ataque conhecido que permita que um agente mal-intencionado extraia todas as informações do cofre de chaves quando o invasor inicia uma conexão com uma versão TLS que tem vulnerabilidades. O invasor ainda precisaria se autenticar e se autorizar e, desde que os clientes legítimos sempre se conectem com versões recentes do TLS, não há nenhuma maneira de que as credenciais pudessem ter sido vazadas de vulnerabilidades em versões antigas do TLS.

## <a name="identity-management"></a>Gerenciamento de identidades

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure Active Directory da assinatura. Qualquer pessoa que tentar gerenciar ou recuperar conteúdo de um cofre deve ser autenticada pelo Azure AD. Em ambos os casos, os aplicativos podem acessar Key Vault de três maneiras:

- **Somente aplicativo**: o aplicativo representa uma entidade de serviço ou uma identidade gerenciada. Essa identidade é o cenário mais comum para aplicativos que periodicamente precisam acessar certificados, chaves ou segredos do cofre de chaves. Para que esse cenário funcione, o `objectId` do aplicativo deve ser especificado na política de acesso e `applicationId` _não_ deve ser especificado ou deve ser `null` .
- **Somente usuário**: o usuário acessa o cofre de chaves de qualquer aplicativo registrado no locatário. Exemplos deste tipo de acesso incluem o Azure PowerShell e o portal do Azure. Para que esse cenário funcione, o `objectId` do usuário deve ser especificado na política de acesso e `applicationId` _não_ deve ser especificado ou deve ser `null` .
- **Application-Plus-User** (às vezes chamado de _identidade composta_): o usuário é solicitado a acessar o cofre de chaves de um aplicativo específico _e_ o aplicativo deve usar o fluxo de autenticação em nome de (obo) para representar o usuário. Para que esse cenário funcione, `applicationId` e `objectId` deve ser especificado na política de acesso. O `applicationId` identifica o aplicativo necessário e o `objectId` identifica o usuário. Atualmente, essa opção não está disponível para o Azure RBAC (visualização) do plano de dados.

Em todos os tipos de acesso, o aplicativo é autenticado com o Azure AD. O aplicativo usa qualquer [método de autenticação compatível](../../active-directory/develop/authentication-vs-authorization.md) com base no tipo de aplicativo. O aplicativo adquire um token para um recurso no plano para conceder acesso. O recurso é um ponto de extremidade no plano de gerenciamento ou de dados, com base no ambiente do Azure. O aplicativo usa esse token e envia uma solicitação da API REST ao Key Vault. Para saber mais, examine o [fluxo de autenticação completo](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Para obter detalhes completos, consulte [conceitos básicos de autenticação do Key Vault](authentication-fundamentals.md)

## <a name="privileged-access"></a>Acesso privilegiado

A autorização determina quais operações o chamador pode realizar. A autorização no Key Vault usa uma combinação de [controle de acesso baseado em função do Azure (RBAC do Azure)](../../role-based-access-control/overview.md) e políticas de acesso de Azure Key Vault.

O acesso aos cofres é feito através de duas interfaces ou planos. Os planos são o plano de gerenciamento e o plano de dados.

- O *plano de gerenciamento* é onde você gerencia o Key Vault em si é a interface usada para criar e excluir cofres. Também é possível ler as propriedades do cofre de chaves e gerenciar políticas de acesso.
- O *plano de dados* permite trabalhar com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e alterar chaves, segredos e certificados.

Os aplicativos acessam os planos por meio de pontos de extremidade. Os controles de acesso dos dois planos trabalham de forma independente. Para conceder a um aplicativo acesso para usar chaves em um cofre de chaves, você concede acesso ao plano de dados usando uma política de acesso Key Vault ou RBAC do Azure (versão prévia). Para conceder a um usuário acesso de leitura a Key Vault Propriedades e marcas, mas não ao acesso a dados (chaves, segredos ou certificados), você concede acesso ao plano de gerenciamento com o RBAC do Azure.

A tabela a seguir mostra os pontos de extremidade para os planos de gerenciamento e de dados.

| Plano de&nbsp;acesso | Pontos de extremidade de acesso | Operações | Mecanismo de controle de&nbsp;acesso |
| --- | --- | --- | --- |
| Plano de gerenciamento | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure Governo dos EUA:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e excluir cofres de chaves<br><br>Definir políticas de acesso do Key Vault<br><br>Definir marcas do Key Vault | RBAC do Azure |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure Governo dos EUA:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: criptografar, descriptografar, wrapKey, unwrapKey, assinar, verificar, obter, listar, criar, atualizar, importar, excluir, recuperar, fazer backup, restaurar, limpar<br><br> Certificados: managecontacts, getemissores, listissuers, setemissores, deleteissuers, manageissuers, obter, listar, criar, importar, atualizar, excluir, recuperar, backup, restaurar, limpar<br><br>  Segredos: obter, listar, definir, excluir, recuperar, fazer backup, restaurar, limpar | Política de acesso de Key Vault ou RBAC do Azure (versão prévia)|

### <a name="managing-administrative-access-to-key-vault"></a>Gerenciamento de acesso administrativo ao Key Vault

Quando criar um cofre de chaves em um grupo de recursos, gerencie o acesso usando o Azure AD. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Você pode conceder acesso em um nível de escopo específico atribuindo as funções apropriadas do Azure. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função do Azure:

- **Assinatura**: uma função do Azure atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: uma função do Azure atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: uma função do Azure atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, consulte [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tem permissões `Contributor` para um plano de gerenciamento de cofre de chaves, pode conceder a si mesmo o acesso ao plano de dados definindo a política de acesso do Key Vault. Você deve controlar rigorosamente quem tem função de acesso `Contributor` aos cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.

### <a name="controlling-access-to-key-vault-data"></a>Controle do acesso aos dados do Key Vault

As políticas de acesso ao Key Vault concedem separadamente as permissões a chaves, segredos ou certificados. Você pode conceder a um usuário o acesso apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados são gerenciadas no nível do cofre.

> [!IMPORTANT]
> As políticas de acesso do Key Vault não aceitam permissões granulares no nível do objeto, como uma chave, um segredo ou um certificado específicos. Quando um usuário tem permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves no cofre de chaves.

Você pode definir políticas de acesso para um cofre de chaves usar as [portal do Azure](assign-access-policy-portal.md), [CLI do Azure](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)ou as [APIs REST de gerenciamento de Key Vault](/rest/api/keyvault/).

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual no Azure Key Vault](overview-vnet-service-endpoints.md)). É possível configurar [regras de firewalls e da rede virtual](network-security.md) para ter uma camada adicional de segurança.

## <a name="logging-and-monitoring"></a>Log e monitoramento

O registrar em log do Key Vault salva informações sobre as atividades executadas em seu cofre. Para obter detalhes completos, consulte [log de Key Vault](logging.md).

Você pode integrar Key Vault com a grade de eventos para ser notificado quando o status de uma chave, certificado ou segredo armazenado no cofre de chaves for alterado. Para obter detalhes, consulte [monitoramento Key Vault com a grade de eventos do Azure](event-grid-overview.md)

Também é importante monitorar a integridade do cofre de chaves para garantir que o serviço funcione conforme o esperado. Para saber como fazer isso, consulte [monitoramento e alertas para Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Backup e recuperação

Azure Key Vault a proteção de exclusão e limpeza reversível permite recuperar cofres excluídos e objetos de cofre. Para obter detalhes completos, consulte [visão geral da exclusão de Azure Key Vault reversível](soft-delete-overview.md).

Você também deve fazer backups regulares de seu cofre em atualizar/excluir/criar objetos em um cofre.  

## <a name="next-steps"></a>Próximas etapas

- [Azure Key Vault linha de base de segurança](security-baseline.md)
- [Melhores práticas do Azure Key Vault](security-baseline.md)
- [Pontos de extremidade de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md)
