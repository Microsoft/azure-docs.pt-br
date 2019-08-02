---
title: Habilitar o Enterprise State Roaming no Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre as configurações do Enterprise State Roaming em dispositivos do Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45c1fc6340df6a5400864b2e1222a2c65e586232
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482026"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Habilitar o Enterprise State Roaming no Active Directory do Azure
Enterprise State Roaming está disponível para qualquer organização com uma licença Azure AD Premium ou Enterprise Mobility + Security (EMS). Para saber mais sobre como obter uma assinatura do Azure AD, confira a [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory).

Quando você habilita o Enterprise State Roaming, sua organização recebe automaticamente uma licença gratuita de uso limitado para o Azure Rights Management da Proteção de Informações do Azure. Essa assinatura gratuita é limitada a criptografar e descriptografar configurações da empresa e dados de aplicativo sincronizados por Enterprise State Roaming. Você deve ter [uma assinatura paga](https://azure.microsoft.com/pricing/details/information-protection/) para usar os recursos completos do serviço do Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Para habilitar o Enterprise State Roaming

1. Entre no [Centro de administração do Azure AD](https://aad.portal.azure.com/).
1. Selecione **Azure Active Directory** &gt; **Dispositivos** &gt; **Enterprise State Roaming**.
1. Selecione **Usuários podem sincronizar configurações e dados de aplicativo entre dispositivos**. Para obter mais informações, confira [como definir as configurações do dispositivo](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![A imagem da configuração do dispositivo rotulada como Usuários pode sincronizar configurações e dados de aplicativo entre dispositivos](./media/enterprise-state-roaming-enable/device-settings.png)
  
Para que um dispositivo Windows 10 use o serviço Enterprise State Roaming, o dispositivo deverá se autenticar usando uma identidade do Azure AD. Para os dispositivos ingressados no Azure AD, a identidade de logon principal do usuário será a identidade do Azure AD, portanto, não será necessária nenhuma configuração adicional. Para dispositivos que usam o Active Directory local, o administrador de TI precisa [Configurar dispositivos ingressados no Azure Active Directory híbrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual-steps). 

## <a name="data-storage"></a>Armazenamento de dados
Os dados do Enterprise State Roaming são hospedados em uma ou mais [regiões do Azure](https://azure.microsoft.com/regions/) que se alinhem melhor ao valor de país/região definido na instância do Azure Active Directory. Os dados do Enterprise State Roaming são particionados com base em três regiões geográficas principais: América do Norte, EMEA e APAC. Dados de Enterprise State Roaming para o locatário estão localizados localmente com a região geográfica e não são replicados entre regiões.  Por exemplo:

| Valor de país/região | tem os dados hospedados em |
| -------------------- | ------------------------ |
| Um país/região EMEA, como França ou Zâmbia | Uma ou mais das regiões do Azure na Europa |
| Um país/região na América do Norte, como Estados Unidos ou Canadá | Uma ou mais das regiões do Azure nos EUA |
| Um país/região APAC, como na Austrália ou na Nova Zelândia | Uma ou mais das regiões do Azure na Ásia |
| Regiões da América do Sul e da Antártida | Uma ou mais regiões do Azure nos EUA |

O valor de país/região é definido como parte do processo de criação de domínio do Azure AD e não pode ser modificado posteriormente. Se você precisar de mais detalhes sobre o local de armazenamento de dados, crie um tíquete no [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Exibir status de sincronização do dispositivo por usuário
Siga estas etapas para exibir um relatório de status de sincronização de dispositivo por usuário.

1. Entre no [Centro de administração do Azure AD](https://aad.portal.azure.com/).
1. Selecione **Azure Active Directory** &gt; **usuários** &gt; **todos os usuários**.
1. Selecione o usuário e, em seguida, selecione **Dispositivos**.
1. Em **Mostrar**, selecione **Dispositivos sincronizando configurações e dados de aplicativo** para mostrar o status de sincronização.
  
   ![imagem da configuração de dados de sincronização do dispositivo](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Se houver dispositivos fazendo sincronização para esse usuário, você verá os dispositivos conforme mostrado aqui.
  
   ![imagem de dados de colunas de sincronização do dispositivo](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Retenção de dados
Os dados sincronizados com a nuvem da Microsoft usando o Enterprise State Roaming são mantidos até serem excluídos manualmente ou até que os dados em questão sejam considerados obsoletos. 

### <a name="explicit-deletion"></a>Exclusão explícita
Exclusão explícita é quando o administrador do Azure exclui um usuário ou diretório ou de outra forma solicita explicitamente a exclusão desses dados.

* **Exclusão de usuário**: quando um usuário é excluído do Azure AD, os dados de roaming da conta do usuário são excluídos depois de 90 a 180 dias. 
* **Exclusão de diretório**: a exclusão de um diretório inteiro no Azure AD é uma operação imediata. Todos os dados de configurações associados a esse diretório são excluídos depois de 90 a 180 dias. 
* **Exclusão mediante solicitação**: se o administrador do Azure AD quiser excluir manualmente os dados de configuração de um usuário específico, poderá criar um [tíquete no suporte do Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Exclusão de dados obsoletos
Os dados que não forem acessados por um ano ("o período de retenção") serão tratados como obsoletos e poderão ser excluídos da nuvem da Microsoft. O período de retenção está sujeito a alterações, mas não será menos de 90 dias. Os dados obsoletos podem ser um conjunto específico de configurações do Windows/aplicativo ou todas as configurações para um usuário específico. Por exemplo:

* Se nenhum dispositivo acessar uma coleção de configurações em particular (por exemplo, um aplicativo é removido do dispositivo ou um grupo de configurações, como "Tema" é desabilitado para todos os dispositivos do usuário), essa coleção se tornará obsoleta após o período de retenção e poderá ser excluída. 
* Se um usuário tiver desativado a sincronização de configurações em todos os seus dispositivos, em seguida, nenhum dos dados de configuração será acessado e todos os dados de configurações para esse usuário tornarão obsoletos e poderão ser excluídos após o período de retenção. 
* Se o administrador de diretório do AD do Azure desativar o Enterprise State Roaming para o diretório inteiro, todos os usuários desse diretório terão a sincronização de configurações interrompida, e todos os dados de configuração de todos os usuários se tornarão obsoletos e poderão ser excluídos após o período de retenção. 

### <a name="deleted-data-recovery"></a>Recuperação de dados excluídos
A política de retenção de dados não é configurável. Depois que os dados são excluídos permanentemente, eles não são recuperáveis. No entanto, os dados de configurações são excluídos somente da nuvem da Microsoft, não do dispositivo do usuário final. Se algum dispositivo se reconectar mais tarde ao serviço Enterprise State Roaming, as configurações serão sincronizadas e armazenadas novamente na nuvem da Microsoft.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Perguntas frequentes sobre configurações e roaming de dados](enterprise-state-roaming-faqs.md)
* [Política de grupo e as configurações do MDM para a sincronização de configurações](enterprise-state-roaming-group-policy-settings.md)
* [Referência de configurações de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Solução de problemas](enterprise-state-roaming-troubleshooting.md)
