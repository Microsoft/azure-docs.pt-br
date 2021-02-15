---
title: Acesso condicional-bloquear acesso-Azure Active Directory
description: Criar uma política de acesso condicional personalizada para
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366183"
---
# <a name="conditional-access-block-access"></a>Acesso condicional: bloquear acesso

Para organizações com uma abordagem de migração de nuvem conservadora, a política bloquear tudo é uma opção que pode ser usada. 

> [!CAUTION]
> A configuração incorreta de uma política de bloqueio pode levar as organizações sendo bloqueadas na portal do Azure.

Políticas como essas podem ter efeitos colaterais indesejados. Os testes e a validação adequados são vitais antes da habilitação. Os administradores devem utilizar ferramentas como o [modo somente de relatório de acesso condicional](concept-conditional-access-report-only.md) e [a ferramenta de What If no acesso condicional](what-if-tool.md) ao fazer alterações.

## <a name="user-exclusions"></a>Exclusões de usuário

As políticas de Acesso Condicional são ferramentas avançadas, recomendamos excluir as seguintes contas da sua política:

* Contas de **acesso de emergência** ou de **interrupção** para impedir o bloqueio de conta em todo o locatário. No cenário improvável de todos os administradores serem bloqueados de seu locatário, sua conta administrativa de acesso de emergência poderá ser usada para fazer logon no locatário. Siga as etapas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo [Gerenciar contas de acesso de emergência no Azure AD](../roles/security-emergency-access.md).
* **Contas de serviço** e **entidades de serviço**, como a conta de sincronização do Azure AD Connect. As contas de serviço são contas não interativas que não estão ligadas a nenhum usuário específico. Normalmente, elas são usadas por serviços de back-end que permitem acesso programático a aplicativos, mas também são usadas para entrar em sistemas para fins administrativos. Contas de serviço como essas devem ser excluídas, pois a MFA não pode ser concluída programaticamente. As chamadas feitas por entidades de serviço não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver essas contas em uso em scripts ou código, considere substituí-las por [identidades gerenciadas](../managed-identities-azure-resources/overview.md). Como solução temporária, você pode excluir essas contas específicas da política de linha de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

As etapas a seguir ajudarão a criar políticas de acesso condicional para bloquear o acesso a todos os aplicativos, exceto para o [Office 365](concept-conditional-access-cloud-apps.md#office-365) , se os usuários não estiverem em uma rede confiável. Essas políticas são colocadas no [modo somente de relatório](howto-conditional-access-insights-reporting.md) para iniciar, para que os administradores possam determinar o impacto que eles terão sobre os usuários existentes. Quando os administradores se sentem confortáveis de que as políticas se aplicam conforme pretendido, eles podem alterná-los para **ativado**.

A primeira política bloqueia o acesso a todos os aplicativos, exceto para aplicativos Microsoft 365 se não estiver em um local confiável.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e Grupos**.
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações**, selecione as seguintes opções:
   1. Em **incluir**, selecione **todos os aplicativos de nuvem**.
   1. Em **excluir**, selecione **Office 365**, selecione **selecionar**e **concluído**.
1. Em **Condições**:
   1. Em **Condições** > **Localização**.
      1. Defina **Configurar** como **Sim**
      1. Em **incluir**, selecione **qualquer local**.
      1. Em **excluir**, selecione **todos os locais confiáveis**.
      1. Selecione **Concluído**.
   1. Em **aplicativos cliente (versão prévia)**, defina **Configurar** como **Sim**e selecione **concluído**e, em seguida, **concluído**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **bloquear acesso**e selecione **selecionar**.
1. Confirme suas configurações e defina **Habilitar política** com **Somente relatório**.
1. Selecione **Criar** para criar e habilitar sua política.

Uma segunda política é criada abaixo para exigir a autenticação multifator ou um dispositivo compatível para usuários de Microsoft 365.

1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e Grupos**.
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Em **Excluir**, selecione **Usuários e grupos** e escolha o acesso de emergência ou as contas de interrupção da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos de nuvem ou ações**  >  **incluem**, selecione **selecionar aplicativos**, **escolha Office 365**e selecione **selecionar**e, em seguida, **concluído**.
1. Em **Access controls**  >  **concessão**de controles de acesso, selecione **conceder acesso**.
   1. Selecione **exigir autenticação multifator** e **exigir que o dispositivo seja marcado como em conformidade** selecione **selecionar**.
   1. Certifique-se **de que exigir que todos os controles selecionados** esteja selecionado.
   1. Selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** com **Somente relatório**.
1. Selecione **Criar** para criar e habilitar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-insights-reporting.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
