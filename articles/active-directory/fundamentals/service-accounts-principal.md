---
title: Protegendo as entidades de serviço no Azure Active Directory
description: Encontre, avalie e proteja as entidades de serviço.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 297c0a53fb2ab4ee0b2c5291cabf5a63c8841664
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604695"
---
# <a name="securing-service-principals"></a>Proteção de entidades de serviço

Uma [entidade de serviço](../develop/app-objects-and-service-principals.md) do Azure Active Directory (AD do Azure) é a representação local de um objeto de aplicativo em um único locatário ou diretório.  Ele funciona como a identidade da instância do aplicativo. As entidades de serviço definem quem pode acessar o aplicativo e quais recursos o aplicativo pode acessar. Uma entidade de serviço é criada em cada locatário em que o aplicativo é usado e faz referência ao objeto de aplicativo exclusivo globalmente. O locatário protege a entrada da entidade de serviço e o acesso aos recursos.  

### <a name="tenant-service-principal-relationships"></a>Locatário-relações de entidade de serviço
Um aplicativo de locatário único tem apenas uma entidade de serviço em seu locatário inicial. Um aplicativo Web multilocatário ou uma API requer uma entidade de serviço em cada locatário. Uma entidade de serviço é criada quando um usuário desse locatário consentiu o uso do aplicativo ou da API. Esse consentimento cria uma relação um-para-muitos entre o aplicativo multilocatário e suas entidades de serviço associadas.

Um aplicativo multilocatário é hospedado em um único locatário e é projetado para ter instâncias em outros locatários. A maioria dos aplicativos SaaS (software como serviço) foi projetada para multilocação. Use entidades de serviço para garantir a postura de segurança certa para o aplicativo e seus usuários em casos de uso de locatário único e de vários locatários.

## <a name="applicationid-and-objectid"></a>ApplicationID e ObjectID

Uma determinada instância do aplicativo tem duas propriedades distintas: ApplicationID (também conhecido como ClientID) e ObjectID.

> [!NOTE] 
> Você pode achar que os termos aplicativo e entidade de serviço são usados de maneira intercambiável quando se referir livremente a um aplicativo no contexto de tarefas relacionadas à autenticação. No entanto, eles são duas representações diferentes de aplicativos no Azure AD.
 

O ApplicationID representa o aplicativo global e é o mesmo para todas as instâncias de aplicativo entre locatários. O ObjectID é um valor exclusivo para um objeto de aplicativo e representa a entidade de serviço. Assim como acontece com usuários, grupos e outros recursos, o ObjectID ajuda a identificar exclusivamente uma instância do aplicativo no Azure AD.

Para obter informações mais detalhadas sobre este tópico, consulte [relação entre aplicativo e entidade de serviço](../develop/app-objects-and-service-principals.md).

Você também pode criar um aplicativo e seu objeto de entidade de serviço (ObjectID) em um locatário usando Azure PowerShell, CLI do Azure, Microsoft Graph, a portal do Azure e outras ferramentas. 

![Captura de tela mostrando um novo registro de aplicativo, com os campos ID do aplicativo e ID do objeto realçados.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Há dois mecanismos para autenticação usando entidades de serviço — certificados de cliente e segredos de cliente. 

![ Captura de tela da página novo aplicativo mostrando as áreas certificados e segredos do cliente realçadas.](./media/securing-service-accounts/secure-principal-certificates.png)

Os certificados são mais seguros: Use certificados de cliente, se possível. Ao contrário dos segredos do cliente, os certificados de cliente não podem ser inseridos acidentalmente no código. Use Azure Key Vault para gerenciamento de certificado e segredos quando possível para criptografar os seguintes ativos usando chaves protegidas por módulos de segurança de hardware:

* chaves de autenticação

* chaves da conta de armazenamento

* chaves de criptografia de dados

* Arquivos .pfx

* senhas 

Para obter mais informações sobre Azure Key Vault e como usá-lo para gerenciamento de certificado e secreto, consulte [sobre Azure Key Vault](../../key-vault/general/overview.md) e [atribuir uma política de acesso de Key Vault usando o portal do Azure](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Desafios e mitigações
A tabela a seguir apresenta mitigações para desafios que você pode encontrar ao usar entidades de serviço.


| Desafios| Atenuações |
| - | - |
| Revisões de acesso para entidades de serviço atribuídas a funções privilegiadas.| Essa funcionalidade está em versão prévia e ainda não está amplamente disponível. |
| Revisa o acesso de entidades de serviço| Verificação manual da lista de controle de acesso do recurso usando o portal do Azure. |
| Sobre entidades de serviço com permissão| Quando você cria contas de serviço de automação ou entidades de serviço, forneça somente as permissões necessárias para a tarefa. Avalie as entidades de serviço existentes para ver se é possível reduzir os privilégios. |
|Identificar modificações nas credenciais ou métodos de autenticação das entidades de serviço |Use a pasta de trabalho de relatório de operações confidenciais, que pode ajudar a atenuar esse problema. [Consulte a explicação nesta postagem no blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Localizar contas usando entidades de serviço
Execute os comandos a seguir para localizar contas usando entidades de serviço.

Usando a CLI do Azure


`az ad sp list`

Usar o PowerShell

`Get-AzureADServicePrincipal -All:$true` 


Para obter mais informações [, consulte Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Avaliar a segurança da entidade de serviço

Para avaliar a segurança de suas entidades de serviço, certifique-se de avaliar os privilégios e o armazenamento de credenciais.

Reduza os desafios potenciais usando as informações a seguir.

|Desafios | Atenuações|
| - | - |
| Detectar o usuário que consentiu em um aplicativo multilocatário e detectar concessões de autorização ilícita para um aplicativo multilocatário | Execute o PowerShell a seguir para localizar aplicativos multilocatários.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Desabilite o consentimento do usuário. <br>Permitir consentimento do usuário de Publicadores verificados, para permissões selecionadas (recomendado) <br> Use o acesso condicional para bloquear entidades de serviço de locais não confiáveis. Configure-os no contexto do usuário e seus tokens devem ser usados para disparar a entidade de serviço.|
|Uso de um segredo compartilhado embutido em código em um script usando uma entidade de serviço.|Use um certificado ou Azure Key Vault.|
|Acompanhando quem está usando o certificado ou o segredo| Monitore as entradas da entidade de serviço usando os logs de entrada do Azure AD.|
Não é possível gerenciar a entrada das entidades de serviço com acesso condicional.| Monitorar as entradas usando os logs de entrada do Azure AD
| A função do RBAC do Azure padrão é colaborador. |Avalie as necessidades e aplique a função com o mínimo possível de permissões para atender a essa necessidade.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Mover de uma conta de usuário para uma entidade de serviço  
Se você estiver usando uma conta de usuário do Azure como uma entidade de serviço, avalie se você pode mudar para uma [identidade gerenciada](../../app-service/overview-managed-identity.md?tabs=dotnet) ou uma entidade de serviço. Se você não puder usar uma identidade gerenciada, Provisione uma entidade de serviço que tenha apenas permissões e escopo suficientes para executar as tarefas necessárias. Você pode criar uma entidade de serviço [registrando um aplicativo](../develop/howto-create-service-principal-portal.md)ou com o [PowerShell](../develop/howto-authenticate-service-principal-powershell.md).

Ao usar Microsoft Graph, verifique a documentação da API específica, [como neste exemplo](/powershell/azure/create-azure-service-principal-azureps), e verifique se o tipo de permissão para o aplicativo está sendo exibido como com suporte.

## <a name="next-steps"></a>Próximas etapas

**Para saber mais sobre as entidades de serviço:**

[Criar uma entidade de serviço](../develop/howto-create-service-principal-portal.md)

 [Monitorar as entradas da entidade de serviço](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**Para saber mais sobre como proteger as contas de serviço:**

[Introdução às contas de serviço do Azure](service-accounts-introduction-azure.md)

[Proteção de identidades gerenciadas](service-accounts-managed-identities.md)

[Administração de contas de serviço do Azure](service-accounts-governing-azure.md)

[Introdução a contas de serviço locais](service-accounts-on-premises.md)
