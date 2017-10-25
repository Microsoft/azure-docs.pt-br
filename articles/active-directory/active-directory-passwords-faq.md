---
title: 'Perguntas frequentes: SSPR do Azure AD | Microsoft Docs'
description: "Perguntas frequentes sobre o autoatendimento de redefinição de senha do Azure AD"
services: active-directory
keywords: "Gerenciamento de senhas do Active Directory, gerenciamento de senhas, autoatendimento de redefinição de senha do Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a457ba3109625ab43ae3a88b95a3e7e1e9641921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="password-management-frequently-asked-questions"></a>Perguntas frequentes sobre gerenciamento de senhas

Veja abaixo algumas perguntas frequentes sobre tudo relativo à redefinição de senhas.

Caso você tenha uma pergunta geral sobre o Azure AD e o autoatendimento de redefinição de senha que não foi respondida aqui, peça ajuda à comunidade nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem Engenheiros, Gerentes de Produto, MVPs e colegas Profissionais de TI.

Esta seção de perguntas frequentes é dividida nas seguintes seções:

* [**Perguntas sobre o registro de redefinição de senha**](#password-reset-registration)
* [**Perguntas sobre a redefinição de senha**](#password-reset)
* [**Perguntas sobre alteração de senha**](#password-change)
* [**Perguntas sobre relatórios de gerenciamento de senha**](#password-management-reports)
* [**Perguntas sobre o write-back de senha**](#password-writeback)

## <a name="password-reset-registration"></a>Registro de redefinição de senha

* **P: meus usuários podem registrar seus próprios dados de redefinição de senha?**

  > **R:** Sim, desde que a redefinição de senha esteja habilitada e eles sejam licenciados, eles podem ir para o portal de Registro de Redefinição de Senha em http://aka.ms/ssprsetup para registrar as informações de autenticação. Os usuários também podem se registrar se conectando ao painel de acesso em http://myapps.microsoft.com, clicando na guia perfil e clicando na opção Registrar-se para a redefinição de senha.
  >
  >
* **P: posso definir dados de redefinição de senha em nome dos meus usuários?**

  > **R:** Sim, você pode fazer isso com o Azure AD Connect, PowerShell, [portal do Azure](https://portal.azure.com) ou o Portal de Administração do Office. Para obter mais informações, consulte o artigo [Dados usados pelo Autoatendimento de Redefinição de Senha do Azure AD](active-directory-passwords-data.md).
  >
  >
* **P: posso sincronizar dados de perguntas de segurança do local?**

  > **R:** isso não é possível atualmente.
  >
  >
* **P: meus usuários podem registrar dados de forma que outros usuários não possam ver esses dados?**

  > **R:** Sim. Quando os usuários registram dados usando o Portal de Registro de Redefinição de Senha, eles são salvos em campos de autenticação privada visíveis apenas por Administradores Globais e pelo usuário.
    >
    > [!NOTE]
    > Se uma **conta do Administrador do Azure** registrar o número de telefone de autenticação, ela também será populada no campo de telefone celular e ficará visível.
    >
  >
  >
* **P: meus usuários precisam ser registrados antes de poder usar a redefinição de senha?**

  > **R:** Não. Se você definir informações de autenticação suficientes em nome deles, os usuários não precisarão se registrar. A redefinição de senha funcionará desde que você tenha formatado corretamente os dados armazenados nos campos apropriados no diretório.
  >
  >
* **P: posso sincronizar ou definir os campos Telefone de autenticação, Autenticação de email ou Telefone alternativo em nome dos meus usuários?**

  > **R:** isso não é possível atualmente.
  >
  >
* **P: como o portal de registro sabe quais opções mostrar aos meus usuários?**

  > **R:** O portal de registro de redefinição de senha só mostra as opções que você habilitou para os usuários. Essas opções estão localizadas na seção Política de Redefinição de Senha de Usuário da guia Configurar do diretório. Por exemplo, isso significa que, se você não habilitar perguntas de segurança, os usuários não poderão se registrar nessa opção.
  >
  >
* **P: quando um usuário é considerado registrado?**

  > **R:** Um usuário é considerado registrado para o SSPR quando ele registrou, pelo menos, o **Número de métodos obrigatórios para a redefinição** que você definiu no [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Redefinição de senha

* **P: quanto tempo deve levar até que eu receba uma chamada telefônica, um SMS ou um email de redefinição de senha?**

  > **R:** Emails, mensagens SMS e chamadas telefônicas devem ser recebidas em menos de um minuto, normalmente de 5 a 20 segundos.
    >Se você não receber a notificação nesse período:
        > * Verifique a pasta Lixo Eletrônico.
        > * Verifique se o número ou o email de contato é aquele que você espera.
        > * Verifique se os dados de autenticação no diretório estão formatados corretamente.
                >     * Exemplo: “+1 4255551234” ou “user@contoso.com”
  >
  >
* **P: quais idiomas são compatíveis com a redefinição de senha?**

  > **R:** A interface do usuário de redefinição de senha, as mensagens SMS e as chamadas de voz estão localizadas nos mesmos idiomas com suporte no Office 365.
  >
  >
* **P: que partes da experiência de redefinição de senha ficam personalizadas com a marca quando eu definir marcas organizacionais na guia Configurar no meu diretório?**

  > **R:** O portal de redefinição de senha mostra o logotipo de sua organização e também permite que você configure o link Contate o administrador para apontar para uma URL ou um email personalizado. Todos os emails enviados pela redefinição de senha incluem o logotipo de sua organização, as cores, o nome no corpo do email e o nome De personalizado.
  >
  >
* **P: como eu instruo os usuários sobre onde acessar para redefinir as suas senhas?**

  > **R:** Tente alguma das sugestões em nosso [artigo de implantação do SSPR](active-directory-passwords-best-practices.md#email-based-rollout)
  >
  >
* **P: posso usar essa página em um dispositivo móvel?**

  > **R:** sim, essa página funciona em dispositivos móveis.
  >
  >
* **P: há suporte para desbloqueio das contas locais do active directory quando os usuários redefinirem as suas senhas?**

  > **R:** Sim. Quando um usuário redefine a senha e o write-back de senha foi implantado usando o Azure AD Connect, a conta desse usuário é desbloqueada automaticamente quando ele redefine a senha.
  >
  >
* **P: como posso integrar a redefinição de senha diretamente à experiência de entrada na área de trabalho do usuário?**

  > **R:** Se você for um cliente do Azure AD Premium, poderá instalar o Microsoft Identity Manager sem custo adicional e implantar a solução de redefinição de senha local para atender a esse requisito.
  >
  >
* **P: posso definir perguntas de segurança diferentes para diferentes localidades?**

  > **R:** isso não é possível atualmente.
  >
  >
* **P: quantas perguntas podemos configurar para a opção de autenticação com perguntas de segurança?**

  > **R:** Você pode configurar até 20 perguntas de segurança personalizadas no [portal do Azure](https://portal.azure.com).
  >
  >
* **P: qual o tamanho máximo das perguntas de segurança?**

  > **R:** as perguntas de segurança podem ter entre 3 e 200 caracteres.
  >
  >
* **P: qual o tamanho máximo das respostas às perguntas de segurança?**

  > **R:** as respostas podem ter entre 3 e 40 caracteres.
  >
  >
* **P: as respostas duplicadas para perguntas de segurança são rejeitadas?**

  > **R:** sim, rejeitamos respostas duplicadas para perguntas de segurança.
  >
  >
* **P: Um usuário pode registrar a mesma pergunta de segurança mais de uma vez?**

  > **R:** Não. Quando um usuário registra uma pergunta específica, ele não pode se registrar nessa pergunta uma segunda vez.
  >
  >
* **P: é possível definir um limite mínimo de perguntas de segurança para registro e redefinição?**

  > **R:** sim, um limite pode ser definido para o registro e outro para a redefinição. Podem ser necessárias de três a cinco perguntas de segurança para o registro e de três a cinco perguntas para a redefinição.
  >
  >
* **P: Configurei minha política para exigir que os usuários usem perguntas de segurança para redefinição, mas os administradores do Azure parecem ter configurado de maneira diferente.**

  > **R:** Esse é um comportamento esperado. A Microsoft impõe uma política padrão forte de redefinição de senha de dois portões para qualquer função de administrador do Azure. Isso impede administradores de usar perguntas de segurança. Mais informações sobre essa política podem ser encontradas no artigo [Políticas e restrições de senha no Azure Active Directory](active-directory-passwords-policy.md#administrator-password-policy-differences)
  >
  >
* **P: se um usuário tiver registrado mais do que o número máximo de perguntas obrigatórias para a redefinição, como as perguntas de segurança são selecionadas durante a redefinição?**

  > **R:** X perguntas de segurança são selecionadas aleatoriamente do número total de perguntas nas quais um usuário se registrou, em que X é o **Número de perguntas obrigatórias para a redefinição**. Por exemplo, se um usuário tem 5 perguntas de segurança registradas, mas apenas 3 são obrigatórias para a redefinição, 3 das 5 são selecionadas aleatoriamente e apresentadas no momento da redefinição. Se o usuário der respostas erradas, o processo de seleção ocorrerá novamente para evitar hammering de perguntas.
  >
  >
* **P: vocês impedem os usuários de tentar redefinir a senha várias vezes em um curto período de tempo?**

  > **R:** Sim, existem recursos de segurança internos na redefinição de senha para proteção contra uso indevido. Os usuários têm somente cinco tentativas de redefinição de senha em uma mesma hora antes de serem bloqueados por 24 horas. Os usuários somente podem tentar validar um número de telefone cinco vezes em uma mesma hora antes de serem bloqueados por 24 horas. Os usuários somente podem experimentar um único método de autenticação cinco vezes em uma mesma hora antes de serem bloqueados por 24 horas.
  >
  >
* **P: por quanto tempo vale a senha de uso único por email e SMS?**

  > **R:** O tempo de vida da sessão para a redefinição de senha é de 15 minutos. Desde o início da operação de redefinição de senha, o usuário tem 15 minutos para redefinir sua senha. A senha de uso único por email e SMS perde a validade depois que esse período de tempo expira.
  >
  >

## <a name="password-change"></a>Alteração de senha

* **P: onde os usuários devem ir para alterar suas senhas?**

  > **R:** Os usuários podem alterar suas senhas em qualquer lugar em que veem seus ícones ou imagens de perfil (como no canto superior direito das experiências do [Office 365](https://portal.office.com) ou do [Painel de Acesso](https://myapps.microsoft.com)). Os usuários podem alterar suas senhas na [página de perfil do Painel de Acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os usuários também poderão ser solicitados a alterar suas senhas automaticamente na tela de conexão do Azure AD se elas expirarem. Por fim, os usuários podem navegar até o [Portal de alteração de senha do Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se desejarem alterar suas senhas.
  >
  >
* **P: meus usuários podem ser notificados no Portal do Office quando uma senha local expirar?**

  > **R:** isso é possível no momento se você está usando o ADFS, seguindo as instruções aqui: [Enviando declarações de política de senha com o ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Se você está usando a sincronização de hash de senha, isso não é possível atualmente. Isso ocorre porque nós não sincronizamos políticas de senha locais e, portanto, não é possível postar as notificações de expiração para experiências de nuvem. Em ambos os casos, também é possível [notificar os usuários cujas senhas estejam prestes a expirar usando o PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >

## <a name="password-management-reports"></a>Relatórios de gerenciamento de senha

* **P: quanto tempo leva para que os dados sejam exibidos nos relatórios de gerenciamento de senha?**

  > **R:** os dados devem ser exibidos nos relatórios de gerenciamento de senha entre 5 e 10 minutos. Em algumas instâncias, pode levar até uma hora para que sejam exibidos.
  >
  >
* **P: como posso filtrar os relatórios de gerenciamento de senha?**

  > **R:** Você pode filtrar os relatórios de gerenciamento de senhas clicando na lupa pequena na extremidade direita dos rótulos de coluna, próximo ao início do relatório. Se você quiser fazer uma filtragem mais avançada, pode baixar o relatório do Excel e criar uma tabela dinâmica.
  >
  >
* **P: Qual é o número máximo de eventos armazenados nos relatórios de gerenciamento de senha?**

  > **R:** até 75 mil redefinições de senha ou eventos de registro de redefinição de senha são armazenados nos relatórios de gerenciamento de senha relativo ao período de 30 dias anteriores.  Estamos trabalhando para expandir esse número e incluir mais eventos.
  >
  >
* **P: qual o período mais antigo coberto pelos relatórios de gerenciamento de senha?**

  > **R:** os relatórios de gerenciamento de senha mostram operações ocorridas nos últimos 30 dias. Por enquanto, se você precisar arquivar esses dados, pode baixar os relatórios periodicamente e salvá-los em um local separado.
  >
  >
* **P: existe um número máximo de linhas que podem ser exibidas nos relatórios de gerenciamento de senha?**

  > **R:** sim, um máximo de 75 mil linhas pode aparecer nos relatórios de gerenciamento de senha, quer seja mostrado na interface do usuário ou baixado.
  >
  >
* **P: Existe uma API para acessar os dados do relatório de redefinição de senha ou de registro de redefinição de senha?**

  > **R:** Sim. Consulte a documentação a seguir para saber como você pode acessar o fluxo de dados do relatório de redefinição de senha.  [Saiba como acessar programaticamente os eventos de relatório de redefinição de senha](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Write-back de senha

* **P: como funciona os bastidores do write-back de senha?**

  > **R:** Consulte [Como funciona o write-back de senha](active-directory-passwords-writeback.md) para obter uma explicação detalhada do que acontece quando você habilita o write-back de senha e como os dados fluem pelo sistema novamente ao seu ambiente local.
  >
  >
* **P: quanto tempo demora para o write-back de senha funcionar?  Há um atraso de sincronização como com a sincronização com hash de senha?**

  > **R:** o write-back de senha é instantâneo. É um pipeline síncrono que funciona de forma essencialmente diferente da sincronização com hash de senha. O write-back de senha permite aos usuários obter comentários em tempo real sobre o sucesso da operação de redefinição ou de alteração de senha. O tempo médio para um write-back bem-sucedido de uma senha é abaixo de 500 ms.
  >
  >
* **P: Se minha conta local estiver desabilitada, como minha conta ou meu acesso na nuvem será afetado?**

  > **R:** Se sua ID local estiver desabilitada, a ID ou o acesso na nuvem também estará desabilitado no próximo intervalo de sincronização por meio do AAD Connect. Por padrão, isso ocorre a cada 30 minutos.
  >
  >
* **P: Se minha conta local for restrita por uma política de senha do Active Directory local, o SSPR respeitará essa política quando eu alterar a senha?**

  > **R:** Sim, o SSPR se baseia e respeita a política de senha do AD local, incluindo a política de senha típica do domínio do AD, bem como as políticas de senha refinadas e definidas, direcionadas a determinado usuário.
  >
  >
* **P: o write-back de senha funciona com quais tipos de conta?**

  > **R:** O write-back de senha funciona com usuários Federados e Sincronizados com Hash de Senha.
  >
  >
* **P: o write-back de senha impõe as políticas de senha do meu domínio?**

  > **R:** Sim, o write-back de senha impõe a duração, o histórico e a complexidade da senha, filtros e qualquer outra restrição que você possa impor nas senhas no domínio local.
  >
  >
* **P: o write-back de senha é seguro?  Como posso ter certeza de que não serei invadido por um hacker?**

  > **R:** Sim, o write-back de senha é seguro. Para ler mais sobre as quatro camadas de segurança implementadas pelo serviço de write-back de senha, confira a seção [Modelo de segurança do write-back de senha](active-directory-passwords-writeback.md#password-writeback-security-model) em Como funciona o write-back de senha.
  >
  >

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD 
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md): entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Relatórios**](active-directory-passwords-reporting.md) – descubra se, quando e onde os usuários estão acessando a funcionalidade do SSPR
* [**Política**](active-directory-passwords-policy.md) – entenda e defina políticas de senha do Azure AD
* [**Write-back de Senha**](active-directory-passwords-writeback.md) – como o write-back de senha funciona com o diretório local
* [**Aprofundamento Técnico**](active-directory-passwords-how-it-works.md) – vá para os bastidores para entender como ele funciona
* [**Resolver problemas**](active-directory-passwords-troubleshoot.md) – saiba como resolver problemas comuns encontrados no SSPR
