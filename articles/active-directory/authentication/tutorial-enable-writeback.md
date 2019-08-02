---
title: Habilitar o write-back de senha do Azure AD
description: Neste tutorial, você habilitará o write-back de senha para obter as alterações de senhas iniciadas na nuvem para o AD local como parte do Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1efb67df6c31a3b03fdc45fffc0564fb09e39faf
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853033"
---
# <a name="tutorial-enabling-password-writeback"></a>Tutorial: Habilitando o write-back de senha

Neste tutorial, você habilitará o write-back de senha para seu ambiente híbrido. O write-back de senha é usado para sincronizar as alterações de senhas no Azure Active Directory (Azure AD) para seu ambiente local de Serviços de Domínio do Active Directory (AD DS). O write-back de senha está habilitado como parte do Azure AD Connect para fornecer um mecanismo seguro para enviar as alterações de senhas para um diretório local existente do Azure AD. Você pode encontrar mais detalhes sobre os funcionamentos internos de write-back de senha no artigo [O que é o write-back de senha](concept-sspr-writeback.md).

> [!div class="checklist"]
> * Habilitar a opção de write-back de senha no Azure AD Connect
> * Habilitar a opção de write-back de senha na redefinição de senha de autoatendimento (SSPR)

## <a name="prerequisites"></a>Pré-requisitos

* Acesso a um locatário de trabalho do Azure AD com pelo menos uma licença de avaliação atribuída.
* Uma conta com privilégios de Administrador Global em seu locatário do Azure AD.
* Um servidor existente configurado executando uma versão atual do [Azure AD Connect](../hybrid/how-to-connect-install-express.md).
* Os tutoriais de redefinição de senha de autoatendimento (SSPR) anteriores foram concluídos.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Habilitar a opção de write-back de senha no Azure AD Connect

Para habilitar o write-back de senha, primeiro precisamos habilitar o recurso do servidor que você instalou no Azure AD Connect.

1. Para configurar e habilitar o write-back de senha, entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
4. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global e escolha **Próximo**.
5. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
6. Na página **Recursos opcionais**, selecione a caixa ao lado de **Write-back de senha** e selecione **Próximo**.
7. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
8. Quando você vir a configuração terminar, selecione **Sair**.

## <a name="enable-password-writeback-option-in-sspr"></a>Habilitar a opção de write-back de senha no SSPR

Habilitar o recurso de write-back de senha no Azure AD Connect é apenas metade da história. Permitir que o SSPR use o write-back de senha conclui o loop, permitindo assim que os usuários que alteram ou redefinem suas senhas definam essas senhas também no local.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de Administrador Global.
2. Navegue até **Azure Active Directory**, clique em **Redefinição de senha** e, em seguida, escolha **Integração local**.
3. Defina a opção de **Write-back de senha para o diretório local** como **Sim**.
4. Defina a opção de **Permitir aos usuários desbloquear contas sem redefinir a senha** como **Sim**.
5. Clique em **Salvar**

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou o write-back de senha para a redefinição de senha de autoatendimento. Deixe a janela do portal do Azure aberta e continue para o próximo tutorial para definir as configurações adicionais relacionadas à redefinição de senha de autoatendimento antes de distribuir a solução no piloto.

> [!div class="nextstepaction"]
> [Avaliar o risco ao entrar](tutorial-risk-based-sspr-mfa.md)
