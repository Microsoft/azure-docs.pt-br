---
title: Instalar o agente de provisionamento do Azure AD Connect
description: Saiba como instalar o agente de provisionamento do Azure AD Connect e como configurá-lo no portal do Azure.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 742b9fc79489feba8192b6e62a6431bb37f55ad4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613022"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalar o agente de provisionamento do Azure AD Connect
Este documento mostra o processo de instalação do agente de provisionamento do Azure AD (Azure Active Directory) Connect e como configurá-lo inicialmente no portal do Azure.

>[!IMPORTANT]
>As seguintes instruções de instalação assumem que todos os [Pré-requisitos](how-to-prerequisites.md) foram atendidos.

A instalação e a configuração do Azure AD Connect a sincronização de nuvem é realizada nas seguintes etapas:

- [Contas de serviço gerenciado de grupo](#group-managed-service-accounts) 
- [Instalar o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Uma conta de serviço gerenciado de grupo é uma conta de domínio gerenciado que fornece gerenciamento automático de senhas, gerenciamento de SPN (nome da entidade de serviço) simplificado, a capacidade de delegar o gerenciamento a outros administradores e também estende essa funcionalidade em vários servidores.  Azure AD Connect a sincronização de nuvem dá suporte e recomenda o uso de uma conta de serviço gerenciado de grupo para executar o agente.  Para obter mais informações sobre um gMSA, consulte [contas de serviço gerenciado de grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Atualizando um agente existente para usar a conta gMSA
Para atualizar um agente existente para usar a conta do gMSA criada durante a instalação, basta atualizar o serviço do Agent para a versão mais recente executando o AADConnectProvisioningAgent.msi.  Isso atualizará o serviço para a versão mais recente.  Agora, execute o assistente de instalação novamente e forneça as credenciais para criar a conta quando solicitado.



## <a name="install-the-agent"></a>Instalar o agente
Para instalar o agente, siga estas etapas.

 1. Entre no servidor que você usará com as permissões de administrador corporativo.
 2. Entre no portal do Azure e, em seguida, vá para **Azure Active Directory**.
 3. No menu à esquerda, selecione **Azure ad Connect**.
 4. Selecione **gerenciar sincronização de nuvem**  >  **examinar todos os agentes**.
 5. Baixe o agente de provisionamento do Azure AD Connect no portal do Azure.
   ![Baixe o agente local](media/how-to-install/install-9.png)</br>
 6. Aceite os termos e clique em baixar.
 7. Execute o AADConnectProvisioningAgentSetup.msi do instalador de provisionamento de Azure AD Connect.
 8. Na tela **Pacote do agente de provisionamento do Microsoft Azure AD Connect**, aceite os termos de licença e selecione **Instalar**.
   ![Tela Pacote do agente de provisionamento do Microsoft Azure AD Connect](media/how-to-install/install-1.png)</br>
 9. Após a conclusão desta operação, o assistente de configuração é iniciado. Entre com sua conta de administrador global do Azure AD.
 10. Na **tela configurar conta de serviço** , selecione **criar gMSA** ou **usar o gMSA personalizado**.  Se você permitir que o agente crie a conta, ele será nomeado provAgentgMSA $. Se você especificar **usar gMSA personalizado** , será solicitado a fornecer essa conta.
 11. Insira as credenciais de administrador de domínio para criar a conta de serviço gerenciado de grupo que será usada para executar o serviço do Agent. Clique em **Próximo**.  
   ![Criar gMSA](media/how-to-install/install-12.png)</br>
 12. Na tela **Active Directory Connect**, selecione **Adicionar diretório**. Em seguida, entre com sua conta de administrador do Active Directory. Esta operação adiciona seu diretório local. 
 13. Opcionalmente, você pode gerenciar a preferência de controladores de domínio que o agente usará selecionando **selecionar prioridade do controlador de domínio** e ordenando a lista de controladores de domínio.   Clique em **OK**.
  ![Ordenar controladores de domínio](media/how-to-install/install-2a.png)</br>
 14. Selecione **Avançar**.
  ![Tela Active Directory Connect](media/how-to-install/install-3a.png)</br>
 15.  Na tela de **instalação do agente** , confirme as configurações e a conta que será criada e clique em **confirmar**.
  ![Confirmar configurações](media/how-to-install/install-11.png)</br>
 16. Após a conclusão dessa operação, você verá que **a instalação do agente está concluída.** Selecione **Sair**.
  ![Tela Configuração concluída](media/how-to-install/install-4a.png)</br>
 17. Se você ainda vir a tela inicial **Pacote do agente de provisionamento do Microsoft Azure AD Connect**, selecione **Fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas:

 1. Entre no portal do Azure.
 2. À esquerda, selecione **Azure Active Directory** > **Azure AD Connect**. No centro, selecione **gerenciar sincronização de nuvem**.

   ![Portal do Azure](media/how-to-install/install-6.png)</br>

 3.  Na tela **Azure ad Connect a sincronização de nuvem** , selecione **examinar todos os agentes**.

   ![Opção Examinar todos os agentes](media/how-to-install/install-7.png)</br>
 
 4. Na tela **Agentes de provisionamento local**, você vê os agentes que instalou. Verifique se o agente em questão está lá e se está marcado como *ativo*.

   ![Tela Agentes de provisionamento local](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas.

1.  Entre no servidor com uma conta de administrador.
1.  Abra **Serviços** navegando até ele ou acessando **Iniciar** > **Executar** > **Services.msc**.
1.  Em **Serviços** certifique-se de que o **Atualizador do Agente do Microsoft Azure AD Connect** e o **Agente de Provisionamento do Microsoft Azure AD Connect** estão lá e se o status é *Em execução*.

    ![Tela de serviços](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e habilitado antes de iniciar a sincronização de usuários. Para configurar um novo agente, consulte [criar uma nova configuração para Azure ad Connect sincronização de nuvem](how-to-configure.md).




## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)
- [Crie uma nova configuração para Azure ad Connect sincronização de nuvem](how-to-configure.md).

