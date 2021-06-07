---
title: Habilitar o write-back de senha do Azure Active Directory
description: Neste tutorial, você aprenderá a habilitar o write-back de redefinição de senha por autoatendimento do Azure AD usando o Azure AD Connect para sincronizar as alterações de volta para um ambiente local do Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97028046"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Tutorial: Habilitar o write-back da redefinição de senha por autoatendimento do Azure Active Directory para um ambiente local

Com a SSPR (redefinição de senha por autoatendimento) do Azure AD (Azure Active Directory), os usuários podem atualizar as respectivas senhas ou desbloquear as respectivas contas usando um navegador da Web. Em um ambiente híbrido em que o Azure AD está conectado a um ambiente local do AD DS (Active Directory Domain Services), esse cenário pode fazer com que as senhas sejam diferentes entre os dois diretórios.

O write-back de senha pode ser usado para sincronizar alterações de senha no Azure AD de volta para seu ambiente local do AD DS. O Azure AD Connect fornece um mecanismo seguro para enviar essas alterações de senha do Azure AD de volta para um diretório local existente.

> [!IMPORTANT]
> Este tutorial mostra como um administrador pode habilitar a redefinição de senha por autoatendimento novamente para um ambiente local. Se você for um usuário final já registrado para redefinição de senha por autoatendimento e precisar voltar à sua conta, vá para https://aka.ms/sspr.
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar as permissões necessárias para write-back de senha
> * Habilitar a opção de write-back de senha no Azure AD Connect
> * Habilitar write-back de senha no SSPR do Azure AD

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Um locatário do Azure AD em funcionamento com pelo menos uma licença de avaliação ou Premium P1 do Azure AD habilitada.
    * Se necessário, [crie um gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Para obter mais informações, confira [Requisitos de licenciamento para o SSPR do Azure AD](concept-sspr-licensing.md).
* Uma conta com privilégios de *administrador global*.
* Azure AD configurado para redefinição de senha por autoatendimento.
    * Se necessário, [conclua o tutorial anterior para habilitar o SSPR do Azure AD](tutorial-enable-sspr.md).
* Um ambiente local do AD DS existente configurado com uma versão atual do Azure AD Connect.
    * Se necessário, configure o Azure AD Connect usando as configurações [Expressas](../hybrid/how-to-connect-install-express.md) ou [Personalizadas](../hybrid/how-to-connect-install-custom.md).
    * Para usar o write-back de senha, os controladores de domínio precisam executar o Windows Server 2012 ou posterior.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Configurar permissões de conta para Azure AD Connect

O Azure AD Connect permite sincronizar usuários, grupos e credenciais entre um ambiente local do AD DS e o Azure AD. Normalmente, você instala o Azure AD Connect em um computador com Windows Server 2012 ou posterior que está ingressado no domínio local do AD DS.

Para trabalhar corretamente com o write-back do SSPR, a conta especificada no Azure AD Connect deve ter as permissões e as opções apropriadas definidas. Se você não tiver certeza de qual conta está em uso no momento, abra o Azure AD Connect e selecione a opção **Exibir a configuração atual**. A conta à qual você precisa adicionar as permissões está listada em **Diretórios Sincronizados**. As seguintes permissões e opções precisam estar definidas na conta:

* **Redefinir senha**
* **Permissões de gravação** em `lockoutTime`
* **Permissões de gravação** em `pwdLastSet`
* **Direitos estendidos** para "Não permitir expiração da senha" no objeto raiz de *cada domínio* nessa floresta, caso ainda não estejam definidos.

Se você não atribuir essas permissões, o write-back poderá parecer estar configurado corretamente, mas os usuários receberão erros ao tentar gerenciar as respectivas senhas locais na nuvem. As permissões precisam ser aplicadas para que **esse objeto e todos os objetos descendentes** de "Não Permitir Expiração de Senha" sejam exibidos.  

> [!TIP]
>
> Se as senhas de algumas contas de usuário não são gravadas de volta no diretório local, verifique se a herança não está desabilitada para essas contas no ambiente local do AD DS. As permissões de gravação para senhas devem ser aplicadas aos objetos descendentes para que o recurso funcione corretamente.

Para configurar as permissões apropriadas para que ocorra o write-back de senha, conclua as etapas a seguir:

1. No ambiente local do AD DS, abra **Usuários e Computadores do Active Directory** com uma conta que tem as permissões de *administrador de domínio* apropriadas.
1. No menu **Exibir**, verifique se os **Recursos avançados** estão ativados.
1. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e escolha **Propriedades** > **Segurança** > **Avançado**.
1. Na guia **Permissões**, selecione **Adicionar**.
1. Para **Entidade de segurança**, selecione a conta à qual as permissões devem ser aplicadas (a conta usada pelo Azure AD Connect).
1. Na lista suspensa **Aplica-se a**, selecione os **objetos de Usuário Descendente**.
1. Em *Permissões*, selecione a caixa para a seguinte opção:
    * **Redefinir senha**
1. Em *Permissões*, marque as caixas das opções a seguir. Role a lista para encontrar essas opções, que podem já estar definidas por padrão:
    * **Gravar lockoutTime**
    * **Gravar pwdLastSet**

    [ ![Defina as permissões apropriadas em Computadores e Usuários Ativos para a conta usada pelo Azure AD Connect](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png) ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Quando concluído, selecione **Aplicar/OK** para aplicar as alterações e sair das caixas de diálogo abertas.

Quando você atualizar as permissões, poderá levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos no diretório.

As políticas de senha no ambiente local do AD DS podem impedir que as redefinições de senha sejam processadas corretamente. Para que o write-back de senha funcione com o máximo de eficiência, a política de grupo para *Tempo de vida mínimo da senha* precisa ser definida como 0. Essa configuração pode ser encontrada em **Configuração do Computador > Políticas > Configurações do Windows > Configurações de Segurança > Políticas de Conta** em `gpedit.msc`.

Se você atualizar a política de grupo, aguarde a política atualizada ser replicada ou use o comando `gpupdate /force`.

> [!Note]
> Para que as senhas sejam alteradas imediatamente, o write-back de senha precisará ser definido como 0. No entanto, se os usuários obedecerem às políticas locais e o *Tempo de vida mínimo da senha* for definido com um valor maior que zero, o write-back de senha continuará funcionando depois que as políticas locais forem avaliadas.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Habilitar write-back de senha no Azure AD Connect

Uma das opções de configuração no Azure AD Connect é para o write-back de senha. Quando essa opção é habilitada, os eventos de alteração de senha fazem o Azure AD Connect sincronizar as credenciais atualizadas de volta para o ambiente local do AD DS.

Para habilitar o write-back de SSPR, primeiro habilite a opção de write-back no Azure AD Connect. Em seu servidor de Azure AD Connect, conclua as seguintes etapas:

1. Entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
1. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global para o locatário do Azure e selecione **Próximo**.
1. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
1. Na página **Recursos opcionais**, selecione a caixa ao lado de **Write-back de senha** e selecione **Próximo**.

    ![Configurar o Azure AD Connect para write-back de senha](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
1. Quando você vir a configuração terminar, selecione **Sair**.

## <a name="enable-password-writeback-for-sspr"></a>Habilitar o write-back de senha para o SSPR

Com o write-back de senha habilitado no Azure AD Connect, agora configure o SSPR do Azure AD para write-back. Quando você habilita o SSPR para usar o write-back de senha, a senha atualizada dos usuários que alteram ou redefinem as respectivas senhas é sincronizada novamente para o ambiente local do AD DS.

Para habilitar o write-back de senha no SSPR, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Procure e selecione **Azure Active Directory**, selecione **redefinição de senha** e, em seguida, escolha **Integração local**.
1. Defina a opção de **Fazer write-back de senhas em seu diretório local?** como *Sim*.
1. Defina a opção de **Permitir aos usuários desbloquear contas sem redefinir a senha?** como *Sim*.

    ![Habilitar a redefinição de senha por autoatendimento do Azure AD para write-back de senha](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Quando estiver pronto, selecione **Salvar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não deseja mais usar a funcionalidade de write-back do SSPR configurada como parte deste tutorial, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure e selecione **Azure Active Directory**, selecione **redefinição de senha** e, em seguida, escolha **Integração local**.
1. Defina a opção **Fazer write-back de senhas em seu diretório local?** como *Não*.
1. Defina a opção de **Permitir aos usuários desbloquear contas sem redefinir a senha?** como *Não*.

Se você não quiser mais usar nenhuma funcionalidade de senha, conclua as seguintes etapas do seu servidor do Azure AD Connect:

1. Entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
1. Na página de **Boas-vindas**, selecione **Configurar**.
1. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
1. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global para o locatário do Azure e selecione **Próximo**.
1. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
1. Na página **Recursos opcionais**, desmarque a caixa ao lado de **Write-back de senha** e selecione **Próximo**.
1. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
1. Quando você vir a configuração terminar, selecione **Sair**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou o write-back do SSPR do Azure AD para um ambiente local do AD DS. Você aprendeu a:

> [!div class="checklist"]
> * Configurar as permissões necessárias para write-back de senha
> * Habilitar a opção de write-back de senha no Azure AD Connect
> * Habilitar write-back de senha no SSPR do Azure AD

> [!div class="nextstepaction"]
> [Avaliar o risco ao entrar](tutorial-risk-based-sspr-mfa.md)
