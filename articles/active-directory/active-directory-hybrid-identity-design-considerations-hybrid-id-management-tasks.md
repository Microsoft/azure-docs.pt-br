---
title: "Considerações de design da identidade híbrida do Azure Active Directory - determinar as tarefas de gerenciamento da identidade híbrida | Microsoft Docs"
description: "Com o controle de acesso condicional, o Active Directory do Azure verifica as condições específicas escolhidas para autenticação do usuário, antes de permitir o acesso ao aplicativo. Quando essas condições forem atendidas, o usuário é autenticado e autorizado a acessar o aplicativo."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 921c8391fc18eca35d10c3ade158a98ae88df397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plano para o ciclo de vida de identidade híbrida
Identidade é um dos pilares da mobilidade da sua empresa e da estratégia de acesso de aplicativo. Se você estiver entrando em seu dispositivo móvel ou em um aplicativo SaaS, sua identidade é a chave para ganhar acesso a tudo. Em seu nível mais alto, uma solução de gerenciamento de identidade abrange a unificação e a sincronização entre os repositórios de identidades, o que inclui automação e centralização do processo de provisionamento de recursos. A solução de identidade deve ser uma identidade centralizada no local e na nuvem e também usar alguma forma de federação de identidades para manter a autenticação centralizada e compartilhar e colaborar de forma segura com empresas e usuários externos. Os recursos variam de sistemas operacionais e aplicativos a pessoas associada ou pertencentes a uma organização. A estrutura organizacional pode ser alterada para acomodar as políticas e procedimentos de provisionamento.

Também é importante ter uma solução de identidade voltada para capacitar seus usuários, fornecendo experiências de autoatendimento para se manterem produtivos. A solução de identidade é mais eficiente se ela permite logon único para usuários em todos os recursos que precisam acessar. Os administradores em todos os níveis podem usar procedimentos padronizados para gerenciar as credenciais do usuário. Alguns níveis de administração podem ser reduzidos ou eliminados, dependendo da amplitude da solução de gerenciamento de provisionamento. Além disso, você pode distribuir recursos de administração, manualmente ou automaticamente, entre várias organizações. Por exemplo, um administrador de domínio pode servir somente a pessoas e recursos nesse domínio. Esse usuário pode executar tarefas administrativas e de configuração, mas não está autorizado a executar tarefas de configuração, como a criação de fluxos de trabalho.

## <a name="determine-hybrid-identity-management-tasks"></a>Determinar tarefas de gerenciamento de identidade híbrida
A distribuição de tarefas administrativas em sua organização melhora a precisão e a eficiência da administração e melhora o equilíbrio da carga de trabalho de uma organização. A seguir estão os pontos que definem um sistema de gerenciamento de identidade sólido.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

Para definir as tarefas de gerenciamento de identidade híbrida, você deve entender algumas características essenciais da organização que adotará identidade híbrida. É importante entender os repositórios atuais que estão sendo usados como fontes de identidade. Conhecendo os principais elementos, você terá os requisitos básicos e, baseado nisso, você precisa fazer perguntas mais sintonizadas que levarão a uma melhor decisão de design para a solução de identidade.  

Ao definir esses requisitos, verifique se pelo menos as seguintes perguntas são respondidas

* Opções de provisionamento: 
  
  * A solução de identidade híbrida dá suporte a um sistema de provisionamento e gerenciamento de acesso à conta sólido?
  * Como os usuários, grupos e senhas serão gerenciados?
  * O gerenciamento do ciclo de vida de identidade é dinâmico? 
    * Quanto tempo dura a suspensão de conta de atualizações de senha?
* Gerenciamento de licenças: 
  
  * A solução de identidade híbrida lida com o gerenciamento de licenças?
    * Em caso afirmativo, quais recursos estão disponíveis?
* A solução lida com gerenciamento de licença baseada em grupo? 
  
      - Em caso afirmativo, é possível atribuir um grupo de segurança a ele? 
       - Em caso afirmativo, o diretório de nuvem atribuirá automaticamente licenças a todos os membros do grupo? 
        - O que acontece se um usuário é posteriormente adicionado ou removido do grupo: uma licença será automaticamente atribuída ou removida conforme o caso? 
* Integração com outros provedores de identidade de terceiros:
* Essa solução híbrida pode ser integrada a provedores de identidade de terceiros para implementar o logon único?
* É possível unificar todos os provedores de identidade diferentes em um sistema de identidade coeso?
* Se sim, como, quais são elas e quais recursos estão disponíveis?

## <a name="synchronization-management"></a>Gerenciamento de sincronização
Um dos objetivos de um gerenciador de identidades é ser capaz de buscar todos os provedores de identidade e mantê-los sincronizados. Você mantém os dados sincronizados baseado em um provedor de identidade mestre autoritativo. Em um cenário de identidade híbrida, com um modelo de gerenciamento sincronizado, você gerencia todas as identidades de usuários e de dispositivos em um servidor local, além de sincronizar contas e, opcionalmente, senhas para a nuvem. O usuário insere a mesma senha local como é feito na nuvem e no logon, e a senha é verificada pela solução de identidade. Esse modelo usa uma ferramenta de sincronização de diretório.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Para projetar adequadamente a sincronização de sua solução da identidade híbrida, verifique se as seguintes perguntas são respondidas: •   Quais soluções de sincronização estão disponíveis para a solução da identidade híbrida?
•   Quais recursos de logon único estão disponíveis?
•   Quais são as opções da federação de identidades entre B2B e B2C?

## <a name="next-steps"></a>Próximas etapas
[Determinar a estratégia de adoção do gerenciamento de identidade híbrida](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Consulte também
[Visão geral sobre as considerações de design](active-directory-hybrid-identity-design-considerations-overview.md)

