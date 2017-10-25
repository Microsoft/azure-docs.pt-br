---
title: "Perguntas frequentes sobre relatórios do Azure Active Directory | Microsoft Docs"
description: "Perguntas frequentes sobre relatórios do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Perguntas frequentes sobre relatórios do Azure Active Directory

Este artigo inclui respostas para FAQs (perguntas frequentes) sobre os relatórios do Azure Active Directory.  
Para obter mais detalhes, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md). 

**P: O que é a retenção de dados para logs de atividade (de entradas e de auditoria) no Portal do Azure?** 

**R:** Fornecemos 7 dias de dados para nossos clientes com acesso gratuito e, alternando para uma licença Azure AD Premium 1 ou Premium 2, você pode acessar dados por até 30 dias. Para obter mais detalhes sobre a retenção, consulte [Políticas de retenção de relatório do Azure Active Directory](active-directory-reporting-retention.md)

--- 

**P: Quanto tempo leva até que eu possa ver os dados de atividade depois que concluir minha tarefa?**

**R:** Logs de atividade de auditoria têm uma latência variando de 15 minutos a uma hora. Logs de atividade de entrada têm uma latência variando de 15 minutos para a maioria dos registros até 2 horas para alguns registros.

---

**P: É necessário ser um administrador global para ver a atividade de logs no Portal do Azure ou para obter dados por meio da API?**

**R:** Não. Você pode ser um **Leitor de segurança**, um **Administrador de segurança** ou um **Administrador Global** para ver dados de relatório no Portal do Azure ou acessando-os por meio da API.

---

**P: Posso obter informações do log de atividades do Office 365 por meio do Portal do Azure?**

**R:** Embora os logs de atividades do Office 365 e de atividades do Azure AD compartilhem muitos dos recursos de diretório, se você quiser uma exibição completa dos logs de atividades do Office 365, vá para o Centro de administração do Office 365 para obter informações de log de atividades do Office 365.

---


**P: Quais APIs devo usar para obter informações sobre logs de atividades do Office 365?**

**R:** Use as APIs de gerenciamento do Office 365 para acessar os [logs de atividades do Office 365 por meio de uma API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**P: Quantos registros posso baixar do Portal do Azure?**

**R:** Você pode baixar até 120 mil registros do Portal do Azure. Os registros são classificados pelos *mais recentes* e, por padrão, você obterá os 120 mil registros mais recentes. 

---

**P: Quantos registros posso consultar por meio da API de atividades?**

**R:** Você pode consultar até 1 milhão de registros (se você não usar o operador top, que classifica o registro pelo mais recente). Se usar o operador "top", você poderá consultar até 500 mil registros. Você pode encontrar consultas de exemplo sobre como usar a API [aqui](active-directory-reporting-api-getting-started.md).

---

**P: Como obtenho uma licença premium?**

**A:** Consulte [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md) para obter uma resposta para essa pergunta.

---

**P: Em quanto tempo deverei ver dados de atividades depois de obter uma licença premium?**

**R:** Se você já tiver dados de atividades como uma licença gratuita, você poderá ver os mesmos dados. Se você não tiver nenhum dado, isso levará um ou dois dias.

---

**P: Eu vejo dados do último mês depois de obter uma licença premium do Azure AD?**

**R:** Se você mudou recentemente para uma versão Premium (incluindo uma versão de avaliação), você poderá inicialmente ver dados de até 7 dias. Quando os dados acumularem, você verá dados de até 30 dias.

---

**P: Há um evento de risco no Identity Protection, mas não estou vendo a entrada correspondente em todas as entradas. Isso é esperado?**

**R:** Sim, o Identity Protection avalia o risco de todos os fluxos de autenticação, sejam eles interativos ou não. No entanto, o relatório de todas as entradas mostra apenas as entradas interativas.

---

**P: Como posso baixar o relatório "Usuários sinalizados para risco" no portal do Azure?**

**R:**: A opção de baixar o relatório *Usuários sinalizados para risco* será adicionada em breve.

---

**P: Como posso saber o motivo pelo qual uma entrada ou um usuário foi sinalizado como arriscado no Portal do Azure?**

**R:** Clientes do Premium Edition podem saber mais sobre os eventos de risco subjacentes clicando no usuário em "Usuários sinalizados para risco" ou clicando nas "Entradas arriscadas". Clientes das edições Gratuita e Basic podem ver as entradas e os usuários em risco sem as informações de evento de risco subjacente.

---

**P: Como os endereços IP são calculados no relatório de entradas e entradas arriscadas?**

**R:** Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. Isso é complicado por fatores como provedores móveis e VPNs que emitem endereços IP de pools centrais, frequentemente muito distantes do local no qual o dispositivo do cliente de fato é usado. Devido a esses fatores, a conversão de endereços IP em um local físico é um esforço baseado em rastreamentos, dados de registro, pesquisas inversas e outras informações. 

---
