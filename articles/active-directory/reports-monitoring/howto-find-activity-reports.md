---
title: Localizar relatórios de atividade do usuário no portal do Azure | Microsoft Docs
description: Saiba onde os relatórios de atividade do usuário do Azure Active Directory estão no portal do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42afa073da9197c12e4cbd316d311a7699d9a95f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013013"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no Portal do Azure

Neste artigo, você aprenderá a localizar relatórios de atividades de usuários do Azure AD (Azure Active Directory) no portal do Azure.

## <a name="audit-logs-report"></a>Relatório de logs de auditoria

O relatório de logs de auditoria combina vários relatórios sobre atividades de aplicativos em uma única exibição para relatórios baseados em contexto. Para acessar o relatório de logs de auditoria:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** do painel de navegação esquerdo.
3. Selecione **Logs de auditorias** da seção **Atividade** da folha do Azure Active Directory. 

    ![Logs de auditoria](./media/howto-find-activity-reports/482.png "Logs de auditoria")

O relatório de logs de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de redefinição de senha
* Atividade de registro de redefinição de senha
* Atividade dos grupos de autoatendimento
* Alterações de nome do grupo do Office365
* Atividade de provisionamento de conta
* Status de substituição de senha
* Erros de provisionamento de conta

### <a name="filtering-on-audit-logs"></a>Filtragem em logs de auditoria

Você pode usar a filtragem avançada no relatório de auditoria para acessar uma categoria específica de dados de auditoria, especificando-a no filtro de **categoria** . Por exemplo, para exibir todas as atividades relacionadas a usuários, selecione a categoria **usermanagement** . 

As categorias incluem:

- Tudo
- AdministrativeUnit
- ApplicationManagement
- Autenticação
- Autorização
- Contact
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Outro
- Política
- ResourceManagement
- RoleManagement
- UserManagement

Você também pode filtrar em um serviço específico usando o filtro suspenso de **serviço** . Por exemplo, para obter todos os eventos de auditoria relacionados ao gerenciamento de senhas de autoatendimento, selecione o filtro de **Gerenciamento de senhas de autoatendimento** .

Os serviços incluem:

- Tudo
- Revisões de acesso
- Provisionamento de conta de usuário 
- SSO de aplicativo
- Métodos de autenticação
- B2C
- Acesso Condicional
- Diretório principal
- Gerenciamento de direitos
- Identity Protection
- Usuários Convidados
- PIM
- Gerenciamento de grupos de autoatendimento
- Gerenciamento de senhas de auto-atendimento
- Termos de Uso

## <a name="sign-ins-report"></a>Relatório de entradas 

A exibição **Entradas** inclui todas as entradas do usuário, bem como o relatório **Uso do Aplicativo**. Também é possível exibir as informações de uso do aplicativo na seção **Gerenciar** da visão geral dos **Aplicativos empresariais**.

Para acessar o relatório de entradas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** do painel de navegação esquerdo.
3. Selecione **Entradas** da seção **Atividade** da folha Azure Active Directory. 

    ![Exibição de entradas](./media/howto-find-activity-reports/483.png "Exibição de entradas")


### <a name="filtering-on-application-name"></a>Filtragem no nome do aplicativo

É possível usar o relatório de entradas para exibir detalhes sobre o uso do aplicativo, filtrando o nome de usuário ou o nome do aplicativo.

![Página filtrar eventos de Sign-In](./media/howto-find-activity-reports/07.png "Página filtrar eventos de Sign-In")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividades anômalas

Os relatórios de atividade anômala fornecem informações sobre as detecções de riscos relacionadas à segurança que o Azure AD pode detectar e relatar.

A tabela a seguir lista os relatórios de segurança da atividade anômala do Azure AD e os tipos de detecção de risco correspondentes no portal do Azure. Para obter mais informações, confira [Detecções de risco do Azure Active Directory](../identity-protection/overview-identity-protection.md).  


| Relatório de atividades anômalas do Azure AD |  Tipo de detecção de risco de proteção de identidade|
| :--- | :--- |
| Usuários com credenciais vazadas | Credenciais vazadas |
| Atividades de entrada irregulares | Viagem impossível a locais atípicos |
| Entradas de dispositivos possivelmente infectados | Entradas de dispositivos infectados|
| Entradas de fontes desconhecidas | Entradas de endereços IP anônimos |
| Entradas de endereços IP com atividade suspeita | Entradas de endereços IP com atividade suspeita |
| - | Entradas de locais desconhecidos |

Os seguintes relatórios de segurança de atividade anômala do Azure AD não estão incluídos como detecções de risco no portal do Azure:

* Entradas após várias falhas
* Entradas de várias geografias


### <a name="detected-risk-detections"></a>Detecções de risco detectadas

Você pode acessar relatórios sobre as detecções de risco detectadas na seção **segurança** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). As detecções de risco detectadas são controladas nos seguintes relatórios:   

- [Usuários em risco](../identity-protection/overview-identity-protection.md)
- [Entradas de risco](../identity-protection/overview-identity-protection.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Solucionar problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados ausentes nos logs de atividade baixados

#### <a name="symptoms"></a>Sintomas 

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Captura de tela mostra o botão baixar no relatório de atividade.](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Quando você baixa os logs de atividade no portal do Azure, limitamos a escala a 250000 registros, classificados por mais recentes primeiro. 

#### <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](concept-reporting-api.md) para buscar até um milhões de registros em qualquer momento determinado.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Dados de auditoria ausentes para ações recentes no portal do Azure

#### <a name="symptoms"></a>Sintomas

Eu executei algumas ações no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Audit Logs`, mas não é possível encontrá-los.

 ![Captura de tela mostra o relatório de atividade.](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretório | 2 minutos | 5 min |
| Atividade de entrada | 2 minutos | 5 min |

#### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Logs ausentes para recentes entradas de usuário no Azure AD

#### <a name="symptoms"></a>Sintomas

Eu entrei recentemente no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Sign-ins`, mas não é possível encontrá-los.

 ![Captura de tela mostra entradas para Azure Active Directory.](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretório | 2 minutos | 5 min |
| Atividade de entrada | 2 minutos | 5 min |

#### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo exibir mais de 30 dias de dados de relatório no portal do Azure

#### <a name="symptoms"></a>Sintomas

Não consigo exibir mais de 30 dias de dados de entrada e de auditoria no portal do Azure. Por quê? 

 ![Captura de tela mostra o menu de data.](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazenam relatórios de atividades para as durações a seguir:

| Relatório           | AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---
| Auditoria de Diretório  | 7 dias        | 30 dias             | 30 dias             |
| Atividade de Entrada | Não disponível. Você pode acessar sua própria atividade de entrada por 7 dias na folha de perfil do usuário individual | 30 dias | 30 dias             |

Para saber mais informações, confira [Políticas de retenção de relatório do Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Resolução

Você tem duas opções para manter os dados por mais de 30 dias. Você pode usar as [APIs de emissão de relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio de programação e armazená-los em um banco de dados. Como alternativa, você pode integrar os logs de auditoria em um sistema SIEM de terceiros, como o Splunk ou SumoLogic.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de logs de auditoria](concept-audit-logs.md)
* [Visão geral de entradas](concept-sign-ins.md)
* [Visão geral de eventos de risco](../identity-protection/overview-identity-protection.md)