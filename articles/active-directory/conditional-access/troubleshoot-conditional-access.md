---
title: Solucionando problemas de conexão com acesso condicional-Azure Active Directory
description: Este artigo descreve o que fazer quando as políticas de acesso condicional resultam em resultados inesperados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92145090"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Solucionando problemas de conexão com acesso condicional

As informações neste artigo podem ser usadas para solucionar os resultados de entrada inesperados relacionados ao acesso condicional usando mensagens de erro e o log de entradas do Azure AD.

## <a name="select-all-consequences"></a>Selecione as consequências "todas"

A estrutura de acesso condicional fornece uma excelente flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que é necessário examinar cuidadosamente cada política de configuração, antes de liberá-la, para evitar resultados indesejáveis. Nesse contexto, preste atenção especial às atribuições que afetam conjuntos completos, como **todos os usuários/grupos/aplicativos de nuvem**.

As organizações devem evitar as seguintes configurações:

**Para todos os usuários, todos os aplicativos de nuvem:**

- **Bloquear o acesso** – essa configuração bloqueia toda a organização.
- **Exigir que o dispositivo seja marcado como compatível** -para usuários que ainda não registraram seus dispositivos, essa política bloqueia todo o acesso, incluindo acesso ao portal do Intune. Se você for um administrador sem um dispositivo registrado, essa política impedirá você voltar ao Portal do Azure para alterar a política.
- **Exigir dispositivo ingressado no domínio do Azure ad** – esse acesso ao bloqueio de política também tem o potencial de bloquear o acesso para todos os usuários em sua organização se eles não tiverem um dispositivo ingressado no Azure ad híbrido.
- **Exigir política de proteção de aplicativo** -este acesso ao bloqueio de política também tem o potencial de bloquear o acesso para todos os usuários em sua organização se você não tiver uma política do Intune. Caso você seja um administrador sem um aplicativo cliente que tenha uma política de proteção de aplicativo do Intune, essa política impedirá você de voltar para portais como o Intune e o Azure.

**Para todos os usuários, todos os aplicativos de nuvem, todas as plataformas de dispositivo:**

- **Bloquear o acesso** – essa configuração bloqueia toda a organização.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupção de entrada da Acesso condicional

A primeira forma é examinando a mensagem de erro que aparece. Para problemas de entrada ao usar um navegador da Web, a página de erro em si traz informações detalhadas. Essas informações sozinhas podem descrever qual é o problema e que podem sugerir uma solução.

![Erro de entrada – dispositivo em conformidade necessário](./media/troubleshoot-conditional-access/image1.png)

No erro acima, a mensagem informa que o aplicativo só pode ser acessado por dispositivos ou aplicativos cliente que atendam à política de gerenciamento de dispositivo móvel da empresa. Nesse caso, o aplicativo e o dispositivo não atendem a essa política.

## <a name="azure-ad-sign-in-events"></a>Eventos de entrada do Azure AD

O segundo método para obter informações detalhadas sobre a interrupção de entrada é examinando os eventos de entrada do Azure AD para ver quais políticas de Acesso condicional foram aplicadas e por quê.

Mais informações podem ser encontradas sobre o problema clicando em **mais detalhes** na página de erro inicial. Clicar em **mais detalhes** revelará informações de solução de problemas que são úteis ao pesquisar os eventos de entrada do Azure ad para o evento de falha específico que o usuário viu ou ao abrir um incidente de suporte com a Microsoft.

![Mais detalhes de uma entrada do navegador da Web interrompida pelo Acesso condicional.](./media/troubleshoot-conditional-access/image2.png)

Para descobrir qual política de acesso condicional ou políticas são aplicadas e por que fazer o seguinte.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou leitor global.
1. Navegue até **Azure Active Directory** > **Entradas**.
1. Localize o evento de entrada a ser analisado. Adicione ou remova filtros e colunas para filtrar informações desnecessárias.
   1. Adicione filtros para restringir o escopo:
      1. **ID de correlação** quando você tiver um evento específico para investigar.
      1. **Acesso condicional** para ver a falha de política e o êxito. Crie um escopo para o filtro para mostrar apenas as falhas e limitar os resultados.
      1. **Nome de usuário** para ver informações relacionadas a usuários específicos.
      1. **Data** do escopo para o intervalo de tempo em questão.

   ![Seleção do filtro de Acesso condicional no log de entradas](./media/troubleshoot-conditional-access/image3.png)

1. Depois que o evento de entrada que corresponde à falha de entrada do usuário for encontrado, selecione a guia **acesso condicional** . A guia acesso condicional mostrará a política ou políticas específicas que resultaram na interrupção de entrada.
   1. As informações na guia **solução de problemas e suporte** podem fornecer um motivo claro sobre por que uma falha de entrada, como um dispositivo que não atende aos requisitos de conformidade.
   1. Para investigar ainda mais, faça uma busca detalhada na configuração das políticas clicando no **nome da política**. Clicar no **nome da política** mostrará a interface do usuário de configuração de política para a política selecionada para revisão e edição.
   1. Os **detalhes do dispositivo** e do **usuário cliente** que foram usados para a avaliação da política de acesso condicional também estão disponíveis nas guias **informações básicas**, **localização**, **informações do dispositivo**, **detalhes de autenticação** e **detalhes adicionais** do evento de entrada.

### <a name="policy-details"></a>Detalhes da política

Ao selecionar as reticências no lado direito da política em um evento de entrada, são apresentados os detalhes da política. Isso fornece aos administradores informações adicionais sobre por que uma política foi aplicada com êxito ou não.

   ![Guia Acesso condicional do evento de entrada](./media/troubleshoot-conditional-access/image5.png)

   ![Detalhes da política (versão prévia)](./media/troubleshoot-conditional-access/policy-details.png)

O lado esquerdo fornece detalhes coletados na entrada e o lado direito fornece detalhes sobre se esses detalhes atendem aos requisitos das políticas de acesso condicional aplicadas. As políticas de Acesso condicional se aplicam somente quando todas as condições são satisfeitas ou não estão configuradas.

Se as informações no evento não forem suficientes para entender os resultados de entrada ou para ajustar a política para obter os resultados desejados, um incidente de suporte poderá ser aberto. Navegue até a guia **Solução de problemas e suporte** do evento de entrada e selecione **Criar uma nova solicitação de suporte**.

![A guia Solução de problemas e suporte do evento de entrada](./media/troubleshoot-conditional-access/image6.png)

Ao enviar o incidente, forneça a ID da solicitação e a hora e data do evento de entrada nos detalhes de envio do incidente. Essas informações permitirão que o suporte da Microsoft encontre o evento foco da preocupação.

### <a name="conditional-access-error-codes"></a>Códigos de erro de acesso condicional

| Código de erro de entrada | Cadeia de Caracteres de Erro |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>O que fazer se você estiver bloqueado no portal do Azure?

Se você estiver bloqueado do portal do Azure devido a uma configuração incorreta em uma política de acesso condicional:

- Verifique se existem outros administradores em sua organização que ainda não estão bloqueados. Um administrador com acesso ao portal do Azure pode desabilitar a política que está afetando sua entrada. 
- Se nenhum dos administradores da sua organização puder atualizar a política, envie uma solicitação de suporte. O suporte da Microsoft pode examinar e após a confirmação atualizar as políticas de acesso condicional que estão impedindo o acesso.

## <a name="next-steps"></a>Próximas etapas

- [Relatórios de atividades de entrada no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Solucionar problema de Acesso Condicional usando a ferramenta What If](troubleshoot-conditional-access-what-if.md)
