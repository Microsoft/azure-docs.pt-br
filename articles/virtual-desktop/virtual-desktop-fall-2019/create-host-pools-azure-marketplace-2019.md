---
title: Pool de host da Área de Trabalho Virtual do Windows (clássica) no Azure Marketplace – Azure
description: Como criar um pool de host da Área de Trabalho Virtual do Windows (clássica) usando o Azure Marketplace.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa271ba87f68d46452633d2b426c7fd805f8566b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445152"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Tutorial: Criar um pool de host na Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../create-host-pools-azure-marketplace.md).

Neste tutorial, você aprenderá como criar um pool de hosts em um locatário de Área de Trabalho Virtual do Windows usando uma oferta do Microsoft Azure Marketplace.

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

As tarefas neste tutorial incluem:

> [!div class="checklist"]
>
> * Criar um pool de hosts na Área de Trabalho Virtual do Windows.
> * Criar um grupo de recursos com VMs em uma assinatura do Azure.
> * Associar as VMs ao domínio do Active Directory.
> * Registrar as VMs com a Área de Trabalho Virtual do Windows.

## <a name="prerequisites"></a>Pré-requisitos

* Um locatário na área de trabalho virtual. Um [tutorial](tenant-setup-azure-active-directory.md) anterior cria um locatário.
* [Módulo do PowerShell da Área de Trabalho Virtual do Windows](/powershell/windows-virtual-desktop/overview/).

Quando você tiver esse módulo, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Execute a oferta do Azure Marketplace para provisionar um novo pool de hosts

Para executar a oferta do Azure Marketplace para provisionar um novo pool de hosts:

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.
1. Insira **Área de Trabalho Virtual do Windows** na janela de pesquisa do Marketplace.
1. Selecione **Área de Trabalho Virtual do Windows – Provisionar um pool de hosts** e selecione **Criar**.

Depois disso, siga as instruções na próxima seção para inserir as informações para as guias apropriadas.

### <a name="basics"></a>Noções básicas

Veja o que você faz para a guia **Noções básicas**:

1. Selecione uma **Assinatura**.
1. Para o **Grupo de recursos**, selecione **Criar** e forneça um nome para o novo grupo de recursos.
1. Selecione uma **Região**.
1. Insira um nome para o pool de hosts que seja exclusivo dentro do locatário da Área de Trabalho Virtual do Windows.
1. Selecione **Tipo de área de trabalho**. Se você selecionar **Pessoal**, cada usuário que se conectar a esse pool de hosts será atribuído permanentemente a uma máquina virtual.
1. Insira os usuários que podem entrar nos clientes de Área de Trabalho Virtual do Windows e acessar uma área de trabalho. Usar uma lista separada por vírgulas. Por exemplo, se você quiser atribuir acesso a `user1@contoso.com` e `user2@contoso.com`, insira *`user1@contoso.com,user2@contoso.com`*
1. Para **Local dos metadados de serviço**, selecione o mesmo local da rede virtual que tenha conectividade com o servidor do Active Directory.

   >[!IMPORTANT]
   >Se você estiver usando o Azure AD DS (Azure Active Directory Domain Services) e a solução do Azure AD (Azure Active Directory), não se esqueça de implantar o pool de hosts na mesma região que o Azure AD DS para evitar erros de associação de domínio e de credenciais.

1. Selecione **Avançar: Configurar máquinas virtuais**.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para a guia **Configurar máquinas virtuais**:

1. Aceite os padrões ou personalize o número e tamanho das máquinas virtuais.

    >[!NOTE]
    >Se o tamanho específico da máquina virtual que você procura não aparece no seletor de tamanho, é porque ainda não o integramos à ferramenta Azure Marketplace.

2. Digite um prefixo para os nomes das máquinas virtuais. Por exemplo, se você inserir o nome *prefixo*, as máquinas virtuais serão chamadas de **prefixo-0,** **prefixo-1** e assim por diante.
3. Selecione **Avançar: Configurações da máquina virtual**.

### <a name="virtual-machine-settings"></a>Configurações da máquina virtual

Para a guia **Configurações de máquina virtual**:

1. Para **Origem da imagem**, selecione a origem e insira as informações apropriadas para saber como encontrá-las e como armazená-las. As opções são diferentes para o Armazenamento de blobs, a Imagem gerenciada e a Galeria.

   Se você optar por não usar discos gerenciados, selecione a conta de armazenamento que contém o arquivo *.vhd*.
1. Insira o nome UPN e a senha. Essa conta deve ser a conta de domínio que adicionará as máquinas virtuais ao domínio do Active Directory. Esse mesmo nome de usuário e senha será criado nas máquinas virtuais como uma conta local. É possível redefinir essas contas locais mais tarde.

   >[!NOTE]
   > Se você estiver unindo suas máquinas virtuais em um ambiente do Azure AD DS, verifique se o usuário de ingresso no domínio é um membro do [grupo de Administradores do AAD DC](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > A conta também precisa fazer parte do domínio gerenciado do Azure AD DS ou do locatário do Azure AD. Contas de diretórios externos associadas ao seu locatário do Azure AD não podem ser autenticadas corretamente durante o processo de ingresso no domínio.

1. Selecione a **Rede virtual** que tem conectividade com o servidor do Active Directory e escolha uma sub-rede para hospedar as máquinas virtuais.
1. Selecione **Avançar: Informações da Área de Trabalho Virtual do Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações de locatário da Área de Trabalho Virtual do Windows

Para a guia **Informações de locatário da Área de Trabalho Virtual do Windows**:

1. Para **Nome do grupo de locatário da Área de Trabalho Virtual do Windows**, digite o nome do grupo de locatário que contém seu locatário. Deixe-o como o padrão, a menos que você tenha recebido um nome de grupo de locatário específico.
1. Para **Nome de locatário da Área de Trabalho Virtual do Windows**, digite o nome do locatário no qual você criará esse pool de hosts.
1. Especifique o tipo de credenciais que você deseja usar para autenticar como o proprietário RDS do locatário da Área de Trabalho Virtual do Windows. Insira o UPN ou a entidade de serviço e uma senha.

   Se você concluiu o [Criar entidades de serviço e atribuições de função com o PowerShell](create-service-principal-role-powershell.md), selecione **Entidade de Serviço**.

1. Para **Entidade de serviço**, para **ID do locatário do Azure AD**, insira a conta do administrador de locatários para a instância do Azure AD que contém a entidade de serviço. Há suporte apenas para entidades de serviço com uma credencial de senha.
1. Selecione **Avançar: Revisar + criar**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Conclua a instalação e crie a máquina virtual

Em **Examinar e Criar**, examine as informações de instalação. Se você precisar alterar algo, volte e faça as alterações. Quando você estiver pronto, selecione **Criar** para implantar seu pool de hosts.

Dependendo de quantas máquinas virtuais você estiver criando, esse processo poderá levar 30 minutos ou mais para ser concluído.

>[!IMPORTANT]
> Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas máquinas virtuais. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as máquinas virtuais do pool de hosts.
>
> Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do acesso just-in-time. Para obter mais informações, confira [Proteger suas portas de gerenciamento com acesso just-in-time](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir usuários adicionais ao grupo de aplicativos da área de trabalho

Depois que o Azure Marketplace terminar de criar o pool, você poderá atribuir mais usuários ao grupo de aplicativos da área de trabalho. Se você não quiser adicionar mais usuários, ignore esta seção.

Para atribuir usuários adicionais ao grupo de aplicativos da área de trabalho:

1. Abra uma janela do PowerShell.

1. Execute o seguinte comando para entrar no ambiente da Área de Trabalho Virtual do Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Adicione usuários ao grupo de aplicativos da área de trabalho usando este comando:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   O UPN do usuário deve corresponder à identidade de usuário no Azure AD, por exemplo, *user1@contoso.com* . Se você desejar adicionar vários usuários, execute este comando para cada um.

Os usuários que você adicionar ao grupo de aplicativo de área de trabalho poderão entrar na Área de Trabalho Virtual do Windows com os clientes de Área de Trabalho Remota compatíveis e verão um recurso para uma área de trabalho de sessão.

Veja os clientes com suporte atuais:

* [Cliente da Área de Trabalho Remota para Windows 7 e Windows 10](connect-windows-7-10-2019.md)
* [Cliente Web da Área de Trabalho Virtual do Windows](connect-web-2019.md)

## <a name="next-steps"></a>Próximas etapas

Você criou um pool de hosts e atribuiu usuários para acessar a área de trabalho dele. Você pode popular o pool de hosts com programas do RemoteApp. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira este tutorial:

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](manage-app-groups-2019.md)
