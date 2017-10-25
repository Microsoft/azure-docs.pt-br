---
title: "Ingressar em um novo dispositivo Windows 10 com o Azure AD durante uma primeira execução | Microsoft Docs"
description: "Um tópico que explica como os usuários podem configurar a Junção do Azure AD durante sua experiência de primeira execução."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: b07386eeb0d3e05a0b4545be39030066fbdafd3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Ingressar em um novo dispositivo Windows 10 com o Azure AD durante uma primeira execução

Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais detalhes, confira a [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md).

Com o Windows 10, você pode adicionar um novo dispositivo ao Azure AD durante a FRX (experiência de primeira execução).  
Isso permite que você distribua dispositivos compactados aos seus funcionários ou alunos.

Se você tiver o Windows 10 Professional ou o Windows 10 Enterprise em um dispositivo, a experiência usará como padrão o processo de instalação de dispositivos de propriedade da empresa.

Não há suporte para o ingresso em um domínio local do AD (Active Directory) na *configuração inicial pelo usuário* do Windows. Se pretender adicionar um computador a um domínio do AD durante a configuração, será necessário selecionar o link **Configurar o Windows com uma conta local**. Em seguida, será possível ingressar no domínio pelas configurações do computador.
 


## <a name="before-you-begin"></a>Antes de começar

Para ingressar em um dispositivo Windows 10, o serviço de registro do dispositivo deverá ser configurado para permitir que você registre dispositivos. Além de ter permissão para ingressar em dispositivos em seu locatário do Azure AD, você deverá ter menos dispositivos registrados que o máximo configurado. Para obter mais detalhes, confira [Configurar configurações do dispositivo](device-management-azure-portal.md#configure-device-settings).

## <a name="joining-a-device"></a>Ingressar em um dispositivo

**Para ingressar em um dispositivo Windows 10 ao Azure AD durante a FRX:**


1. Ao ligar o novo dispositivo e iniciar o processo de instalação, você deverá ver a mensagem **Preparando-se** . Siga os prompts para configurar o dispositivo.

2. Inicie personalizando a região e o idioma. Em seguida, aceite os Termos de Licença para Software Microsoft.
 
    ![Personalizar a região](./media/device-management-azuread-joined-devices-frx/01.png)

3. Selecione a rede que deseja usar para se conectar à Internet.

4. Clique em **Este dispositivo pertence à minha organização**. 

    ![Tela Quem é o proprietário deste computador?](./media/device-management-azuread-joined-devices-frx/02.png)

5. Insira as credenciais que foram fornecidas a você por sua organização e clique em **Entrar**.

    ![Tela de entrada](./media/device-management-azuread-joined-devices-frx/03.png)

6. Seu dispositivo localiza um locatário correspondente no Azure AD. Se estiver em um domínio federado, você será redirecionado para o servidor local do STS (Serviço de Token Seguro), por exemplo, o Serviços de Federação do Active Directory (AD FS).

7. Se você for um usuário em um domínio não federado, será necessário inserir suas credenciais diretamente na página hospedada pelo Azure AD. 

8. Um desafio da autenticação multifator será apresentado a você. 
 
9. O Azure AD verifica se é necessário um registro no gerenciamento de dispositivo móvel.

10. O Windows registra o dispositivo no diretório da organização no Azure AD e faz o registro no gerenciamento de dispositivo móvel, se aplicável.

11. Se você for:
    - Um usuário gerenciado, o Windows o levará para a área de trabalho por meio do processo de entrada automática.

    - Um usuário federado, será direcionado para a tela de entrada do Windows para inserir suas credenciais.

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado ao seu Azure AD, examine a caixa de diálogo **Acesso corporativo ou de estudante** no seu dispositivo do Windows. A caixa de diálogo deverá indicar que você está conectado ao seu diretório do Azure AD.

![Acesso corporativo ou de estudante](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Próximas etapas

- Para obter mais detalhes, confira a [Introdução ao gerenciamento de dispositivos no Azure Active Directory](device-management-introduction.md).

- Para obter mais detalhes sobre como gerenciar dispositivos no portal do Azure AD, consulte [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md).
