---
title: Azure AD Connect Health e privacidade de usuário | Microsoft Docs
description: Este documento descreve a privacidade do usuário com o Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ecdfefa79716bcc0a69063293d5a1067693a78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182353"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>Privacidade do usuário e Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata do Azure AD Connect Health e da privacidade do usuário.  Para obter informações sobre o Azure AD Connect e a privacidade do usuário, consulte o artigo [aqui](reference-connect-user-privacy.md).

## <a name="user-privacy-classification"></a>Classificação de privacidade do usuário
O Azure AD Connect Health se encaixa na categoria **processador de dados** da classificação do GDPR. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados para parceiros-chave e consumidores finais. O Azure AD Connect Health não gera dados de usuário e não possui controle independente com relação aos dados pessoais coletados e como são utilizados. A recuperação de dados, agregação, análise e relatórios no Azure AD Connect Health são baseados em dados locais existentes. 

## <a name="data-retention-policy"></a>Política de retenção de dados
O Azure AD Connect Health não gera relatórios, realiza análise nem fornece informações com mais de 30 dias. Portanto, o Azure AD Connect Health não armazena, processa ou retém quaisquer dados com mais de 30 dias. Este design está em conformidade com os regulamentos do GDPR, com os regulamentos de conformidade de privacidade da Microsoft e com as políticas de retenção de dados do Azure Active Directory. 

Servidores com alertas **Os dados do serviço de integridade não estão atualizados de ** **erro** ativo por mais de 30 dias consecutivos sugerem que nenhum dado atingiu o Connect Health durante esse intervalo de tempo. Esses servidores serão desabilitados e não exibidos no Portal do Connect Health. Para habilitar novamente os servidores, será necessário desinstalar e [reinstalar o agente de integridade](how-to-connect-health-agent-install.md). Observe que isso não se aplica a **avisos** com o mesmo tipo de alerta. Os avisos indicam que faltam dados parciais do servidor do qual você é alertado. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Desabilitar a coleta e monitoramento de dados no Azure AD Connect Health
O Azure AD Connect Health habilita interromper a coleta de dados para cada servidor monitorado individual ou para uma instância de um serviço monitorado. Por exemplo, é possível parar a coleta de dados de servidores individuais AD FS (Serviços de Federação do Active Directory) que são monitorados usando o Azure AD Connect Health. Você também pode parar a coleta de dados de toda a instância do AD FS que está sendo monitorada usando o Azure AD Connect Health. Ao optar por fazer isso, os servidores correspondentes serão excluídos do Portal do Azure AD Connect Health após parar a coleta de dados. 

>[!IMPORTANT]
> Você precisa de privilégios de administrador global do Azure AD ou da função colaborador no RBAC do Azure para excluir servidores monitorados da Azure AD Connect Health.
>
> Remover uma instância de serviço ou servidor do Azure AD Connect Health não é uma ação reversível. 

### <a name="what-to-expect"></a>O que esperar?
Se você parar a coleta e monitoramento de dados de um servidor monitorado individual ou uma instância de um serviço monitorado, observe o seguinte:

- Ao excluir uma instância de um serviço monitorado, a instância será removida da lista de serviços de monitoramento do Azure AD Connect Health no portal. 
- Ao excluir um servidor monitorado ou uma instância de um serviço monitorado, o Agente de Integridade NÃO será desinstalado nem removido dos servidores. O Agente de Integridade é configurado para não enviar dados para o Azure AD Connect Health. É necessário desinstalar manualmente o Agente de Integridade em servidores anteriormente monitorados.
- Se você não tiver desinstalado o agente de integridade antes de executar essa etapa, poderá ver os eventos de erro no(s) servidor(es) relacionado(s) ao agente de integridade.
- Todos os dados pertencentes à instância do serviço monitorado são excluídos de acordo com a Política de Retenção de Dados do Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Desabilitar a coleta e o monitoramento de dados para uma instância de um serviço monitorado
Consulte [Como remover uma instância de serviço do Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Desabilitar a coleta de dados e o monitoramento de um servidor monitorado
Consulte [Como remover um servidor do Azure AD Connect Health](how-to-connect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>Desabilitar a coleta e monitoramento de dados para todos os serviços monitorados no Azure AD Connect Health
O Azure AD Connect Health também fornece a opção de interromper a coleta de dados de **todos** os serviços registrados no locatário. É recomendável uma consideração cuidadosa e um conhecimento completo de todos os administradores globais antes de realizar a ação. Quando o processo começar, o serviço Connect Health irá parar de receber, processar e relatar quaisquer dados de todos os serviços. Os dados existentes no serviço Connect Health serão mantidos por não mais de 30 dias.
Se quiser interromper a coleta de dados de um servidor específico, siga as etapas de exclusão de servidores específicos. Para interromper a coleta de dados de locatários, siga as etapas a seguir para interromper a coleta de dados e excluir todos os serviços do locatário.

1. Clique em **Configurações gerais** em configuração na folha do principal. 
2. Clique no botão **Interromper a coleta de dados** na parte superior da folha. As outras opções de definições de configuração de locatário serão desabilitadas quando o processo for iniciado.  
 
   ![Interromper a coleta de dados](./media/reference-connect-health-user-privacy/gdpr4.png)
  
3. Verifique se a lista de serviços incorporados que são afetados pela interrupção de coletas de dados. 
4. Insira o nome exato do locatário para habilitar o botão de ação **Excluir**.
5. Clique em **Excluir** para disparar a exclusão de todos os serviços. O Connect Health interromperá a recepção, o processamento e o relatório de quaisquer dados enviados de seus serviços integrados. Todo o processo de pode pode levar até 24 horas. Observe que essa etapa não é reversível. 
6. Depois que o processo for concluído, você não verá mais nenhum serviço registrado no Connect Health. 

   ![Após a interrupção da coleta de dados](./media/reference-connect-health-user-privacy/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Habilitar novamente a coleta e o monitoramento de dados no Azure AD Connect Health
Para habilitar novamente o monitoramento no Azure AD Connect Health de um serviço monitorado excluído anteriormente, é necessário desinstalar e [reinstalar o agente de integridade](how-to-connect-health-agent-install.md) em todos os servidores.

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>Reabilitar a coleta de dados e o monitoramento de todos os servidores monitorados

A coleta de dados de locatários pode ser retomada no Azure AD Connect Health. É recomendável uma consideração cuidadosa e um conhecimento completo de todos os administradores globais antes de realizar a ação.

>[!IMPORTANT]
> As etapas a seguir estarão disponíveis após 24 horas da ação de desabilitar.
> Após a habilitação da coleta de dados, o insight apresentado e dados de monitoramento no Connect Health não mostrarão todos os dados herdados coletados antes. 

1. Clique em **Configurações gerais** em configuração na folha do principal. 
2. Clique no botão **Habilitar a coleta de dados** na parte superior da folha. 
 
   ![Habilitar coleta de dados](./media/reference-connect-health-user-privacy/gdpr6.png)
 
3. Insira o nome exato de locatário para ativar o botão **Habilitar**.
4. Clique no botão **Habilitar** para conceder permissão de coleta de dados no serviço Connect Health. A alteração será aplicada em breve. 
5. Siga o [processo de instalação](how-to-connect-health-agent-install.md) para reinstalar o agente nos servidores a serem monitorados e os serviços estarão presentes no portal.  


## <a name="next-steps"></a>Próximas etapas
* [Revise a Política de Privacidade da Microsoft na Central de Confiabilidade](https://www.microsoft.com/trustcenter)
* [Azure AD Connect e Privacidade do Usuário](reference-connect-user-privacy.md)

