---
title: Criar um locatário na Versão prévia da Área de Trabalho Virtual do Windows – Azure
description: Descreve como configurar locatários da Versão Prévia da Área de Trabalho Virtual do Windows no Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 3d418d9f18c98e1b6fdf39924ab41dae77fba291
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204747"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Tutorial: Criar um locatário na Versão Prévia da Área de Trabalho Virtual do Windows

A criação de um locatário na Versão prévia da Área de Trabalho Virtual do Windows é a primeira etapa para a criação de sua solução de virtualização de área de trabalho. Um locatário é um grupo de um ou mais pools de host. Cada pool de hosts consiste em vários hosts de sessão, em execução como máquinas virtuais no Azure e registrados no serviço da Área de Trabalho Virtual do Windows. Cada pool de hosts também consiste em um ou mais grupos de aplicativos que são usados para publicar recursos de aplicativo remoto e área de trabalho remota para usuários. Com um locatário, é possível criar pools de hosts, criar grupos de aplicativos, atribuir usuários e fazer conexões por meio do serviço.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Conceda permissões do Azure Active Directory ao serviço da Área de Trabalho Virtual do Windows.
> * Atribua a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory.
> * Crie um locatário da Área de Trabalho Virtual do Windows.

Isto é o que você precisa para configurar seu locatário da Área de Trabalho Virtual do Windows:

* A ID de locatário do [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para os usuários da Área de Trabalho Virtual do Windows.
* Uma conta de administrador global no locatário do Azure Active Directory.
   * Isso também se aplica às organizações CSP (Provedor de Soluções na Nuvem) que estão criando um locatário da Área de Trabalho Virtual do Windows para seus clientes. Se você é uma organização CSP, deve conseguir entrar como administrador global da instância do Azure Active Directory do cliente.
   * A conta de administrador deve se originar de locatário do Azure Active Directory no qual você está tentando criar o locatário de Área de Trabalho Virtual do Windows. Esse processo não dá suporte a contas do Azure Active Directory B2B (convidado).
   * A conta de administrador deve ser uma conta corporativa ou de estudante.
* Uma assinatura do Azure.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Conceder permissões do Azure Active Directory ao serviço da Versão Prévia da Área de Trabalho Virtual do Windows

Se você já concedeu permissões à Área de Trabalho Virtual do Windows para essa instância do Azure Active Directory, ignore esta seção.

A concessão de permissões ao serviço da Área de Trabalho Virtual do Windows permite que ele confira tarefas administrativas e do usuário final no Azure Active Directory.

Para conceder as permissões de serviço:

1. Abra um navegador e conecte-se à [página de consentimento da Área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com).
2. Para **Opção de Consentimento** > **Aplicativo de Servidor**, insira o nome do locatário ou a ID de diretório do Azure Active Directory e, em seguida, selecione **Enviar**.
        
   Para clientes do Provedor de Soluções na Nuvem, a ID é a ID da Microsoft do cliente no Portal do Partner. Para clientes do Enterprise, a ID está localizada em **Azure Active Directory** > **Propriedades** > **ID de Diretório**.
3. Entre na página de consentimento da Área de Trabalho Virtual do Windows com uma conta de administrador global. Por exemplo, se você fosse entrar com a organização Contoso, sua conta poderia ser admin@contoso.com ou admin@contoso.onmicrosoft.com.  
4. Selecione **Aceitar**.
5. Aguarde um minuto.
6. Vá novamente para a [página de consentimento da Área de Trabalho Virtual do Windows](https://rdweb.wvd.microsoft.com).
7. Acesse **Opção de Consentimento** > **Aplicativo Cliente**, insira o mesmo nome de locatário ou ID de diretório do Azure Active Directory e, em seguida, selecione **Enviar**.
8. Entre na página de consentimento da Área de Trabalho Virtual do Windows como administrador global, como fez na etapa 3.
9. Selecione **Aceitar**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Atribuir a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory

A atribuição de um usuário do Azure Active Directory à função de aplicativo TenantCreator permite que o usuário crie um locatário da Área de Trabalho Virtual do Windows associado à instância do Azure Active Directory. Você precisará usar sua conta de administrador global para atribuir a função TenantCreator.

Para atribuir a função de aplicativo TenantCreator:

1. Abra um navegador e conecte-se ao [portal do Azure](https://portal.azure.com) com sua conta de administrador global.
   
   Se você está trabalhando com vários locatários do Azure Active Directory, é uma prática recomendada abrir uma sessão privada do navegador e copiar e colar as URLs na barra de endereços.
2. Na barra de pesquisa do portal do Azure, pesquise **Aplicativos Empresariais** e selecione a entrada que aparece na categoria **Serviços**.
3. Em **Aplicativos Empresariais**, pesquise **Área de Trabalho Virtual do Windows**. Você verá os dois aplicativos para os quais forneceu consentimento na seção anterior. Desses dois aplicativos, selecione **Área de Trabalho Virtual do Windows**.
   ![Uma captura de tela dos resultados da pesquisa ao pesquisar "Área de Trabalho Virtual do Windows" em "Aplicativos empresariais". O aplicativo chamado "Área de Trabalho Virtual do Windows" está realçado.](media/tenant-enterprise-app.png)
4. Selecione **Usuários e grupos**. Você pode ver que o administrador que deu consentimento ao aplicativo já está listado com a função **Acesso Padrão** atribuída. Isso não é suficiente para criar um locatário da Área de Trabalho Virtual do Windows. Continue a seguir estas instruções para adicionar a função **TenantCreator** a um usuário.
   ![Captura de tela dos usuários e grupos designados para gerenciar o aplicativo corporativo "Área de Trabalho Virtual do Windows". A captura de tela mostra apenas uma atribuição, que é de "Acesso Padrão".](media/tenant-default-access.png)
5. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na folha **Adicionar Atribuição**.
6. Pesquise uma conta de usuário que criará o locatário da Área de Trabalho Virtual do Windows. Para simplificar, isso pode ser a conta de administrador global.

   ![Captura de tela da seleção de um usuário para adicionar como "TenantCreator".](media/tenant-assign-user.png)

   > [!NOTE]
   > É preciso selecionar um usuário (ou um grupo que contém um usuário) originário desta instância do Azure Active Directory. Não é possível escolher um usuário convidado (B2B) ou uma entidade de serviço.

7. Selecione a conta de usuário, escolha o botão **Selecionar** e, em seguida, selecione **Atribuir**.
8. Na página **Área de Trabalho Virtual do Windows – Usuários e Grupos**, verifique se há uma nova entrada com a função **TenantCreator** atribuída ao usuário que criará o locatário da Área de Trabalho Virtual do Windows.
   ![Captura de tela dos usuários e grupos designados para gerenciar o aplicativo corporativo "Área de Trabalho Virtual do Windows". A captura de tela agora inclui uma segunda entrada de um usuário atribuído à função "TenantCreator".](media/tenant-tenant-creator-added.png)

Antes de continuar a criar seu locatário da Área de Trabalho Virtual do Windows, você precisará de duas informações:
- ID de locatário do Azure Active Directory (ou **ID de diretório**)
- ID da assinatura do Azure

Para localizar sua ID de locatário do Azure Active Directory (ou **ID de diretório**):
1. Na mesma sessão do portal do Azure, pesquise **Azure Active Directory** na barra de pesquisa e selecione a entrada que aparece na categoria **Serviços**.
   ![Captura de tela dos resultados da pesquisa para "Azure Active Directory" no portal do Azure. O resultado da pesquisa em "Serviços" está destacado.](media/tenant-search-azure-active-directory.png)
2. Role para baixo até encontrar **Propriedades** e, em seguida, selecione essa opção.
3. Procure a **ID de diretório** e selecione o ícone da área de transferência. Cole-o em um local prático para poder usá-lo posteriormente como o valor **AadTenantId**.
   ![Captura de tela das propriedades do Azure Active Directory. O mouse passando sobre o ícone da área de transferência para o "ID de diretório" para copiar e colar.](media/tenant-directory-id.png)

Para localizar sua ID da assinatura do Azure:
1. Na mesma sessão do portal do Azure, pesquise **Inscrições** na barra de pesquisa e selecione a entrada que aparece na categoria **Serviços**.
   ![Captura de tela dos resultados da pesquisa para "Azure Active Directory" no portal do Azure. O resultado da pesquisa em "Serviços" está destacado.](media/tenant-search-subscription.png)
2. Selecione a assinatura do Azure que você quer usar para receber as notificações de serviço da Área de Trabalho Virtual do Windows.
3. Procure **ID da assinatura** e passe o mouse sobre o valor até que um ícone da área de transferência seja exibido. Selecione o ícone da área de transferência e cole-o em um local prático para poder usá-lo posteriormente como o valor **AzureSubscriptionId**.
   ![Captura de tela das propriedades da assinatura do Azure. O mouse passando sobre o ícone da área de transferência para o "ID da assinatura" para copiar e colar.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Criar um locatário da Versão Prévia da Área de Trabalho Virtual do Windows

Agora que você recebeu as permissões de serviço da Área de Trabalho Virtual do Windows para consultar o Azure Active Directory e atribuiu a função TenantCreator a uma conta de usuário, crie um locatário da Área de Trabalho Virtual do Windows.

Primeiro, [baixe e importe o módulo da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Entre na Área de Trabalho Virtual do Windows usando a conta de usuário TenantCreator com este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, crie um locatário da Área de Trabalho Virtual do Windows associado ao locatário do Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Substitua os valores entre colchetes pelos valores relevantes à sua organização e ao locatário. O nome escolhido para o novo locatário da Área de Trabalho Virtual do Windows deve ser globalmente exclusivo. Por exemplo, digamos que você seja o TenantCreator da Área de Trabalho Virtual do Windows para a organização Contoso. O cmdlet que você executaria seria semelhante a este:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar seu locatário, você precisará criar uma entidade de serviço no Azure Active Directory e atribuir a ela uma função dentro da Área de Trabalho Virtual do Windows. A entidade de serviço permitirá que você implante com êxito a oferta do Azure Marketplace da Área de Trabalho Virtual do Windows para criar um pool de host. Para saber mais sobre os pools de host, continue com o tutorial para criar um pool de hosts na Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Criar entidades de serviço e atribuições de função com o PowerShell](./create-service-principal-role-powershell.md)
