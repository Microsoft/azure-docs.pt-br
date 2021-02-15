---
title: Como gerenciar administradores locais em dispositivos ingressados no Azure AD
description: Saiba como atribuir funções do Azure para o grupo de administradores locais de um dispositivo do Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d482f21955b76e6b90523afe3b4933378c91d36e
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107354"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Como gerenciar o grupo de administradores locais nos dispositivos do Microsoft Azure Active Directory

Para gerenciar um dispositivo Windows, você precisa ser um membro do grupo Administradores local. Como parte do processo de junção do Azure Active Directory (Azure AD), o Azure AD atualiza os membros desse grupo em um dispositivo. Você pode personalizar a atualização de associação para satisfazer seus requisitos de negócios. Uma atualização de associação é, por exemplo, útil se você quiser habilitar sua equipe de assistência técnica para realizar tarefas que exigem direitos de administrador em um dispositivo.

Este artigo explica como a atualização de associação de administradores locais funciona e como você pode personalizá-la durante uma junção do Azure AD. O conteúdo deste artigo não se aplica a um dispositivo **ingressado no Azure ad híbrido** .

## <a name="how-it-works"></a>Como ele funciona

Quando você conecta um dispositivo Windows com o Azure AD usando uma junção do Azure AD, o Azure AD adiciona as seguintes entidades de segurança ao grupo local de administradores no dispositivo:

- Função de administrador global do Microsoft Azure Active Directory
- Função de administrador de dispositivo do Microsoft Azure Active Directory 
- O usuário que está executando o ingresso no Microsoft Azure Active Directory   

Com a adição de funções do Microsoft Azure ADao grupo Administradores local, você pode atualizar os usuários que podem gerenciar um dispositivo a qualquer momento no Microsoft Azure AD sem modificar algo no dispositivo. O Azure Active Directory também adiciona a função de administrador do dispositivo do Microsoft Azure Active Directory ao grupo Administradores local para dar suporte o princípio do privilégio mínimo (PoLP). Além dos administradores globais, você também pode habilitar os usuários que foram *apenas* atribuídos à função de administrador do dispositivo para gerenciar um dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gerenciar a função de administradores globais

Para exibir e atualizar a associação de função de administrador global, consulte:

- [Ver todos os membros de uma função de administrador no Azure Active Directory](../roles/manage-roles-portal.md)
- [Atribuir um usuário às funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gerenciar a função de administrador do dispositivo 

No portal do Azure, você pode gerenciar a função de administrador do dispositivo na página **Dispositivos**. Vá para a página **Dispositivos**:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.
1. Pesquise *Azure Active Directory* e selecione-o.
1. Na seção **Gerenciar**, clique em **Dispositivos**.
1. Na página **Dispositivos**, clique em **Configurações de Dispositivo**.

Para modificar a função de administrador do dispositivo, configure **Administradores locais adicionais no Microsoft Azure Active Directory ingressado em dispositivos**.  

![Administradores locais adicionais](./media/assign-local-admin/10.png)

>[!NOTE]
> Essa opção exige um locatário do Azure AD Premium. 

Administradores do dispositivo são atribuídos a todos os dispositivos ingressados do Microsoft Azure AD. Não é possível definir o escopo de administradores do dispositivo para um conjunto específico de dispositivos. Atualizar a função de administrador do dispositivo não tem necessariamente um impacto imediato sobre os usuários afetados. Em dispositivos em que um usuário já está conectado, a elevação de privilégio ocorre quando *ambas* as ações abaixo acontecem:

- Até 4 horas passaram para o Azure AD emitir um novo token de atualização primário com os privilégios apropriados. 
- O usuário sai e faz logon, não bloqueia/desbloqueie, para atualizar seu perfil.

>[!NOTE]
> As ações acima não são aplicáveis a usuários que não entraram no dispositivo relevante anteriormente. Nesse caso, os privilégios de administrador são aplicados imediatamente após sua primeira entrada no dispositivo. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Gerenciar privilégios de administrador usando grupos do Azure AD (versão prévia)

>[!NOTE]
> Esse recurso atualmente está em versão prévia.


A partir da atualização do Windows 10 2004, você pode usar os grupos do Azure AD para gerenciar privilégios de administrador em dispositivos ingressados no Azure AD com a política de MDM de [Grupos restritos](/windows/client-management/mdm/policy-csp-restrictedgroups) . Essa política permite que você atribua usuários individuais ou grupos do Azure AD ao grupo de administradores locais em um dispositivo ingressado no Azure AD, fornecendo a granularidade para configurar administradores distintos para diferentes grupos de dispositivos. 

>[!NOTE]
> Iniciando a atualização do Windows 10 20H2, é recomendável usar a política de [usuários e grupos locais](/windows/client-management/mdm/policy-csp-localusersandgroups) em vez da política de grupos restritos


Atualmente, não há nenhuma interface do usuário no Intune para gerenciar essas políticas e elas precisam ser configuradas usando [configurações de OMA-URI personalizadas](/mem/intune/configuration/custom-settings-windows-10). Algumas considerações sobre o uso de qualquer uma dessas políticas: 

- A adição de grupos do Azure AD por meio da política requer o SID do grupo que pode ser obtido pela execução da [API Microsoft Graph para grupos](/graph/api/resources/group?view=graph-rest-beta). O SID é definido pela propriedade `securityIdentifier` na resposta da API.
- Quando a política de grupos restritos é imposta, qualquer membro atual do grupo que não está na lista de membros é removido. Assim, a imposição dessa política com novos membros ou grupos removerá os administradores existentes, ou seja, o usuário que ingressou no dispositivo, a função de administrador de dispositivo e a função de administrador global do dispositivo. Para evitar a remoção de membros existentes, você precisa configurá-los como parte da lista de membros na política de grupos restritos. Essa limitação será resolvida se você usar a política usuários e grupos locais que permite atualizações incrementais na associação de grupo
- Os privilégios de administrador usando ambas as políticas são avaliados apenas para os seguintes grupos conhecidos em um dispositivo Windows 10: administradores, usuários, convidados, usuários avançados, Área de Trabalho Remota usuários e usuários de gerenciamento remoto. 
- O gerenciamento de administradores locais usando grupos do Azure AD não é aplicável a dispositivos registrados no Azure ad híbrido ou ao Azure AD.
- Embora a política de grupos restritos existia antes da atualização do Windows 10 2004, ela não dava suporte a grupos do Azure AD como membros do grupo de administradores locais de um dispositivo. 

## <a name="manage-regular-users"></a>Gerenciar usuários regulares

Por padrão, o Microsoft Azure Active Directory adiciona o usuário que está executando o ingresso no Microsoft Azure Active Directory ao grupo administrador no dispositivo. Se você quiser impedir que os usuários normais se tornem os administradores locais, você tem as seguintes opções:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot lhe oferece uma opção para impedir que o usuário primário que está executando a junção de se tornar um administrador local. Você pode fazer isso [criando um perfil do Autopilot](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Registro em massa](/intune/windows-bulk-enroll) -um ingresso no Microsoft Azure Active Directory que é executado no contexto de um registro em massa acontece no contexto de um usuário criado automaticamente. Usuários entrando depois que um dispositivo foi associado não são adicionados ao grupo de administradores.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevar manualmente um usuário em um dispositivo 

Além de usar o processo de ingresso do Microsoft Azure Active Directory, você pode elevar manualmente um usuário normal para se tornar um administrador local em um dispositivo específico. Esta etapa requer que você já seja um membro do grupo de administradores locais. 

A partir da versão **10 1709 do Windows** , você pode executar essa tarefa em **configurações-> contas-> outros usuários**. Selecione **Adicionar um usuário ou de estudante**, insira o UPN do usuário sob a **conta de usuário** e selecione *administrador* sob **tipo de conta**  
 
Além disso, você também pode adicionar usuários usando o prompt de comando:

- Se os usuários de locatário são sincronizadosno local do Active Directory, use `net localgroup administrators /add "Contoso\username"`.
- Se os usuários de locatário são criados no Microsoft Azure Active Directory, use `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerações 

Você não pode atribuir grupos à função de administrador do dispositivo, somente usuários individuais são permitidos.

Administradores do dispositivo são atribuídos a todos os dispositivos ingressados do Microsoft Azure Active Directory. Eles não podem ser limitados a um conjunto específico de dispositivos.

Quando você remove os usuários da função de administrador do dispositivo, ele ainda tem o privilégio de administrador local em um dispositivo, desde que eles se conectem a ele. O privilégio é revogado durante a próxima entrada quando um novo token de atualização primário é emitido. Essa revogação, semelhante à elevação de privilégio, pode levar até 4 horas.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral de como gerenciar dispositivos no portal do Azure, consulte [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md)
- Para saber mais sobre o acesso condicional baseado em dispositivo, confira [configurar Azure Active Directory políticas de acesso condicional com base no dispositivo](../conditional-access/require-managed-devices.md).
