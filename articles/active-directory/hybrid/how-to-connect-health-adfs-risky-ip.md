---
title: Azure AD Connect Health com AD FS relatório de IP arriscado | Microsoft Docs
description: Descreve o Azure AD Connect Health AD FS relatório de IP arriscado.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0b76d2f943f254eb06208e2c190bae4d4088030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746063"
---
# <a name="risky-ip-report-public-preview"></a>Relatório de IP arriscado (visualização pública)
AD FS clientes podem expor pontos de extremidade de autenticação de senha à Internet para fornecer serviços de autenticação para que os usuários finais acessem aplicativos SaaS, como Microsoft 365. Nesse caso, é possível que um ator mal-intencionado tente fazer logons em seu sistema de AD FS adivinhando a senha do usuário final e obtendo acesso aos recursos do aplicativo. O AD FS fornece a funcionalidade de bloqueio de conta de extranet para evitar esses tipos de ataque desde a sua versão no Windows Server 2012 R2. Se você estiver usando uma versão inferior, recomendamos fortemente que atualize seu sistema do AD FS para o Windows Server 2016. <br />

Além disso, é possível que um único endereço IP tente vários logons em relação a vários usuários. Nesses casos, o número de tentativas por usuário pode estar abaixo do limite para a proteção de bloqueio de conta no AD FS. O Azure AD Connect Health agora fornece o "Relatório IP arriscado", que detecta essa condição e notifica os administradores quando isso ocorre. Estes são os principais benefícios do relatório: 
- Detecção de endereços IP que excedem um limite de logons com falha com base em senha
- Dá suporte a logons com falha devido a senha incorreta ou a estado de bloqueio de extranet
- Notificação por email para alertar os administradores assim que isso ocorrer com configurações de email personalizáveis
- Configurações de limite personalizáveis que correspondem à política de segurança de uma organização
- Relatórios que podem ser baixados para análise offline e integração com outros sistemas por meio de automação

> [!NOTE]
> Para usar esse relatório, você deve habilitar a auditoria do AD FS. Para obter mais informações, consulte [Habilitar a auditoria do AD FS](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Para acessar a versão prévia, uma permissão de Administrador Global ou [Leitor de segurança](../../role-based-access-control/built-in-roles.md#security-reader) é necessária.  
>

## <a name="what-is-in-the-report"></a>O que há no relatório?
Os endereços IP do cliente de atividade de entrada com falha são agregados por meio de servidores proxy de aplicativo Web. Cada item no Relatório de IP arriscado mostra informações agregadas sobre atividades de entrada do AD FS com falha que excedem o limite designado. Ele fornece as seguintes informações: ![ captura de tela que mostra um relatório IP arriscado com cabeçalhos de coluna realçado.](./media/how-to-connect-health-adfs/report4a.png)

| Item do relatório | Descrição |
| ------- | ----------- |
| Carimbo de Data/Hora | Mostra o carimbo de data/hora com base na hora local do portal do Azure quando a janela de tempo de detecção é iniciada.<br /> Todos os eventos diários são gerados à meia-noite, horário UTC. <br />Os eventos por hora têm o carimbo de data/hora arredondado para o início da hora. Você pode encontrar a hora de início da primeira atividade de "firstAuditTimestamp" no arquivo exportado. |
| Tipo de gatilho | Mostra o tipo de janela de tempo de detecção. Os tipos de gatilho de agregação são por hora ou por dia. Isso é útil para detectar um ataque de força bruta de alta frequência versus um ataque lento, em que o número de tentativas é distribuído ao longo do dia. |
| Endereço IP | O único endereço IP arriscado que tinha senha incorreta ou atividades de entrada do bloqueio de extranet. Pode ser um endereço IPv4 ou IPv6. |
| Contagem de erro de senha inválida | Ocorreu a contagem de erro de senha incorreta no endereço IP durante a janela de tempo de detecção. Os erros de senha incorreta podem ocorrer várias vezes para determinados usuários. Observe que isso não inclui tentativas com falha devido a senhas expiradas. |
| Contagem de erro de bloqueio de extranet | Ocorreu a contagem de erro de bloqueio de extranet no endereço IP durante a janela de tempo de detecção. Os erros de bloqueio de Extranet podem ocorrer várias vezes para determinados usuários. Isso só será visto se o Bloqueio de Extranet for configurado no AD FS (versões 2012R2 ou superior). <b>Observação</b> Recomendamos ativar esse recurso se você permite logons extranet com senhas. |
| Usuários exclusivos tentados | Contagem de tentativas de contas de usuário exclusivas no endereço IP durante a janela de tempo de detecção. Isso fornece um mecanismo para diferenciar um padrão de ataque de usuário único versus um padrão de ataque de multiusuário.  |

Por exemplo, o item de relatório abaixo indica, da janela de 18h às 19h em 28/2/2018, que o endereço IP <i>104.2XX.2XX.9</i> não teve erros de senha incorreta e 284 erros de bloqueio de extranet. 14 usuários exclusivos foram afetados dentro dos critérios. O evento de atividade excedeu o limite horário definido para o relatório. 

![Captura de tela que mostra um exemplo de uma entrada de relatório IP arriscada.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Apenas atividades ultrapassando o limite designado serão exibidas na lista de relatórios. 
> - Esse relatório pode controlar um histórico de 30 dias.
> - Esse relatório de alerta não mostra endereços IP privado ou endereços IP do Exchange. Eles ainda são incluídos na lista de exportação. 
>

![Captura de tela que mostra o relatório de IP arriscado com as opções "baixar", "configurações de notificação" e "configurações de limite" realçadas.](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>Endereços IP do balanceador de carga na lista
Atividades de entrada com falha agregadas ao balanceador de carga e limite de alerta atingido. Se você está vendo endereços IP do balanceador de carga, é muito provável que o seu balanceador de carga externo não esteja enviando o endereço IP do cliente ao passar a solicitação para o servidor proxy do aplicativo Web. Configure o balanceador de carga corretamente para encaminhar o endereço IP do cliente. 

## <a name="download-risky-ip-report"></a>Baixar relatório de IP arriscado 
Usando a função **Baixar**, a lista de endereços IP arriscados inteira nos últimos 30 dias pode ser exportada do Portal do Connect Health O resultado de exportação incluirá todas as atividades de entrada do AD FS com falha em cada janela de tempo de detecção, para que você possa personalizar a filtragem após a exportação. Além de agregações realçadas no portal, o resultado da exportação também mostra mais detalhes sobre as atividades de entrada com falha por endereço IP:

|  Item do relatório  |  Descrição  | 
| ------- | ----------- | 
| firstAuditTimestamp | Mostra o primeiro carimbo de data/hora de quando as atividades com falha começaram durante a janela de tempo de detecção.  | 
| lastAuditTimestamp | Mostra o último carimbo de data/hora de quando as atividades com falha terminaram durante a janela de tempo de detecção.  | 
| attemptCountThresholdIsExceeded | O sinalizador que indica se as atividades atuais estão excedendo o limite de alerta.  | 
| isWhitelistedIpAddress | O sinalizador que indica se o endereço IP foi filtrado de alertas e relatórios. Endereços IP privados (<i>10.x.x.x, 172.x.x.x & 192.168.x.x</i>) e endereços IP do Exchange são filtrados e marcados como Verdadeiros. Se você está vendo intervalos de endereços IP privados, é muito provável que o balanceador de carga externo não esteja enviando o endereço IP do cliente ao passar a solicitação para o servidor proxy do aplicativo Web.  | 

## <a name="configure-notification-settings"></a>Definir as configurações de notificação
Os contatos de Admin do relatório podem ser atualizados por meio das **Configurações de Notificação**. Por padrão, a notificação de email de alerta de IP arriscado está desativada. Você pode habilitar a notificação alternando o botão em "Obter notificações por email para endereços IP que excederem o limite de atividades com falha do relatório" Como as configurações de notificação de alerta genérico no Connect Health, ele permite que você personalize a lista de destinatários designados para a notificação sobre o relatório de IPs arriscados aqui. Você também pode notificar todos os administradores globais ao fazer a alteração. 

## <a name="configure-threshold-settings"></a>Definir configurações de limite
O limite de alerta pode ser atualizado com as Configurações de Limite. Para começar, o sistema tem um limite definido por padrão. Os valores padrão são fornecidos abaixo. Há quatro categorias nas configurações de limite do relatório de IP arriscado:

![Portal do Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Item de limite | Descrição |
| --- | --- |
| (U/P má + Bloqueio de Extranet) / Dia  | Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de senha incorreta mais a contagem de bloqueio de extranet o exceder, por **dia**. O valor padrão é 100.|
| (U/P má + Bloqueio de Extranet) / Hora | Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de senha incorreta mais a contagem de bloqueio de extranet o exceder, por **hora**. O valor padrão é 50.|
| Bloqueio de Extranet / Dia | Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de bloqueio de extranet o exceder, por **dia**. O valor padrão é 50.|
| Bloqueio de Extranet / Hora| Configuração de limite para relatar a atividade e disparar a notificação de alerta quando a contagem de bloqueio de extranet o exceder, por **hora**. O valor padrão é 25|

> [!NOTE]
> - A alteração do limite do relatório será aplicada após uma hora a contar da alteração da configuração. 
> - Os itens relatados existentes não serão afetados pela alteração de limite. 
> - Recomendamos que você analise o número de eventos vistos em seu ambiente e ajuste o limite adequadamente. 
>
>

## <a name="faq"></a>Perguntas frequentes
**Por que estou vendo intervalos de endereços IP privados no relatório?**  <br />
Endereços IP privados (<i>10. x. x. x, 172. x. x & 192.168. x. x</i>) e endereços IP do Exchange são filtrados e marcados como true na lista de IP aprovado. Se você está vendo intervalos de endereços IP privados, é muito provável que o balanceador de carga externo não esteja enviando o endereço IP do cliente ao passar a solicitação para o servidor proxy do aplicativo Web.

**Por que estou vendo endereços IP do balanceador de carga no relatório?**  <br />
Se você está vendo endereços IP do balanceador de carga, é muito provável que o seu balanceador de carga externo não esteja enviando o endereço IP do cliente ao passar a solicitação para o servidor proxy do aplicativo Web. Configure o balanceador de carga corretamente para encaminhar o endereço IP do cliente. 

**O que fazer para bloquear o endereço IP?**  <br />
Você deve adicionar o endereço IP mal-intencionado ao firewall ou bloqueá-lo no Exchange.   <br />

**Por que não vejo todos os itens no relatório?** <br />
- Atividades de entrada com falha não excedem as configurações de limite.
- Verifique se nenhum alerta “Serviço Health desatualizado” está ativo na sua lista de servidores AD FS.  Leia mais sobre [como solucionar esse alerta](how-to-connect-health-data-freshness.md).
- As auditorias não estão habilitadas em farms de servidores do AD FS.

**Por que não estou vendo nenhum acesso ao relatório?**  <br />
É necessária a permissão de Administrador Global ou [Leitor de segurança](../../role-based-access-control/built-in-roles.md#security-reader). Entre em contato com o administrador global para obter acesso.


## <a name="next-steps"></a>Próximas etapas
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
