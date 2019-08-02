---
title: 'Azure Active Directory Domain Services: Domínios suspensos | Microsoft Docs'
description: Suspensão do domínio gerenciado e a exclusão
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 781a81589032c290cef7342e7210ee36f388b22a
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233980"
---
# <a name="understand-the-suspended-states-for-an-azure-active-directory-domain-services-managed-domain"></a>Entender os Estados suspensos para um Azure Active Directory Domain Services domínio gerenciado

Quando os Serviços de Domínio Active Directory do Azure (Azure AD DS) não conseguem atender a um domínio gerenciado por um longo período de tempo, ele coloca o domínio gerenciado em um estado suspenso. Este artigo explica porque os domínios gerenciados são suspensos e como corrigir um domínio suspenso.


## <a name="states-your-managed-domain-can-be-in"></a>Estados de seu domínio gerenciado podem estar em

![Linha do tempo do domínio suspenso](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

O gráfico anterior descreve os possíveis estados em que um domínio gerenciado do Azure AD DS pode estar.

### <a name="running-state"></a>"Estado"em execução
Um domínio gerenciado que está configurado corretamente e operacional regularmente é no estado **Executando**.

**O que esperar**
* A Microsoft regularmente pode monitorar a integridade do seu domínio gerenciado.
* Controladores de domínio para seu domínio gerenciado são corrigidos e atualizados regularmente.
* Alterações do Azure Active Directory são sincronizadas regularmente para seu domínio gerenciado.
* Backups regulares são feitos para seu domínio gerenciado.


### <a name="needs-attention-state"></a>Estado de "Atenção Necessária"
Um domínio gerenciado está no estado **Atenção necessária** se um ou mais problemas exigirem que um administrador tome uma ação. A página de integridade do seu domínio gerenciado lista um ou mais alertas nesse estado.

Por exemplo, se você configurou um NSG restritivo para sua rede virtual, talvez a Microsoft não consiga atualizar e monitorar seu domínio gerenciado. Essa configuração inválida aciona um alerta que coloca seu domínio gerenciado no estado "Atenção Necessária".

Cada alerta tem um conjunto de etapas de resolução. Alguns alertas são temporários e são resolvidos automaticamente pelo serviço. Você pode resolver alguns outros alertas, seguindo as instruções nas etapas de resolução correspondente para o alerta. Para alguns alertas críticos, você precisa entrar em contato com o suporte da Microsoft para obter uma solução.

Para obter mais informações, consulte [Como solucionar alertas em um domínio gerenciado](troubleshoot-alerts.md).

**O que esperar**

Em alguns casos (por exemplo, se você tiver uma configuração de rede inválida), os controladores de domínio do seu domínio gerenciado podem estar inacessíveis. Quando seu domínio gerenciado está no estado "Requer atenção", a Microsoft não garante que ele será ser monitorado, corrigido, atualizado ou backup regularmente.

* Seu domínio gerenciado está em um estado não íntegro e o monitoramento contínuo da integridade pode parar até que o alerta seja resolvido.
* Controladores de domínio para o seu domínio gerenciado não podem ser corrigidos ou atualizados.
* As alterações do Active Directory do Azure podem não estar sincronizadas com o seu domínio gerenciado.
* Backups para o seu domínio gerenciado podem ser obtidos, se possível.
* Se você resolver alertas que estão afetando seu domínio gerenciado, talvez seja possível restaurá-lo para o estado "Em execução".
* Alertas críticos são disparados para problemas de configuração em que a Microsoft não consegue alcançar seus controladores de domínio. Se esses alertas não forem resolvidos em 15 dias, o domínio gerenciado será colocado no estado "Suspenso".


### <a name="the-suspended-state"></a>O estado "Suspenso"
Um domínio gerenciado é colocado no estado **Suspenso** pelos seguintes motivos:

* Um ou mais alertas críticos ainda não foram resolvidos em 15 dias. Os alertas críticos podem ser causados por um erro de configuração que bloqueia o acesso aos recursos necessários ao Azure AD DS.
    * Por exemplo, o alerta [AADDS104: Erro de rede](alert-nsg.md) não foi resolvido por mais de 15 dias no domínio gerenciado.
* Há um problema de cobrança com sua assinatura do Azure ou sua assinatura do Azure expirou.

Os domínios gerenciados são suspensos quando a Microsoft não consegue gerenciar, monitorar, corrigir ou fazer backup do domínio continuamente.

**O que esperar**
* Controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
* O acesso LDAP seguro ao domínio gerenciado pela Internet (se estiver ativado) pára de funcionar.
* Você percebe falhas na autenticação no domínio gerenciado, no logon em máquinas virtuais ingressadas no domínio ou na conexão via LDAP / LDAPS.
* Não há mais são realizados backups para seu domínio gerenciado.
* A sincronização com o Azure Active Directory para.

Depois de resolver o alerta, seu domínio gerenciado entra no estado "Suspenso". Então você precisa entrar em contato com o suporte.
O suporte pode restaurar seu domínio gerenciado, mas somente se houver um backup com menos de 30 dias.

O domínio gerenciado permanece apenas em estado suspenso por 15 dias. Para recuperar seu domínio gerenciado, a Microsoft recomenda que você resolva alertas críticos imediatamente.


### <a name="deleted-state"></a>Estado "Excluído"
Um domínio gerenciado que permanece no estado "Suspenso" por 15 dias é **Excluído**.

**O que esperar**
* Todos os recursos e os backups para o domínio gerenciado serão excluídos.
* Você não pode restaurar o domínio gerenciado e precisa criar um novo domínio gerenciado para usar o Azure AD DS.
* Depois que ele for excluído, não são cobrados para o domínio gerenciado.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como saber se o seu domínio gerenciado está suspenso?
Você vê um [alerta](troubleshoot-alerts.md) na página Integridade do Azure AD DS no portal do Azure que declara que o domínio está suspenso. O estado do domínio também mostra "Suspenso".


## <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso
Para restaurar um domínio que esteja no estado "Suspenso", siga os seguintes passos:

1. Vá até a página [Serviços de Domínio do Active Directory do Azure](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Selecione o domínio gerenciado.
3. No painel esquerdo, selecione **integridade**.
4. Selecione o alerta. A ID do alerta será AADDS503 ou AADDS504, dependendo da causa da suspensão.
5. Selecione o link de resolução fornecido no alerta. Em seguida, siga as etapas para resolver o alerta.

Seu domínio gerenciado só pode ser restaurado para a data do último backup. A data de seu último backup é exibida na página de integridade do seu domínio gerenciado. Todas as alterações que ocorreram depois do último backup não serão restauradas. Backups para um domínio gerenciado são armazenados por até 30 dias. Backups mais antigos do que 30 dias serão excluídos.


## <a name="next-steps"></a>Próximas etapas
- [Resolver alertas para seu domínio gerenciado](troubleshoot-alerts.md)
- [Leia mais sobre o Azure Active Directory Domain Services](overview.md)
- [Entrar em contato com a equipe do produto](contact-us.md)

## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](contact-us.md).
