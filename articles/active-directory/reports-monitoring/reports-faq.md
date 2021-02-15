---
title: Perguntas frequentes sobre os relatórios do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre Azure Active Directory relatórios.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 05/12/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b6b182d030c267dcec7ed57c0c0dd1901b0cb1
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935080"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Perguntas frequentes sobre os relatórios do Azure Active Directory

Este artigo inclui respostas a perguntas frequentes sobre os relatórios do Azure AD (Azure Active Directory). Para saber mais, consulte [Relatórios do Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introdução 

**P: atualmente uso as `https://graph.windows.net/<tenant-name>/reports/` APIs de ponto de extremidade para extrair relatórios de uso de aplicativos integrados e auditoria do Azure AD em nossos sistemas de relatórios de forma programática. O que devo mudar?**

**R:** Consulte a [Referência de API](https://developer.microsoft.com/graph/) para saber como é possível [usar as APIs para acessar os relatórios de atividades](concept-reporting-api.md). Esse ponto de extremidade tem dois relatórios (**Auditoria** e **Entradas**) que fornecem todos os dados que você obteve no ponto de extremidade de API antigo. Esse novo ponto de extremidade também tem um relatório de entradas com a licença do Azure AD Premium que você pode usar para obter informações de uso de aplicativo uso de dispositivo e conexão do usuário.

---

**P: atualmente uso as `https://graph.windows.net/<tenant-name>/reports/` APIs de ponto de extremidade para efetuar pull de relatórios de segurança do Azure AD (tipos específicos de detecções, como credenciais vazadas ou entradas de endereços IP anônimos) em nossos sistemas de relatórios programaticamente. O que devo mudar?**

**R:** Você pode usar a [API de detecções de risco da proteção de identidade](../identity-protection/howto-identity-protection-graph-api.md) para acessar as detecções de segurança por meio do Microsoft Graph. Esse novo formato oferece maior flexibilidade na forma como você pode consultar dados, com filtragem avançada, seleção de campo e muito mais, e padroniza as detecções de risco em um tipo para uma integração mais fácil em SIEMs e outras ferramentas de coleta de dados. Uma vez que os dados estão em um formato diferente, você não pode substituir uma nova consulta para suas consultas antigas. No entanto, [a nova API usa Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta), que é o padrão da Microsoft para tais APIs como Microsoft 365 ou o Azure AD. Portanto, o trabalho necessário pode estender seus investimentos atuais de Microsoft Graph ou ajudá-lo a começar sua transição para essa nova plataforma padrão.

---

**P: Como obtenho uma licença premium?**

**R:** Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory.

---

**P: Em quanto tempo deverei ver dados de atividades depois de obter uma licença premium?**

**R:** Se você já tiver dados de atividades como uma licença gratuita, poderá vê-los imediatamente. Se você não tiver dados, levará até três dias para que os dados sejam exibidos nos relatórios.

---

**P: Eu poderei visualizar os dados do último mês, após obter uma licença premium do Azure AD?**

**R:** Se você tiver alternado recentemente para uma versão Premium (incluindo uma versão de avaliação), poderá ver dados de até 7 dias inicialmente. Quando os dados forem acumulados, será possível visualizar os dados dos últimos 30 dias.

---

**P: Eu preciso ser um administrador global para ver as entradas da atividade no portal do Azure ou obter dados por meio da API?**

**R:** Não, você também poderá acessar os dados do relatório por meio do portal ou por meio da API se for um **Leitor de Segurança** ou **Administrador de Segurança** do locatário. É claro que os **Administradores Globais** também terão acesso a esses dados.

---


## <a name="activity-logs"></a>Logs de atividades


**P: O que é a retenção de dados para logs de atividade (de entradas e de auditoria) no Portal do Azure?** 

**R:** Para obter mais informações, consulte [políticas de retenção de dados para relatórios do Azure ad](reference-reports-data-retention.md).

---

**P: quanto tempo leva até que eu possa ver os dados da atividade depois de concluir minha tarefa?**

**R:** Os logs de auditoria têm uma latência que varia de 15 minutos a uma hora. Logs de atividade de conexão podem levar de 15 minutos a até 2 horas para alguns registros.

---

**P: posso obter Microsoft 365 informações do log de atividades por meio do portal do Azure?**

**R:** Embora Microsoft 365 atividade e os logs de atividade do Azure AD compartilhem muitos recursos de diretório, se você quiser uma exibição completa dos logs de atividade de Microsoft 365, vá para o [centro de administração do Microsoft 365](https://admin.microsoft.com) para obter informações sobre o log de atividades do Office 365.

---

**P: quais APIs eu uso para obter informações sobre Microsoft 365 logs de atividades?**

**R:** Use as [APIs de gerenciamento de Microsoft 365](/office/office-365-management-api/office-365-management-apis-overview) para acessar os logs de atividade de Microsoft 365 por meio de uma API.

---

**P: Quantos registros posso baixar do Portal do Azure?**

**R:** É possível baixar até 5000 registros do portal do Azure. Os registros são classificados por *mais recentes* e, por padrão, você obterá 5000 registros mais recentes.

---

## <a name="risky-sign-ins"></a>Entradas de risco

**P: há uma detecção de risco na proteção de identidade, mas não estou vendo a entrada correspondente no relatório de entradas. Isso é esperado?**

**R:** Sim, o Identity Protection avalia o risco de todos os fluxos de autenticação, sejam eles interativos ou não. No entanto, o relatório de todas as entradas mostra apenas as entradas interativas.

---

**P: Como posso saber o motivo pelo qual uma entrada ou um usuário foi sinalizado como arriscado no Portal do Azure?**

**R:** Se você tiver uma assinatura **Azure ad Premium** , poderá saber mais sobre as detecções de risco subjacentes selecionando o usuário em **usuários sinalizados para risco** ou selecionando um registro no relatório de **entradas arriscadas** . Se você tiver uma assinatura **gratuita** ou **básica** , poderá exibir os relatórios dos usuários em risco e de entradas arriscadas, mas não poderá ver as informações de detecção de risco subjacente.

---

**P: Como os endereços IP são calculados no relatório de entradas e entradas arriscadas?**

**R:** Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é ainda mais complicado por fatores como provedores móveis e VPNs que emitem endereços IP de pools centrais, geralmente muito longe de onde o dispositivo cliente é realmente usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações. 

---

**P: o que significa a detecção de risco "entrar com risco adicional detectado"?**

**R:** Para fornecer insights sobre todos as entradas arriscadas no ambiente, "Entrar com risco adicional detectado" funciona como espaço reservado para entradas de detecções exclusivas dos assinantes do Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acesso Condicional

**P: quais são as novidades com esse recurso?**

**R:** Agora, os clientes podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de entradas. Os clientes podem examinar o status de acesso condicional e aprofundar-se nos detalhes das políticas aplicadas à entrada e ao resultado de cada política.

**P: Como faço para começar?**

**R:** para começar a usar:

* Navegue até o relatório de logins no [Portal do Azure](https://portal.azure.com).
* Clique no login que você deseja solucionar.
* Navegue até a guia **acesso condicional** . Aqui, você pode exibir todas as políticas que afetaram a entrada e o resultado de cada política. 
    
**P: quais são todos os valores possíveis para o status de acesso condicional?**

**R:** O status de acesso condicional pode ter os seguintes valores:

* **Não aplicado**: isso significa que não havia nenhuma política de acesso condicional com o usuário e o aplicativo no escopo. 
* **Êxito**: isso significa que houve uma política de acesso condicional com o usuário e o aplicativo no escopo e as políticas de acesso condicional foram atendidas com êxito. 
* **Falha**: a entrada satisfez a condição de usuário e aplicativo de pelo menos uma política de acesso condicional e os controles de concessão não são atendidos ou definidos para bloquear o acesso.
    
**P: quais são os valores possíveis para o resultado da política de acesso condicional?**

**R:** Uma política de acesso condicional pode ter os seguintes resultados:

* **Sucesso**: A política foi atendida com êxito.
* **Falha**: A política não foi atendida.
* **Não aplicado**: isso pode ocorrer porque não atenderam às condições da política.
* **Não habilitado**: isso é devido à política no estado desabilitado. 
    
**P: o nome da política no relatório de entrada não corresponde ao nome da política na CA. por quê?**

**R:** O nome da política no relatório de entrada é baseado no nome da política de acesso condicional no momento da entrada. Isso pode ser inconsistente com o nome da política na autoridade de certificação, se você tiver atualizado o nome da política mais tarde, ou seja, após o logon.

**P: minha entrada foi bloqueada devido a uma política de acesso condicional, mas o relatório de atividade de entrada mostra que a entrada foi bem-sucedida. Por?**

**R:** No momento, o relatório de entrada pode não mostrar resultados precisos para cenários do Exchange ActiveSync quando o acesso condicional é aplicado. Pode haver casos em que o resultado da entrada no relatório mostra uma entrada bem-sucedida, mas a entrada realmente falhou devido a uma política de acesso condicional.
