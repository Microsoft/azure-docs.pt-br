---
title: Tutorial para configurar o Saviynt com o Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial para configurar Azure Active Directory B2C com o Saviynt para integração entre aplicativos para simplificar a modernização de ti e promover melhor segurança, governança e conformidade. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 99741a412ae02a2d30293e1aa254245f5ca7b662
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171425"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Tutorial para configurar o Saviynt com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure Active Directory (AD) B2C com o [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/). A plataforma do Gerenciador de segurança do Saviynt fornece visibilidade, segurança e governança de que as empresas de hoje precisam, em uma única plataforma unificada. O Saviynt incorpora o risco e a governança do aplicativo, gerenciamento de infraestrutura, gerenciamento de conta com privilégios e análise de risco do cliente.

Neste tutorial de exemplo, você vai configurar o Saviynt para fornecer a administração delegada baseada no controle de acesso refinado para Azure AD B2C usuários. O Saviynt faz as verificações a seguir para determinar se um usuário está autorizado a gerenciar Azure AD B2C usuários.

- Segurança em nível de recurso para determinar se um usuário pode executar uma operação específica. Por exemplo, criar usuário, atualizar usuário, Redefinir senha de usuário e assim por diante.

- Segurança em nível de campo para determinar se um usuário pode ler/gravar um atributo específico de outro usuário durante as operações de gerenciamento de usuário. Por exemplo, o agente de suporte técnico só pode atualizar o número de telefone e todos os outros atributos são somente leitura.

- Segurança de nível de dados para determinar se um usuário pode executar uma determinada operação em um usuário específico. Por exemplo, o administrador do suporte técnico para região do Reino Unido pode gerenciar somente os usuários do Reino Unido.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md). O locatário está vinculado à sua assinatura do Azure.

- Uma [assinatura](https://saviynt.com/contact-us/) do Saviynt

## <a name="scenario-description"></a>Descrição do cenário

A integração do Saviynt inclui os seguintes componentes:

- [Azure ad B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – a identidade da empresa para o cliente como um serviço que permite o controle personalizado de como seus clientes se inscrevem, entram e gerenciam seus perfis.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – a plataforma de governança de identidade que fornece administração delegada refinada para gerenciamento do ciclo de vida do usuário e governança de acesso de Azure ad B2C usuários.  

- [API de Microsoft Graph](/graph/use-the-api) – essa API fornece as interfaces para Saviynt para gerenciar os Azure ad B2C usuários e seu acesso no Azure ad B2C.

O diagrama de arquitetura a seguir mostra a implementação.

![Imagem mostrando o diagrama de arquitetura saviynt](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | Um administrador delegado inicia uma operação gerenciar Azure AD B2C usuário por meio do Saviynt.
| 2. | Saviynt verifica seu mecanismo de autorização se o administrador delegado puder realizar a operação específica.
| 3. | O mecanismo de autorização de Saviynt envia uma resposta de êxito/falha de autorização.
| 4. | Saviynt permite que o administrador delegado faça a operação necessária.
| 5. | Saviynt invoca a API Microsoft Graph junto com os atributos de usuário para gerenciar o usuário no Azure AD B2C
| 6. | Microsoft Graph API, por sua vez, criará/atualizará/excluirá o usuário no Azure AD B2C.
| 7. | Azure AD B2C enviará uma resposta de êxito/falha.
| 8. | Microsoft Graph API retornará a resposta para Saviynt.

## <a name="onboard-with-saviynt"></a>Integração com o Saviynt

1. Para criar uma conta do Saviynt, entre em contato com [Saviynt](https://saviynt.com/contact-us/)

2. Crie políticas de administração delegadas e atribua usuários como [administradores delegados](../active-directory/roles/concept-delegation.md) com várias funções.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Configurar Azure AD B2C com Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Criar um aplicativo do Azure AD para Saviynt

1. Entre no [portal do Azure](https://portal.azure.com/#home).

2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

3. No portal do Azure, pesquise e selecione **Azure ad B2C**.

4. Selecione **registros de aplicativo**  >  **novo registro**.

5. Insira um nome para o aplicativo. Por exemplo, Saviynt e selecione **criar**.

6. Acesse **permissões de API** e selecione **+ Adicionar uma permissão.**

7. A página Solicitar permissões de API é exibida. Selecione a guia **APIs da Microsoft** e selecione **Microsoft Graph** como APIs da Microsoft usadas com frequência.

8. Vá para a próxima página e selecione **permissões de aplicativo**.

9. Selecione **diretório** e selecione **diretório. ler. todas as** caixas de seleção Directory **. ReadWrite. All** .

10. Selecione **adicionar permissões**. Revise as permissões adicionadas.

11. Selecione **conceder consentimento do administrador para salvar o diretório padrão**  >  **Save**.

12. Vá para **certificados e segredos** e selecione **+ Adicionar segredo do cliente**. Insira a descrição do segredo do cliente, selecione a opção expiração e selecione **Adicionar**.

13. A chave secreta é gerada e exibida na seção segredo do cliente.

    >[!NOTE]
    > Você precisará do segredo do cliente mais tarde.

14. Acesse **visão geral** e obtenha a **ID do cliente** e a ID do **locatário**.

15. A ID do locatário, a ID do cliente e o segredo do cliente serão necessários para concluir a instalação no Saviynt.

### <a name="enable-saviynt-to-delete-users"></a>Habilitar Saviynt para excluir usuários

As etapas a seguir explicam como habilitar o Saviynt para executar operações de exclusão de usuário no Azure AD B2C.

>[!NOTE]
>[Avalie o risco antes de conceder acesso às funções de administrador a uma entidade de serviço.](../active-directory/develop/app-objects-and-service-principals.md)

1. Instale a versão mais recente do módulo MSOnline do PowerShell em uma estação de trabalho do Windows/servidor.

2. Conecte-se ao módulo do PowerShell do AzureAD e execute os seguintes comandos:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testar a solução

Navegue até o locatário do aplicativo Saviynt e teste o gerenciamento do ciclo de vida do usuário e o caso de uso de governança de acesso.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)

- [Criar um aplicativo de API Web](./add-web-api-application.md)