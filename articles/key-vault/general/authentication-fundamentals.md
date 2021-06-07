---
title: Conceitos básicos de autenticação Azure Key Vault
description: Saiba mais sobre como funciona o modelo de autenticação do Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103572801"
---
# <a name="key-vault-authentication-fundamentals"></a>Conceitos básicos sobre a Autenticação do Key Vault

Azure Key Vault permite armazenar e gerenciar com segurança as credenciais do aplicativo, como segredos, chaves e certificados em um repositório de nuvem central e seguro. Key Vault elimina a necessidade de armazenar credenciais em seus aplicativos. Seus aplicativos podem se autenticar em Key Vault em tempo de execução para recuperar credenciais.

Como administrador, você pode controlar rigidamente quais usuários e aplicativos podem acessar o cofre de chaves e pode limitar e auditar as operações que eles executam. Este documento explica os conceitos fundamentais do modelo de acesso do cofre de chaves. Ele fornecerá um nível introdutório de conhecimento e mostrará como você pode autenticar um usuário ou aplicativo no Key Vault do início ao fim.

## <a name="required-knowledge"></a>Conhecimento necessário

Este documento pressupõe que você esteja familiarizado com os conceitos a seguir. Se você não estiver familiarizado com nenhum desses conceitos, siga os links da ajuda antes de continuar.

* Azure Active Directory [link](../../active-directory/fundamentals/active-directory-whatis.md)
* [Link](./authentication.md#app-identity-and-security-principals) de entidades de segurança

## <a name="key-vault-configuration-steps-summary"></a>Resumo das etapas de configuração do Key Vault

1. Registre seu usuário ou aplicativo no Azure Active Directory como uma entidade de segurança.
1. Configure uma atribuição de função para sua entidade de segurança no Azure Active Directory.
1. Configure as políticas de acesso do cofre de chaves para sua entidade de segurança.
1. Configure Key Vault acesso de firewall ao cofre de chaves (opcional).
1. Teste a capacidade da sua entidade de segurança de acessar o cofre de chaves.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrar um usuário ou aplicativo no Azure Active Directory como uma entidade de segurança

Quando um usuário ou aplicativo faz uma solicitação para o cofre de chaves, a solicitação deve primeiro ser autenticada pelo Azure Active Directory. Para que isso funcione, o usuário ou aplicativo precisa ser registrado em Azure Active Directory como uma entidade de segurança.

Siga os links de documentação abaixo para entender como registrar um usuário ou aplicativo no Azure Active Directory.
**Certifique-se de criar uma senha para o registro de usuário e um segredo do cliente ou uma credencial de certificado de cliente para aplicativos.**

* Registrando um usuário no Azure Active Directory [link](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* Registrando um aplicativo no Azure Active Directory [link](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Atribuir sua entidade de segurança a uma função

Você pode usar o controle de acesso baseado em função do Azure (RBAC do Azure) para atribuir permissões a entidades de segurança. Essas permissões são chamadas de atribuições de função.

No contexto do cofre de chaves, essas atribuições de função determinam o nível de acesso da entidade de segurança ao plano de gerenciamento (também conhecido como plano de controle) do cofre de chaves. Essas atribuições de função não fornecem acesso aos segredos do plano de dados diretamente, mas fornecem acesso para gerenciar Propriedades do cofre de chaves. Por exemplo, um usuário ou aplicativo atribuído a uma **função de leitor** não terá permissão para fazer alterações nas configurações de firewall do Key Vault, enquanto um usuário ou aplicativo atribuído a uma **função colaborador** pode fazer alterações. Nenhuma função terá acesso direto para executar operações em segredos, chaves e certificados, como criar ou recuperar seu valor até receber acesso ao plano de dados do cofre de chaves. Isso é abordado na próxima etapa.

>[!IMPORTANT]
> Embora os usuários com a função colaborador ou proprietário não tenham acesso para executar operações em segredos armazenados no Key Vault por padrão, as funções colaborador e proprietário, fornecem permissões para adicionar ou remover políticas de acesso para segredos armazenados no cofre de chaves. Portanto, um usuário com essas atribuições de função pode conceder a si mesmo acesso aos segredos de acesso no cofre de chaves. Por esse motivo, é recomendável que somente os administradores tenham acesso às funções de colaborador ou de proprietário. Os usuários e aplicativos que precisam apenas recuperar segredos do Key Vault devem receber a função leitor. **Mais detalhes na próxima seção.**

>[!NOTE]
> Quando você atribui uma atribuição de função a um usuário no nível de Azure Active Directory locatário, esse conjunto de permissões será trickledo a todas as assinaturas, grupos de recursos e recursos dentro do escopo da atribuição. Para seguir a entidade de privilégio mínimo, você pode fazer essa atribuição de função em um escopo mais granular. Por exemplo, você pode atribuir um usuário a uma função de leitor no nível de assinatura e uma função de proprietário para um único cofre de chaves. Vá para as configurações de IAM (gerenciamento de acesso de identidade) de uma assinatura, grupo de recursos ou cofre de chaves para fazer uma atribuição de função em um escopo mais granular.

* Para saber mais sobre o [link](../../role-based-access-control/built-in-roles.md) de funções do Azure
* Para saber mais sobre como atribuir ou remover o [link](../../role-based-access-control/role-assignments-portal.md) de atribuições de função

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Configurar políticas de acesso do cofre de chaves para sua entidade de segurança

Antes de conceder acesso para seus usuários e aplicativos acessarem o cofre de chaves, é importante entender os diferentes tipos de operações que podem ser executadas em um cofre de chaves. Há dois tipos principais de operações de cofre de chaves, operações de plano de gerenciamento (também chamadas de plano de controle) e operações de plano de dados.

Esta tabela mostra vários exemplos das diferentes operações que são controladas pelo plano de gerenciamento versus o plano de dados. As operações que alteram as propriedades do cofre de chaves são operações do plano de gerenciamento. As operações que alteram ou recuperam o valor dos segredos armazenados no Key Vault são operações do plano de dados.

|Operações do plano de gerenciamento (exemplos)|Operações do plano de dados (exemplos)|
| --- | --- |
| Criar Cofre da Chave | Criar uma chave, um segredo, um certificado
| Excluir Key Vault | Excluir uma chave, um segredo, um certificado
| Adicionar ou remover atribuições de função Key Vault | Listar e obter valores de chaves, segredos, certificados
| Adicionar ou remover políticas de acesso de Key Vault | Chaves de backup e restauração, segredos, certificados
| Modificar Key Vault configurações de firewall | Renovar chaves, segredos, certificados
| Modificar Key Vault configurações de recuperação | Limpar ou recuperar chaves, segredos, certificados excluídos de maneira reversível
| Modificar Key Vault configurações de logs de diagnóstico

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Acesso ao plano de gerenciamento & atribuições de função Azure Active Directory

Azure Active Directory atribuições de função concedem acesso para executar operações de plano de gerenciamento em um cofre de chaves. Esse acesso é normalmente concedido aos usuários, não a aplicativos. Você pode restringir quais operações do plano de gerenciamento um usuário pode executar alterando a atribuição de função de um usuário.

Por exemplo, atribuir a um usuário uma função de leitor de Key Vault a um usuário permitirá que eles vejam as propriedades do cofre de chaves, como políticas de acesso, mas não permitirão que eles façam alterações. Atribuir um usuário, uma função de proprietário permitirá acesso completo para alterar as configurações do plano de gerenciamento do cofre de chaves.

As atribuições de função são controladas na folha IAM (controle de acesso do cofre de chaves). Se você quiser que um usuário específico tenha acesso a um leitor ou seja o administrador de vários recursos do cofre de chaves, poderá criar uma atribuição de função no cofre, no grupo de recursos ou no nível de assinatura, e a atribuição de função será adicionada a todos os recursos dentro do escopo da atribuição.

O acesso ao plano de dados ou o acesso para executar operações em chaves, segredos e certificados armazenados no Key Vault pode ser adicionado de duas maneiras.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Opção de acesso do plano de dados 1: políticas de acesso Key Vault clássicas

As políticas de acesso do cofre de chaves concedem aos usuários e aplicativos acesso para executar operações de plano de dados em um cofre de chaves.

> [!NOTE]
> Este modelo de acesso não é compatível com o RBAC do Azure para o Key Vault (opção 2) documentado abaixo. Você deve escolher um. Você terá a oportunidade de fazer essa seleção ao clicar na guia política de acesso do cofre de chaves.

As políticas de acesso clássico são granulares, o que significa que você pode permitir ou negar a capacidade de cada usuário ou aplicativo individual executar operações individuais em um cofre de chaves. Veja alguns exemplos:

* A entidade de segurança 1 pode executar qualquer operação de chave, mas não tem permissão para executar qualquer operação secreta ou de certificado.
* A entidade de segurança 2 pode listar e ler todas as chaves, segredos e certificados, mas não pode executar nenhuma operação de criação, exclusão ou renovação.
* A entidade de segurança 3 pode fazer backup e restaurar todos os segredos, mas não pode ler o valor dos próprios próprios segredos.

No entanto, as políticas de acesso clássico não permitem permissões de nível por objeto e as permissões atribuídas são aplicadas ao escopo de um cofre de chaves individual. Por exemplo, se você conceder a permissão de política de acesso "segredo Get" a uma entidade de segurança em um cofre de chaves específico, a entidade de segurança terá a capacidade de obter todos os segredos dentro desse cofre de chaves específico. No entanto, essa permissão de "obter segredo" não será estendida automaticamente para outros cofres de chaves e deverá ser atribuída explicitamente.

> [!IMPORTANT]
> As políticas de acesso do cofre de chaves clássicas e as atribuições de função Azure Active Directory são independentes umas das outras. Atribuir uma entidade de segurança a uma função de ' colaborador ' em um nível de assinatura não permitirá automaticamente que a entidade de segurança possa executar operações de plano de dados em cada cofre de chaves dentro do escopo da assinatura. A entidade de segurança ainda deve ser concedida ou conceder permissões de política de acesso para executar operações de plano de dados.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Opção de acesso do plano de dados 2: RBAC do Azure para Key Vault (versão prévia)

Uma nova maneira de conceder acesso ao plano de dados do cofre de chaves é por meio do controle de acesso baseado em função do Azure (RBAC do Azure) para o Key Vault.

> [!NOTE]
> Este modelo de acesso não é compatível com as políticas de acesso clássico do Key Vault mostradas acima. Você deve escolher um. Você terá a oportunidade de fazer essa seleção ao clicar na guia política de acesso do cofre de chaves.

Key Vault atribuições de função são um conjunto de atribuições de função internas do Azure que abrangem conjuntos comuns de permissões usadas para acessar chaves, segredos e certificados. Esse modelo de permissão também habilita recursos adicionais que não estão disponíveis no modelo de política de acesso do cofre de chaves clássico.

* As permissões do RBAC do Azure podem ser gerenciadas em escala, permitindo que os usuários tenham essas funções atribuídas em uma assinatura, grupo de recursos ou nível de cofre de chaves individuais. Um usuário terá as permissões de plano de dados para todos os cofres de chaves dentro do escopo da atribuição de RBAC do Azure. Isso elimina a necessidade de atribuir permissões de política de acesso individuais por usuário/aplicativo por cofre de chaves.

* As permissões do RBAC do Azure são compatíveis com Privileged Identity Management ou PIM. Isso permite que você configure controles de acesso just-in-time para funções com privilégios como administrador de Key Vault. Essa é uma prática recomendada de segurança e segue a entidade de privilégio mínimo, eliminando o acesso à sua chave para seus cofres de chaves.

Para saber mais sobre o RBAC do Azure para Key Vault, consulte os seguintes documentos:

* [Link](./secure-your-key-vault.md#management-plane-and-azure-rbac) do Azure RBAC para Key Vault
* [Link](../../role-based-access-control/built-in-roles.md#key-vault-administrator) do Azure RBAC para funções de Key Vault

## <a name="configure-key-vault-firewall"></a>Configurar Key Vault firewall

Por padrão, o Key Vault permite que o tráfego da Internet pública envie o alcance do cofre de chaves por meio de um ponto de extremidade público. Para obter uma camada adicional de segurança, você pode configurar o firewall de Azure Key Vault para restringir o acesso ao ponto de extremidade público do cofre de chaves.

Para habilitar o Firewall do Key Vault, clique na guia rede no portal do cofre de chaves e selecione o botão de opção para permitir o acesso de: "ponto de extremidade privado e redes selecionadas". Se você optar por habilitar o Firewall do cofre de chaves, essas são as maneiras como você pode permitir o tráfego por meio do firewall do cofre de chaves.

* Adicione os endereços IPv4 à lista de permissões do firewall do cofre de chaves. Essa opção funciona melhor para aplicativos que têm endereços IP estáticos.

* Adicione uma rede virtual ao firewall do cofre de chaves. Essa opção funciona melhor para recursos do Azure que têm endereços IP dinâmicos, como máquinas virtuais. Você pode adicionar recursos do Azure a uma rede virtual e adicionar a rede virtual à lista de permissões do firewall do cofre de chaves. Essa opção usa um ponto de extremidade de serviço que é um endereço IP privado dentro da rede virtual. Isso fornece uma camada adicional de proteção, portanto, nenhum tráfego entre o Key Vault e sua rede virtual é roteado pela Internet pública. Para saber mais sobre o ponto de extremidade de serviço, consulte a documentação a seguir. [criar](./network-security.md)

* Adicione uma conexão de link privado ao cofre de chaves. Essa opção conecta sua rede virtual diretamente a uma instância específica do Key Vault, trazendo efetivamente seu cofre de chaves dentro de sua rede virtual. Para saber mais sobre como configurar uma conexão de ponto de extremidade privado com o Key Vault, consulte o [link](./private-link-service.md) a seguir

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testar a capacidade da sua entidade de serviço de acessar o cofre de chaves

Depois de ter seguido todas as etapas acima, você poderá definir e recuperar segredos do cofre de chaves.

### <a name="authentication-process-for-users-examples"></a>Processo de autenticação para usuários (exemplos)

* Os usuários podem fazer logon no portal do Azure para usar o Key Vault. [Início rápido do portal do Key Vault](./quick-create-portal.md)

* O usuário pode usar CLI do Azure para usar o Key Vault. [Guia de início rápido do Key Vault CLI do Azure](./quick-create-cli.md)

* O usuário pode usar Azure PowerShell para usar o Key Vault. [Guia de início rápido do Key Vault Azure PowerShell](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory processo de autenticação para aplicativos ou serviços (exemplos)

* Um aplicativo fornece um segredo do cliente e uma ID de cliente em uma função para obter um token de Azure Active Directory. 

* Um aplicativo fornece um certificado para obter um token de Azure Active Directory. 

* Um recurso do Azure usa a autenticação MSI para obter um token Azure Active Directory. 

* Saiba mais sobre o [link](../../active-directory/managed-identities-azure-resources/overview.md) de autenticação do MSI

### <a name="authentication-process-for-application-python-example"></a>Processo de autenticação para o aplicativo (exemplo de Python)

Use o exemplo de código a seguir para testar se seu aplicativo pode recuperar um segredo do seu Key Vault usando a entidade de serviço que você configurou.

>[!NOTE]
>Este exemplo destina-se apenas a fins de demonstração e teste. **não usar autenticação de segredo do cliente em produção** Essa não é uma prática de design segura. Você deve usar o certificado do cliente ou a autenticação do MSI como uma prática recomendada.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a autenticação do Key Vault mais detalhadamente, consulte o documento a seguir. [Autenticação do cofre de chaves](./authentication.md)
