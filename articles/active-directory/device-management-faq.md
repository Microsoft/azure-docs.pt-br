---
title: Perguntas frequentes sobre o gerenciamento de dispositivo do Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b37033e0c96e01c73bef15af1b85116f404c354
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-device-management-faq"></a>Perguntas frequentes sobre o gerenciamento de dispositivos do Azure Active Directory



**P: Como posso registrar um dispositivo macOS?**

**R:** Para registrar um dispositivo macOS:

1.  [Criar uma política de conformidade](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definir uma política de acesso condicional para dispositivos macOS](active-directory-conditional-access-azure-portal.md) 

**Comentários:**

- Os usuários que estão incluídos em sua política de acesso condicional precisam de uma [versão com suporte do Office para macOS](active-directory-conditional-access-technical-reference.md#client-apps-condition) para acessar os recursos. 

- Durante a primeira tentativa de acesso, os usuários são solicitados a registrar o dispositivo usando o portal da empresa.

---

**P: Registrei o dispositivo recentemente. Por que não consigo ver o dispositivo em minhas informações de usuário no Portal do Azure?**

**R:** Dispositivos Windows 10 que ingressaram no domínio com o registro de dispositivo automático não aparecem nas informações de usuário.
Você precisa usar o PowerShell para ver todos os dispositivos. 

Apenas os seguintes dispositivos são listados nas informações de usuário:

- Todos os dispositivos pessoais que não são ingressados pela empresa 
- Todos os não Windows 10/Windows Server 2016 
- Todos os dispositivos não Windows 

---

**P: Por que não posso ver todos os dispositivos registrados no Azure Active Directory no Portal do Azure?** 

**R:** Atualmente, não há nenhuma maneira de ver todos os dispositivos registrados no Portal do Azure. Você pode usar o Azure PowerShell para localizar todos os dispositivos. Para obter mais detalhes, consulte o cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0).

--- 

**P: Como saber qual é o estado de registro do dispositivo do cliente?**

**R:** O estado de registro do dispositivo depende de:

- O que é o dispositivo
- Como ele foi registrado 
- Todos os detalhes relacionados a ele. 
 

---

**P: Por que um dispositivo que eu excluí no Portal do Azure ou usando o Windows PowerShell ainda está listado como registrado?**

**R:** Esse comportamento é intencional. O dispositivo não terá acesso aos recursos na nuvem. Se você quiser remover o dispositivo e registre novamente, uma ação manual deve ser a ser executada no dispositivo. 

Para Windows 10 e Windows Server 2016 que estão ingressados pelo domínio do AD local:

1.  Abra o prompt de comando como administrador.

2.  Digite `dsregcmd.exe /debug /leave`

3.  Sair e entrar para disparar a tarefa agendada que registra o dispositivo novamente. 

Para outras plataformas Windows que estão ingressadas pelo domínio do AD local:

1.  Abra o prompt de comando como administrador.
2.  Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Digite `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**P: Por que vejo entradas de dispositivo duplicadas no Portal do Azure?**

**R:**

-   Para Windows 10 e Windows Server 2016, se houver tentativas repetidas de cancelar o ingresso e ingressar novamente o mesmo dispositivo, poderá haver entradas duplicadas. 

-   Se você tiver usado Adicionar Conta Corporativa ou de Estudante, cada usuário do Windows que usar Adicionar Conta Corporativa ou de Estudante criará um novo registro do dispositivo com o mesmo nome do dispositivo.

-   Outras plataformas Windows que são ingressadas pelo domínio do AD local usando o registro automático criarão um novo registro de dispositivo com o mesmo nome do dispositivo para cada usuário de domínio que faça logon no dispositivo. 

-   Um computador AADJ que foi apagado, reinstalado e reingressado com o mesmo nome aparecerá como outro registro com o mesmo nome do dispositivo.

---

**P: Por que um usuário ainda pode acessar recursos de um dispositivo que eu desabilitei no Portal do Azure?**

**R:** Pode demorar até uma hora para uma revogação ser aplicada.

>[!Note] 
>Para dispositivos perdidos, recomendamos apagar o dispositivo para garantir que os usuários não possam acessar o dispositivo. Para obter mais detalhes, consulte [Registrar dispositivos para gerenciamento no Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**P: Por que meus usuários veem "Você não pode chegar lá daqui"?**

**R:** Se você tiver configurado certas regras de acesso condicional para exigir um estado de dispositivo específico e o dispositivo não atender aos critérios, os usuários serão bloqueados e verão esta mensagem. Avalie as regras e verifique se o dispositivo é capaz de atender aos critérios para evitar esta mensagem.

---


**P: Posso ver o registro de dispositivo nas informações do usuário no Portal do Azure e posso ver o estado como registrado no cliente. Estou configurado corretamente para usar o acesso condicional?**

**R:** O registro do dispositivo (deviceID) e o estado no Portal do Azure devem corresponder ao cliente e atender a todos os critérios de avaliação para acesso condicional. Para obter mais informações, consulte [Introdução ao registro de dispositivos do Azure Active Directory](active-directory-device-registration.md).

---

**P: Por que recebo uma mensagem de "nome de usuário ou senha está incorreta" para um dispositivo que acabei de ingressar no Azure AD?**

**R:** As razões comuns para esse cenário são:

- Suas credenciais de usuário não são mais válidas.

- O computador não pode se comunicar com o Azure Active Directory. Verifique se há problemas de conectividade de rede.

- Os pré-requisitos de Ingresso no Azure AD não foram atendidos. Certifique-se de ter seguido as etapas de [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Ingresso do Azure Active Directory](active-directory-azureadjoin-overview.md).  

- Os logons federados requerem que o servidor de federação dê suporte a um ponto de extremidade WS-Trust ativo. 

---

**P: Por que vejo a caixa de diálogo "Ocorreu um erro" quando tento ingressar meu computador?**

**R:** Esse é um resultado da configuração de registro do Azure Active Directory como Intune. Para obter mais detalhes, consulte [Configurar o gerenciamento do dispositivo Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**P: Por que minha tentativa de ingressar um computador falhou embora eu não tenha obtido nenhuma informação de erro?**

**R:** Uma causa provável é que o usuário está conectado ao dispositivo usando a conta de administrador interno. Crie uma conta local diferente antes de usar o Ingresso do Azure Active Directory para concluir a configuração. 

---

**P: onde posso encontrar instruções para a configuração de registro automático do dispositivo?**

**R:** Para obter as instruções detalhadas, confira [Como configurar o registro automático de dispositivos ingressados no domínio do Windows com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**P: Onde posso encontrar informações de solução de problemas sobre o registro de dispositivo automático?**

**R:** Para encontrar informações de solução de problemas, consulte:

- [Solução de problemas de registro automático de computadores ingressados no domínio do Azure AD – Windows 10 e Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Solução de problemas com o registro automático de computadores ingressados no domínio do Azure AD para clientes de nível inferior do Windows](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

