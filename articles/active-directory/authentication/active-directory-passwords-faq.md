---
title: Perguntas frequentes de redefinição de senha – Azure Active Directory
description: Perguntas frequentes sobre o autoatendimento de redefinição de senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f98b72d9b5e59155bb7215181fd1ecd336126ee6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440964"
---
# <a name="password-management-frequently-asked-questions"></a>Perguntas frequentes sobre gerenciamento de senhas

Veja abaixo algumas perguntas frequentes (FAQ) sobre tudo relativo à redefinição de senhas.

Caso você tenha uma pergunta geral sobre o Azure Active Directory (Azure AD) e o autoatendimento de redefinição de senha (SSPR) que não foi respondida aqui, peça ajuda à comunidade nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de TI.

Esta seção de perguntas frequentes é dividida nas seguintes seções:

* [Perguntas sobre o registro de redefinição de senha](#password-reset-registration)
* [Perguntas sobre a redefinição de senha](#password-reset)
* [Perguntas sobre alteração de senha](#password-change)
* [Perguntas sobre relatórios de gerenciamento de senha](#password-management-reports)
* [Perguntas sobre o write-back de senha](#password-writeback)

## <a name="password-reset-registration"></a>Registro de redefinição de senha

* **P:  Os usuários podem registrar seus próprios dados de redefinição de senha?**

  > **R:** Sim. Desde que a redefinição de senha esteja habilitada e eles sejam licenciados, os usuários podem ir para o portal de registro de redefinição de senha (https://aka.ms/ssprsetup) ) para registrar as informações de autenticação. Os usuários também podem registrar por meio do painel de acesso (https://myapps.microsoft.com) ). Para registrar-se por meio do Painel de Acesso, eles precisam selecionar a imagem do perfil, selecionar **Perfil** e, em seguida, selecionar a opção **Registrar-se para redefinição de senha**.
  >
  >
* **P:  Se eu habilitar a redefinição de senha para um grupo e, em seguida, decidir habilitá-la para todos, os usuários deverão se registrar novamente?**

  > **R:** Não. Os usuários que possuem dados de autenticação populados não devem se registrar novamente.
  >
  >
* **P:  Posso definir os dados da redefinição de senha em nome dos usuários?**

  > **R:** Sim, você pode fazer isso com o Azure AD Connect, PowerShell, o [portal do Azure](https://portal.azure.com), ou o [Centro de administração do Microsoft 365](https://admin.microsoft.com). Para obter mais informações, consulte [Dados usados pelo autoatendimento de redefinição de senha do Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **P:  Posso sincronizar os dados de perguntas de segurança localmente?**

  > **R:** Não, atualmente, isso não é possível.
  >
  >
* **P:  Os usuários podem registrar dados de modo que outros usuários não possam vê-los?**

  > **R:** Sim. Quando os usuários registram dados usando o portal de registro de redefinição de senha, os dados são salvos em campos de autenticação privada visíveis apenas por administradores globais e pelo usuário.
  >
  >
* **P:  Os usuários precisam ser registrados antes de usar a redefinição de senha?**

  > **R:** Não. Se você definir informações de autenticação suficientes em nome deles, os usuários não precisarão se registrar. A redefinição de senha funcionará desde que você tenha formatado corretamente os dados armazenados nos campos apropriados no diretório.
  >
  >
* **P:  Posso sincronizar ou definir os campos de telefone de autenticação, email de autenticação ou telefone de autenticação alternativo em nome dos usuários?**

  > **R:** Os campos que podem ser definidos por um Administrador Global são definidos no artigo [Requisitos de dados da SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **P:  Como o portal de registro determina quais opções mostrar aos usuários?**

  > **R:** O portal de registro de redefinição de senha só mostra as opções que você habilitou para os usuários. Essas opções estão localizadas na seção **Política de Redefinição de Senha de Usuário** da guia **Configurar** do diretório. Por exemplo, se você não habilitar perguntas de segurança, os usuários não poderão se registrar nessa opção.
  >
  >
* **P:  Quando um usuário é considerado registrado?**

  > **R:** Um usuário é considerado registrado na SSPR quando ele registrou, pelo menos, o **Número de métodos obrigatórios para a redefinição** que você definiu no [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Redefinição de senha

* **P:  Os usuários são impedidos de realizar várias tentativas para redefinir uma senha em um curto período?**

  > **R:** Sim, existem recursos de segurança internos na redefinição de senha para proteção contra uso indevido. 
  >
  > Os usuários poderão realizar apenas cinco tentativas de redefinição de senha dentro de um período de 24 horas, antes de serem bloqueados por 24 horas. 
  >
  > Os usuários poderão tentar validar um número de telefone, enviar um SMS ou validar questões de segurança e responder apenas cinco vezes em uma hora, antes de serem bloqueados por 24 horas. 
  >
  > Os usuários poderão enviar um email no máximo 10 vezes dentro de um período de 10 minutos, antes de serem bloqueados por 24 horas.
  >
  > Os contadores serão redefinidos quando o usuário redefinir sua senha.
  >
  >
* **P:  Quanto tempo devo aguardar para receber um email, um SMS ou uma chamada telefônica da redefinição de senha?**

  > **R:** Emails, mensagens SMS e chamadas telefônicas devem ser recebidos em menos de um minuto. O caso comum é de 5 a 20 segundos.
  > Se você não receber a notificação nesse período:
  > * Verifique a pasta Lixo Eletrônico.
  > * Verifique se o número ou o email de contato é aquele que você espera.
  > * Verifique se os dados de autenticação no diretório estão corretamente formatado, por exemplo, + 1 4255551234 ou *usuário\@contoso.com*. 
* **P:  Quais idiomas são compatíveis com a redefinição de senha?**

  > **R:** A interface do usuário, as mensagens SMS e as chamadas de voz da redefinição de senha estão localizadas nos mesmos idiomas compatíveis com o Office 365.
  >
  >
* **P:  Quais partes da experiência de redefinição de senha são personalizadas quando defino os itens de identidade visual organizacional na guia Configurar do meu diretório?**

  > **R:** O portal da redefinição de senha mostra o logotipo de sua organização e também permite que você configure o link "Contate o administrador" para que ele aponte para uma URL ou um email personalizado. Todos os emails enviados pela redefinição de senha incluem o logotipo de sua organização, as cores e o nome no corpo do email e são personalizados nas configurações desse nome específico.
  >
  >
* **P:  Como posso instruir os usuários sobre o local que eles podem acessar para redefinir suas senhas?**

  > **R:** Experimente usar algumas das sugestões descritas no artigo [Implantação da SSPR](howto-sspr-deployment.md#communications-plan).
  >
  >
* **P:  Posso usar essa página em um dispositivo móvel?**

  > **R:** Sim, essa página funciona em dispositivos móveis.
  >
  >
* **P:  Há suporte para desbloqueio de contas locais do Active Directory quando os usuários redefinem suas senhas?**

  > **R:** Sim. Quando um usuário redefine a senha, se o write-back de senha foi implantado usando o Azure AD Connect, a conta desse usuário é desbloqueada automaticamente quando ele redefine a senha.
  >
  >
* **P:  Como posso integrar a redefinição de senha diretamente à experiência de entrada da área de trabalho do usuário?**

  > **R:** Se você for um cliente do Azure AD Premium, poderá instalar o Microsoft Identity Manager sem custo adicional e implantar a solução de redefinição de senha local.
  >
  >
* **P:  Posso definir perguntas de segurança diferentes para diferentes localidades?**

  > **R:** Não, atualmente, isso não é possível.
  >
  >
* **P:  Quantas perguntas posso configurar para a opção de autenticação com perguntas de segurança?**

  > **R:** Você pode configurar até 20 perguntas de segurança personalizadas no [portal do Azure](https://portal.azure.com).
  >
  >
* **P:  Qual o tamanho máximo das perguntas de segurança?**

  > **R:** As perguntas de segurança podem ter entre 3 e 200 caracteres.
  >
  >
* **P:  Qual o tamanho máximo das respostas às perguntas de segurança?**

  > **R:** As respostas podem ter entre 3 e 40 caracteres.
  >
  >
* **P:  As respostas duplicadas às perguntas de segurança são rejeitadas?**

  > **R:** Sim, rejeitamos respostas duplicadas às perguntas de segurança.
  >
  >
* **P:  Um usuário pode registrar a mesma pergunta de segurança mais de uma vez?**

  > **R:** Não. Quando um usuário registra uma pergunta específica, ele não pode registrar essa pergunta uma segunda vez.
  >
  >
* **P:  É possível definir um limite mínimo de perguntas de segurança para registro e redefinição?**

  > **R:** Sim, um limite pode ser definido para registro e outro para redefinição. Podem ser necessárias de três a cinco perguntas para registro, e de três a cinco perguntas podem ser necessárias para redefinição.
  >
  >
* **P:  Configurei minha política para exigir que os usuários usem perguntas de segurança para redefinição, mas os administradores do Azure parecem ter configurado a política de maneira diferente.**

  > **R:** Esse é um comportamento esperado. A Microsoft impõe uma política padrão forte de redefinição de senha de dois portões para qualquer função de administrador do Azure. Isso impede que administradores usem perguntas de segurança. Mais informações sobre essa política podem ser encontradas no artigo [Políticas e restrições de senha no Azure Active Directory](concept-sspr-policy.md).
  >
  >
* **P:  Se um usuário registrar mais do que o número máximo de perguntas obrigatórias para redefinição, como as perguntas de segurança serão selecionadas durante a redefinição?**

  > **R:** *X* perguntas de segurança são selecionadas aleatoriamente do número total de perguntas que um usuário registrou, em que *X* é a quantidade definida para a opção **Número de perguntas obrigatórias para a redefinição**. Por exemplo, se um usuário tiver registrado cinco perguntas de segurança, mas apenas três forem necessárias para redefinir uma senha, três das cinco perguntas serão selecionadas aleatoriamente e serão apresentadas na reinicialização. Se o usuário der respostas erradas, o processo de seleção ocorrerá novamente para evitar hammering de perguntas.
  >
  >
* **P:  Por quanto tempo a senha avulsa de email e SMS permanece válida?**

  > **R:** O tempo de vida da sessão para a redefinição de senha é de 15 minutos. Desde o início da operação de redefinição de senha, o usuário tem 15 minutos para redefinir sua senha. A senha de uso único por email e SMS perde a validade depois que esse período de tempo expira.
  >
  >
* **P:  Posso impedir os usuários de redefinirem suas senhas?**

  > **R:** Sim, se você usar um grupo para habilitar a SSPR, poderá remover um usuário individual do grupo que permite aos usuários redefinir suas senhas. Se o usuário for um Administrador Global, ele manterá a capacidade de redefinir sua senha, e isso não poderá ser desabilitado.
  >
  >

## <a name="password-change"></a>Alteração de senha

* **P:  Que local os usuários devem acessar para alterar suas senhas?**

  > **R:** Os usuários podem alterar suas senhas em qualquer lugar em que suas imagens do perfil ou seus ícones são exibidos, como no canto superior direito das experiências do portal do [Office 365](https://portal.office.com) ou do [Painel de Acesso](https://myapps.microsoft.com). Os usuários podem alterar suas senhas na [página de perfil do Painel de Acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os usuários também poderão ser solicitados a alterar suas senhas automaticamente na tela de conexão do Azure AD se elas expirarem. Por fim, os usuários poderão navegar até o [portal de alteração de senha do Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se desejarem alterar suas senhas.
  >
  >
* **P:  Os usuários poderão ser notificados no Portal do Office quando suas senhas locais expirarem?**

  > **R:** Sim, isso é possível hoje mesmo se você usa os Serviços de Federação do Active Directory (AD FS). Se você usar o AD FS, siga as instruções no artigo [Envio de declarações de política de senha com o AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Se você usar a sincronização de hash de senha, isso não será possível atualmente. Nós não sincronizamos políticas de senha locais e, portanto, não é possível postar as notificações de expiração para experiências de nuvem. Em ambos os casos, também é possível [notificar os usuários cujas senhas estejam prestes a expirar usando o PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P:  Posso impedir os usuários de alterarem suas senhas?**

  > **R:** Para usuários somente na nuvem, as alterações de senha não podem ser bloqueadas. Para usuários locais, você pode definir a opção **O usuário não pode alterar a senha** como selecionada. Os usuários selecionados não podem alterar sua senha.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gerenciamento de senha

* **P:  Quanto tempo leva para que os dados sejam exibidos nos relatórios de gerenciamento de senha?**

  > **R:** Os dados devem ser exibidos nos relatórios de gerenciamento de senha entre 5 e 10 minutos. Em algumas instâncias, pode levar até uma hora para que sejam exibidos.
  >
  >
* **P:  Como posso filtrar os relatórios de gerenciamento de senha?**

  > **R:** Para filtrar os relatórios de gerenciamento de senha, selecione a lupa pequena à extrema direita dos rótulos de coluna, próximo ao início do relatório. Se você quiser fazer uma filtragem mais avançada, poderá baixar o relatório do Excel e criar uma tabela dinâmica.
  >
  >
* **P: Qual é o número máximo de eventos armazenados nos relatórios de gerenciamento de senha?**

  > **R:** Até 75 mil eventos de redefinição de senha ou de registro de redefinição de senha são armazenados nos relatórios de gerenciamento de senha, relativo ao período de 30 dias anteriores. Estamos trabalhando para expandir esse número e incluir mais eventos.
  >
  >
* **P:  Qual o período mais antigo coberto pelos relatórios de gerenciamento de senha?**

  > **R:** Os relatórios de gerenciamento de senha mostram as operações ocorridas nos últimos 30 dias. Por enquanto, se você precisar arquivar esses dados, pode baixar os relatórios periodicamente e salvá-los em um local separado.
  >
  >
* **P:  Existe um número máximo de linhas que podem ser exibidas nos relatórios de gerenciamento de senha?**

  > **R:** Sim. Um máximo de 75 mil linhas pode aparecer nos relatórios de gerenciamento de senha, quer seja mostrado na interface do usuário ou baixado.
  >
  >
* **P:  Existe uma API para acessar os dados do relatório de registro ou de redefinição de senha?**

  > **R:** Sim. Para saber como é possível acessar o fluxo de dados do relatório de redefinição de senha, consulte [Saiba como acessar eventos de relatório de redefinição de senha de maneira programática](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>write-back de senha

* **P:  Como funciona os bastidores do write-back de senha?**

  > **R:** Confira o artigo [Como funciona o write-back de senha](howto-sspr-writeback.md) para obter uma explicação detalhada do que acontece quando você habilita o write-back de senha e como os dados fluem pelo sistema novamente ao ambiente local.
  >
  >
* **P:  Quanto tempo leva para o write-back de senha funcionar? Há um atraso de sincronização como com a sincronização com hash de senha?**

  > **R:** O write-back de senha é instantâneo. É um pipeline síncrono que funciona de forma essencialmente diferente da sincronização com hash de senha. O write-back de senha permite aos usuários obter comentários em tempo real sobre o sucesso da operação de redefinição ou de alteração de senha. O tempo médio para um write-back bem-sucedido de uma senha é abaixo de 500 ms.
  >
  >
* **P:  Se minha conta local estiver desabilitada, como minha conta na nuvem e meu acesso à nuvem serão afetados?**

  > **R:** Caso sua ID local esteja desabilitada, sua ID da nuvem e seu acesso à nuvem também estarão desabilitados no próximo intervalo de sincronização por meio do Azure AD Connect. Por padrão, esta sincronização ocorre a cada 30 minutos.
  >
  >
* **P:  Se minha conta local for restrita por uma política de senha do Active Directory local, a SSPR respeitará essa política quando eu alterar a senha?**

  > **R:** Sim, a SSPR depende da política de senha do Active Directory local e a respeita. Esta política inclui a política de senha de domínio do Active Directory típica, bem como as políticas de senha refinadas definidas que são direcionadas para um usuário.
  >
  >
* **P:  O write-back de senha funciona com quais tipos de conta?**

  > **R:** O write-back de senha funciona com contas de usuário sincronizadas do Active Directory local para o Azure AD, incluindo usuários federados, sincronizados com hash de senha e de autenticação de passagem.
  >
  >
* **P:  O write-back de senha impõe as políticas de senha do meu domínio?**

  > **R:** Sim. O write-back de senha impõe a duração, o histórico e a complexidade da senha, filtros e qualquer outra restrição que você possa impor nas senhas no domínio local.
  >
  >
* **P:  O write-back de senha é seguro?  Como posso ter certeza de que não serei invadido por um hacker?**

  > **R:** Sim, o write-back de senha é seguro. Para ler mais sobre as várias camadas de segurança implementadas pelo serviço de write-back de senha, verifique a seção [Segurança de write-back de senha](concept-sspr-writeback.md#password-writeback-security) no artigo [Visão geral do write-back de senha](howto-sspr-writeback.md).
  >
  >

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](howto-sspr-deployment.md)
* [Redefinir ou alterar sua senha](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registro de redefinição de senha de autoatendimento](../user-help/active-directory-passwords-reset-register.md)
* [Você tem uma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](howto-sspr-authenticationdata.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](concept-sspr-policy.md)
* [O que é o write-back de senha e por que devo me importar com isso?](howto-sspr-writeback.md)
* [Como faço para informar sobre a atividade no SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](concept-sspr-howitworks.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
