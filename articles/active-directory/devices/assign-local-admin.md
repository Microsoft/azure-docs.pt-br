---
title: Como gerenciar o grupo de administradores locais nos dispositivos do Microsoft Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções do Azure para o grupo de administradores locais de um dispositivo do Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35cb6cba02a1bdcf9f19c7f02b7e2ca4d01e0d3f
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67983661"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Como gerenciar o grupo de administradores locais nos dispositivos do Microsoft Azure Active Directory

Para gerenciar um dispositivo Windows, você precisa ser um membro do grupo Administradores local. Como parte do processo de junção do Azure Active Directory (Azure AD), o Azure AD atualiza os membros desse grupo em um dispositivo. Você pode personalizar a atualização de associação para satisfazer seus requisitos de negócios. Uma atualização de associação é, por exemplo, útil se você quiser habilitar sua equipe de assistência técnica para realizar tarefas que exigem direitos de administrador em um dispositivo.

Este artigo explica como a atualização de associação funciona e como é possível personalizá-la durante um ingresso no Azure AD. O conteúdo deste artigo não aplica-se a um ingresso no Azure AD **híbrido**.

## <a name="how-it-works"></a>Como ele funciona

Quando você conectar um dispositivo Windows com o Microsoft Azure AD usando uma junção do Microsoft Azure AD, o Microsoft Azure AD adiciona os seguintes princípios de segurança ao grupo Administradores local no dispositivo:

- Função de administrador global do Microsoft Azure Active Directory
- Função de administrador de dispositivo do Microsoft Azure Active Directory 
- O usuário que está executando o ingresso no Microsoft Azure Active Directory   

Com a adição de funções do Microsoft Azure ADao grupo Administradores local, você pode atualizar os usuários que podem gerenciar um dispositivo a qualquer momento no Microsoft Azure AD sem modificar algo no dispositivo. Atualmente, é possível atribuir grupos a uma função de administrador.
O Azure Active Directory também adiciona a função de administrador do dispositivo do Microsoft Azure Active Directory ao grupo Administradores local para dar suporte o princípio do privilégio mínimo (PoLP). Além dos administradores globais, você também pode habilitar os usuários que foram *apenas* atribuídos à função de administrador do dispositivo para gerenciar um dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gerenciar a função de administradores globais

Para exibir e atualizar a associação de função de administrador global, consulte:

- [Ver todos os membros de uma função de administrador no Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Atribuir um usuário às funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gerenciar a função de administrador do dispositivo 

No portal do Azure, você pode gerenciar a função de administrador do dispositivo na página **Dispositivos**. Vá para a página **Dispositivos**:

1. Entre no [portal do Azure](https://portal.azure.com) como administrador global ou administrador de usuários.
1. Na barra de navegação à esquerda, clique em **Azure Active Directory**. 
1. Na seção **Gerenciar**, clique em **Dispositivos**.
1. Na página **Dispositivos**, clique em **Configurações de Dispositivo**.

Para modificar a função de administrador do dispositivo, configure **Administradores locais adicionais no Microsoft Azure Active Directory ingressado em dispositivos**.  

![Administradores locais adicionais](./media/assign-local-admin/10.png)

>[!NOTE]
> Essa opção exige um locatário do Azure AD Premium. 

Administradores do dispositivo são atribuídos a todos os dispositivos ingressados do Microsoft Azure AD. Não é possível definir o escopo de administradores do dispositivo para um conjunto específico de dispositivos. Atualizar a função de administrador do dispositivo não tem necessariamente um impacto imediato sobre os usuários afetados. Para os dispositivos, um usuário já está conectado, a atualização de privilégio ocorre:

- Quando um usuário sair.
- Após 4 horas, quando um novo primário Token de atualização for emitido. 

## <a name="manage-regular-users"></a>Gerenciar usuários regulares

Por padrão, o Microsoft Azure Active Directory adiciona o usuário que está executando o ingresso no Microsoft Azure Active Directory ao grupo administrador no dispositivo. Se você quiser impedir que os usuários normais se tornem os administradores locais, você tem as seguintes opções:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot lhe oferece uma opção para impedir que o usuário primário que está executando a junção de se tornar um administrador local. Você pode fazer isso [criando um perfil do Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Registro em massa](https://docs.microsoft.com/intune/windows-bulk-enroll) -um ingresso no Microsoft Azure Active Directory que é executado no contexto de um registro em massa acontece no contexto de um usuário criado automaticamente. Usuários entrando depois que um dispositivo foi associado não são adicionados ao grupo de administradores.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevar manualmente um usuário em um dispositivo 

Além de usar o processo de ingresso do Microsoft Azure Active Directory, você pode elevar manualmente um usuário normal para se tornar um administrador local em um dispositivo específico. Esta etapa requer que você já seja um membro do grupo de administradores locais. 

A partir da versão **10 1709 do Windows** , você pode executar essa tarefa em **Configurações-> contas-> outros usuários**. Selecione **Adicionar um usuário ou de estudante**, insira o UPN do usuário sob a **conta de usuário** e selecione *administrador* sob **tipo de conta**  
 
Além disso, você também pode adicionar usuários usando o prompt de comando:

- Se os usuários de locatário são sincronizadosno local do Active Directory, use `net localgroup administrators /add "Contoso\username"`.
- Se os usuários de locatário são criados no Microsoft Azure Active Directory, use `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerações 

Você não pode atribuir grupos à função de administrador do dispositivo, somente usuários individuais são permitidos.

Administradores do dispositivo são atribuídos a todos os dispositivos ingressados do Microsoft Azure Active Directory. Eles não podem ser limitados a um conjunto específico de dispositivos.

Quando você remove os usuários da função de administrador do dispositivo, ele ainda tem o privilégio de administrador local em um dispositivo, desde que eles se conectem a ele. O privilégio é revogado durante o próximo logon, ou após 4 horas, quando um novo token de atualização principal é emitido.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de como gerenciar dispositivos no portal do Azure, consulte [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md)
- Para saber mais sobre o acesso condicional baseado em dispositivo, confira [configurar Azure Active Directory políticas de acesso condicional com base no dispositivo](../conditional-access/require-managed-devices.md).
