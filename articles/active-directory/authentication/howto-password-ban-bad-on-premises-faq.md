---
title: Proteção de senha do Azure AD perguntas Frequentes – Azure Active Directory local
description: Perguntas frequentes sobre a proteção por senha do Azure AD local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4879093ed80a554219b053cc5a2bc895126725
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702887"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Proteção por senha do Azure AD local – perguntas frequentes

## <a name="general-questions"></a>Perguntas gerais

**P: Que diretrizes os usuários devem receber sobre como selecionar uma senha segura?**

A diretriz atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Diretrizes de Senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: Há suporte para a proteção por senha do Azure AD local em nuvens não públicas?**

Não, locais de proteção por senha do Azure AD têm suporte apenas na nuvem pública. Não foi anunciada nenhuma data para a disponibilidade de nuvem não pública.

**P: Como posso aplicar os benefícios da proteção por senha do Azure AD a um subconjunto dos usuários locais?**

Sem suporte. Depois de implantada e habilitada, a proteção por senha do Azure AD não discrimina, todos os usuários recebem benefícios de segurança iguais.

**P: Qual é a diferença entre uma alteração de senha e um conjunto de senhas (ou redefinição)?**

Uma alteração de senha é quando um usuário escolhe uma nova senha depois de comprovar que eles têm conhecimento da senha antiga. Por exemplo, isso é o que acontece quando um usuário faz logon no Windows e, em seguida, é solicitado a escolher uma nova senha.

Um conjunto de senha (às vezes chamado de uma redefinição de senha) é quando um administrador substitui a senha em uma conta com uma nova senha, por exemplo, usando a ferramenta de gerenciamento de computadores e usuários do Active Directory. Esta operação requer um alto nível de privilégio (normalmente, o administrador de domínio) e a pessoa que realiza a operação geralmente não tem conhecimento da senha antiga. Cenários de assistência técnica faz isso com frequência, por exemplo quando ajudar um usuário que esqueceu a senha. Você também verá senha definir eventos quando uma nova conta de usuário está sendo criada pela primeira vez com uma senha.

A política de validação de senha se comporta da mesma, independentemente se estiver sendo feito uma alteração de senha ou um conjunto. O serviço do agente de controlador de domínio de proteção de senha do Azure AD registrar eventos diferentes para informá-lo se uma alteração de senha ou a operação set foi feita.  Ver [proteção de senha do Azure AD, monitoramento e registro em log](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**P: Por que os eventos de rejeição de duplicados senha registrados durante a tentativa de definir uma senha fraca estão usando o snap-in Gerenciamento computadores e usuários do Active Directory?**

O snap-in de gerenciamento de computadores e usuários do Active Directory tentará primeiro definir a nova senha usando o protocolo Kerberos. Em caso de falha o snap-in fará uma segunda tentativa de definir a senha usando um protocolo (SAM RPC) herdado (os protocolos específicos usados não são importantes). Se a nova senha é considerada fraca pela proteção de senha do Azure AD, isso resultará em dois conjuntos de eventos de rejeição de redefinição de senha que está sendo registrados.

**P: Há suporte para instalação da proteção por senha do Azure AD lado a lado com outros produtos baseados em filtro de senha?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso importante do Windows e não específico para a proteção por senha do Azure AD. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**P: Como implantar e configurar a proteção de senha do Azure AD no meu ambiente do Active Directory sem o uso do Azure?**

Sem suporte. A proteção de senha do Azure AD é um recurso do Azure que dá suporte a ser estendido para um ambiente local do Active Directory.

**P: Como faço para modificar o conteúdo da política no nível do Active Directory?**

Sem suporte. A política só pode ser administrada usando o portal de gerenciamento do Azure AD. Confira também a pergunta anterior.

**P: Por que o DFSR é necessário para a replicação de SYSVOL?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure AD serão feitos nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**P: Quanto espaço em disco o recurso requer no compartilhamento sysvol de domínio?**

O uso do espaço preciso varia, pois depende de fatores como o número e o tamanho dos tokens banidos na lista proibida global do Microsoft e a lista personalizada por locatário, além da sobrecarga de criptografia. O conteúdo dessas listas provavelmente aumentará no futuro. Com isso em mente, uma expectativa razoável é que o recurso precisará de pelo menos cinco (5) megabytes de espaço no compartilhamento de sysvol do domínio.

**P: Por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: Há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

Nº Uma vez que o servidor proxy é sem estado, não é importante saber qual servidor proxy específico é usado.

**P: Não tem problema implantar o serviço de Proxy de Proteção de Senha do Azure AD lado a lado com outros serviços, como o Azure AD Connect?**

Sim. O serviço de Proxy de Proteção de Senha do Azure AD e o Azure AD Connect nunca devem entrar em conflito diretamente entre si.

**P: Em qual ordem devem os agentes de controlador de domínio e os proxies seja instalados e registrados?**

Há suporte para qualquer ordenação de instalação do agente de Proxy, instalação do agente de controlador de domínio, o registro de floresta e registro do Proxy.

**P: Devo me preocupar com o impacto ao desempenho de meus controladores de domínio ao implantar esse recurso?**

O serviço do Agente de Controlador de Domínio de Proteção por Senha do Azure AD não deve afetar significativamente o desempenho do controlador de domínio em uma implantação íntegra existente do Active Directory.

Para a maioria das implantações do Active Directory, operações de alteração de senha são uma pequena proporção da carga de trabalho geral em qualquer controlador de domínio. Como um exemplo, imagine um domínio do Active Directory com 10 mil contas de usuário e uma política de MaxPasswordAge definida como 30 dias. Em média, esse domínio verá 10.000/30 = ~333 operações de alteração de senha por dia, o que é um número pequeno de operações para até mesmo um único controlador de domínio. Considere um possível cenário de pior hipótese: suponha que essas ~333 alterações de senha em um único controlador de domínio tenham sido feitas em uma única hora. Por exemplo, esse cenário poderá ocorrer quando muitos funcionários voltam a trabalhar na segunda-feira pela manhã. Mesmo nesse caso, estamos ainda frente a ~333/60 minutos = seis alterações de senha por minuto, o que, novamente, não é uma carga significativa.

No entanto se os controladores de domínio atuais já estão executando em níveis de desempenho limitado (por exemplo, tiverem atingido o limite máximo com relação à CPU, ao espaço em disco, E/S de disco etc.), recomendamos adicionar mais controladores de domínio ou expandir o espaço em disco disponível antes de implantar esse recurso. Confira também pergunta acima sobre o uso do espaço em disco sysvol acima.

**P: Desejo testar a proteção de senha do Azure AD em apenas alguns controladores de domínio em meu domínio. É possível forçar alterações de senha de usuário para usar esses controladores de domínio específicos?**

Nº O sistema operacional de cliente do Windows controla qual controlador de domínio é usado quando um usuário altera sua senha. O controlador de domínio é selecionado com base em fatores como atribuições de site e sub-rede do Active Directory, configuração de rede específicas do ambiente etc. A proteção de senha do Azure AD controla esses fatores e não pode influenciar qual controlador de domínio é selecionado para alterar a senha do usuário.

Uma maneira de parcialmente atingir essa meta seria implantar a proteção de senha do Azure AD em todos os controladores de domínio em um determinado site do Active Directory. Essa abordagem fornecerá cobertura razoável para os clientes do Windows atribuídos a esse site e, portanto, também para os usuários que estão fazendo logon nesses clientes e alterando suas senhas.

**P: Se eu instalar o serviço de agente do controlador de domínio de proteção de senha do Azure AD apenas no domínio do PDC (controlador primário), todos os outros controladores de domínio no domínio também serão protegidos?**

Nº Quando uma senha do usuário for alterada em um controlador de domínio do PDC não fornecido, a senha com texto não criptografado nunca será enviada ao PDC (essa ideia é uma percepção incorreta comum). Depois que uma nova senha é aceita em um determinado controlador de domínio, esse controlador de domínio a usa para criar vários hashes específicos de protocolo de autenticação dessa senha e, em seguida, manter esses hashes no diretório. A senha com texto não criptografado não é persistente. Os hashes atualizados então são replicados para o controlador de domínio primário. As senhas de usuário podem, em alguns casos, ser alteradas diretamente no PDC dependendo de vários fatores, como topologia de rede e design de site do Active Directory. (Confira a pergunta anterior.)

Em resumo, a implantação do serviço de Agente de Controlador de Domínio de Proteção por Senha do Azure AD no PDC é necessária para alcançar 100% de cobertura de segurança do recurso em domínio. Implantar o recurso no PDC apenas não oferece benefícios de segurança de proteção por senha do Azure AD para todos os demais controladores de domínio no domínio.

**P: Um pacote de gerenciamento do System Center Operations Manager está disponível para a proteção por senha do Azure AD?**

Nº

**P: Por que é Azure ainda rejeitar senhas fracas, embora eu configurei a política para estar no modo de auditoria?**

Somente há suporte para o modo de auditoria no ambiente do Active Directory local. Azure é implicitamente sempre no modo "impor" quando ele avalia as senhas.

## <a name="additional-content"></a>Conteúdo adicional

Os links a seguir não fazem parte da principal documentação da Proteção de Senha do Azure AD, mas podem ser uma fonte útil de informações adicionais sobre o recurso.

[Proteção por senha do AD do Azure agora está disponível!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guia de proteção contra phishing por email – parte 15: Implementar o serviço de proteção de senha do Microsoft Azure AD (para local também!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[O Bloqueio Inteligente e a Proteção por senha do Azure AD agora estão em Versão Prévia Pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Treinamento em suporte da Microsoft Premier\Unified disponível

Se você estiver interessado em aprender mais sobre a proteção por senha do Azure AD e como implantá-la em seu ambiente, poderá aproveitar um serviço proativo da Microsoft disponível para clientes com um contrato de suporte Premier ou Unified. O serviço chama-se Azure Active Directory: Proteção por Senha. Para obter mais informações, entre em contato com seu Gerente Técnico de Conta.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma pergunta sobre proteção de senha do Azure AD local que não foi respondida aqui, envie um item de Comentário abaixo. Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
