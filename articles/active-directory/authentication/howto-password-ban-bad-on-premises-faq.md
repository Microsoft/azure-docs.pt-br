---
title: Perguntas frequentes sobre a proteção por senha do Azure AD local
description: Examinar as perguntas frequentes sobre a proteção de senha do Azure AD em um ambiente de Active Directory Domain Services local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625120"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Perguntas frequentes locais sobre a proteção por senha do Azure AD

Esta seção fornece respostas para muitas perguntas frequentes sobre a proteção de senha do Azure AD.

## <a name="general-questions"></a>Perguntas gerais

**P: quais diretrizes os usuários devem receber sobre como selecionar uma senha segura?**

A diretriz atual da Microsoft sobre este tópico pode ser encontrada no seguinte link:

[Diretrizes de Senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: a proteção de senha do Azure AD local tem suporte em nuvens não públicas?**

A proteção de senha do Azure AD local tem suporte na nuvem pública e na nuvem Arlington. Nenhuma data foi anunciada para disponibilidade em outras nuvens.

O portal do AD do Azure permite a modificação da configuração "proteção por senha para Windows Server Active Directory" específica local, mesmo em nuvens sem suporte; essas alterações serão persistidas, mas, caso contrário, nunca entrarão em vigor. O registro de florestas ou agentes de proxy local não é suportado em nuvens sem suporte e qualquer tentativa de registro desse tipo sempre falhará.

**P: como posso aplicar os benefícios da proteção de senha do Azure AD a um subconjunto de meus usuários locais?**

Não há suporte. Depois de implantada e habilitada, a proteção por senha do Azure AD não discrimina, todos os usuários recebem benefícios de segurança iguais.

**P: Qual é a diferença entre uma alteração de senha e um conjunto de senhas (ou redefinição)?**

Uma alteração de senha é quando um usuário escolhe uma nova senha depois de provar que tem conhecimento da senha antiga. Por exemplo, uma alteração de senha é o que acontece quando um usuário faz logon no Windows e, em seguida, é solicitado a escolher uma nova senha.

Um conjunto de senhas (às vezes chamado de redefinição de senha) é quando um administrador substitui a senha em uma conta com uma nova senha, por exemplo, usando a ferramenta de gerenciamento Active Directory usuários e computadores. Essa operação requer um alto nível de privilégio (geralmente administrador de domínio) e a pessoa que executa a operação geralmente não tem conhecimento da senha antiga. Cenários de assistência técnica geralmente executam conjuntos de senhas, por exemplo, ao auxiliar um usuário que esqueceu sua senha. Você também verá eventos de definição de senha quando uma conta de usuário totalmente nova estiver sendo criada pela primeira vez com uma senha.

A política de validação de senha comporta-se o mesmo, independentemente de uma alteração ou definição de senha estar sendo feita. O serviço de agente DC de proteção de senha do Azure AD registra eventos diferentes para informá-lo se uma operação de alteração ou de definição de senha foi feita.  Consulte [monitoramento e registro em log da proteção de senha do Azure ad](./howto-password-ban-bad-on-premises-monitor.md).

**P: a proteção de senha do Azure AD valida as senhas existentes após a instalação?**

Não-a proteção de senha do Azure AD só pode impor a política de senha em senhas de texto não criptografado durante uma alteração de senha ou uma operação de definição. Depois que uma senha é aceita pelo Active Directory, somente os hashes específicos de protocolo de autenticação dessa senha são persistidos. A senha de texto não criptografado nunca é persistida, portanto, a proteção de senha do Azure AD não pode validar as senhas existentes.

Após a implantação inicial da proteção de senha do Azure AD, todos os usuários e contas começarão a usar uma senha validada de proteção de senha do Azure AD, já que suas senhas existentes expiram normalmente ao longo do tempo. Se desejar, esse processo pode ser acelerado por uma expiração manual única de senhas de conta de usuário.

As contas configuradas com "a senha nunca expira" nunca serão forçadas a alterar sua senha, a menos que a expiração manual seja feita.

**P: por que os eventos de rejeição de senha duplicados são registrados ao tentar definir uma senha fraca usando o snap-in de gerenciamento de usuários e computadores Active Directory?**

O snap-in de gerenciamento de usuários e computadores Active Directory primeiro tentará definir a nova senha usando o protocolo Kerberos. Após a falha, o snap-in fará uma segunda tentativa de definir a senha usando um protocolo herdado (SAM RPC) (os protocolos específicos usados não são importantes). Se a nova senha for considerada fraca pela proteção de senha do Azure AD, esse comportamento de snap-in resultará em um log de dois conjuntos de eventos de rejeição de redefinição de senha.

**P: por que os eventos de validação de senha da proteção de senha do Azure AD são registrados com um nome de usuário vazio?**

Active Directory dá suporte à capacidade de testar uma senha para ver se ela passa os requisitos de complexidade de senha atuais do domínio, por exemplo, usando a API [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) . Quando uma senha é validada dessa forma, o teste também inclui validação por produtos baseados em filtro de senha, como a proteção de senha do Azure AD, mas os nomes de usuário passados para uma determinada dll de filtro de senha ficarão vazios. Nesse cenário, a proteção de senha do Azure AD ainda validará a senha usando a política de senha atualmente em vigor e emitirá uma mensagem de log de eventos para capturar o resultado, no entanto, a mensagem de log de eventos terá campos de nome de usuário vazios.

**P: há suporte para instalar a proteção de senha do Azure AD lado a lado com outros produtos baseados em filtro de senha?**

Sim. Suporte para várias dlls de filtro de senha registrada é um recurso importante do Windows e não específico para a proteção por senha do Azure AD. Todas as dlls de filtro de senha registrados devem aceitar antes que uma senha seja aceita.

**P: como posso implantar e configurar a proteção de senha do Azure AD no meu ambiente de Active Directory sem usar o Azure?**

Não há suporte. A proteção de senha do Azure AD é um recurso do Azure que dá suporte a ser estendido para um ambiente local do Active Directory.

**P: como posso modificar o conteúdo da política no nível de Active Directory?**

Não há suporte. A política só pode ser administrada usando o portal do AD do Azure. Confira também a pergunta anterior.

**P: por que o DFSR é necessário para a replicação de sysvol?**

FRS (a tecnologia do predecessor para DFSR) tem vários problemas conhecidos e é totalmente suportada em versões mais recentes do Windows Server Active Directory. Zero testes da proteção por senha do Azure AD serão feitos nos domínios configurados para FRS.

Para obter mais informações, consulte:

[O caso para a replicação do sysvol migrando para DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[O fim é Nigh para FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Se o seu domínio ainda não estiver usando o DFSR, você deverá migrá-lo para usar o DFSR antes de instalar a proteção de senha do Azure AD. Para obter mais informações, consulte o seguinte link:

[Guia de Migração de Replicação SYSVOL: Replicação FRS para DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> O software de agente DC de proteção por senha do Azure AD será instalado no momento em controladores de domínio em domínios que ainda estão usando o FRS para replicação SYSVOL, mas o software não funcionará corretamente nesse ambiente. Efeitos colaterais negativos adicionais incluem arquivos individuais que falham na replicação e os procedimentos de restauração de SYSVOL que aparecem com êxito, mas falham silenciosamente na replicação de todos os arquivos. Você deve migrar seu domínio para usar o DFSR assim que possível, tanto para os benefícios inerentes do DFSR quanto para desbloquear a implantação da proteção de senha do Azure AD. Versões futuras do software serão desabilitadas automaticamente quando executadas em um domínio que ainda esteja usando o FRS.

**P: quanto espaço em disco o recurso requer no compartilhamento SYSVOL do domínio?**

O uso do espaço preciso varia, pois depende de fatores como o número e o tamanho dos tokens banidos na lista proibida global do Microsoft e a lista personalizada por locatário, além da sobrecarga de criptografia. O conteúdo dessas listas provavelmente aumentará no futuro. Com isso em mente, uma expectativa razoável é que o recurso precisará de pelo menos cinco (5) megabytes de espaço no compartilhamento de sysvol do domínio.

**P: por que uma reinicialização é necessária para instalar ou atualizar o software do agente de controlador de domínio?**

Esse requisito é causado pelo comportamento principal do Windows.

**P: há alguma maneira de configurar um agente de controlador de domínio para usar um servidor proxy específico?**

Não. Uma vez que o servidor proxy é sem estado, não é importante saber qual servidor proxy específico é usado.

**P: não há problema em implantar o serviço proxy de proteção de senha do Azure AD lado a lado com outros serviços, como Azure AD Connect?**

Sim. O serviço de Proxy de Proteção de Senha do Azure AD e o Azure AD Connect nunca devem entrar em conflito diretamente entre si.

Infelizmente, uma incompatibilidade foi encontrada entre a versão do serviço do atualizador do agente do Microsoft Azure AD Connect que é instalado pelo software de proxy de proteção de senha do Azure AD e a versão do serviço que é instalada pelo software [proxy de aplicativo do Azure Active Directory](../manage-apps/application-proxy.md) . Essa incompatibilidade pode fazer com que o serviço atualizador do agente não consiga contatar o Azure para atualizações de software. Não é recomendável instalar o proxy de proteção de senha do Azure AD e Proxy de Aplicativo do Azure Active Directory no mesmo computador.

**P: em que ordem os agentes de DC e os proxies serão instalados e registrados?**

Há suporte para qualquer ordem de instalação do agente de proxy, instalação do agente DC, registro de floresta e registro de proxy.

**P: devo me preocupar com o impacto sobre o desempenho em meus controladores de domínio de implantar esse recurso?**

O serviço do Agente de Controlador de Domínio de Proteção por Senha do Azure AD não deve afetar significativamente o desempenho do controlador de domínio em uma implantação íntegra existente do Active Directory.

Para a maioria das implantações do Active Directory, operações de alteração de senha são uma pequena proporção da carga de trabalho geral em qualquer controlador de domínio. Como um exemplo, imagine um domínio do Active Directory com 10 mil contas de usuário e uma política de MaxPasswordAge definida como 30 dias. Em média, esse domínio verá 10.000/30 = ~333 operações de alteração de senha por dia, o que é um número pequeno de operações para até mesmo um único controlador de domínio. Considere um possível cenário de pior hipótese: suponha que essas ~333 alterações de senha em um único controlador de domínio tenham sido feitas em uma única hora. Por exemplo, esse cenário poderá ocorrer quando muitos funcionários voltam a trabalhar na segunda-feira pela manhã. Mesmo nesse caso, estamos ainda frente a ~333/60 minutos = seis alterações de senha por minuto, o que, novamente, não é uma carga significativa.

No entanto se os controladores de domínio atuais já estão executando em níveis de desempenho limitado (por exemplo, tiverem atingido o limite máximo com relação à CPU, ao espaço em disco, E/S de disco etc.), recomendamos adicionar mais controladores de domínio ou expandir o espaço em disco disponível antes de implantar esse recurso. Confira também pergunta acima sobre o uso do espaço em disco sysvol acima.

**P: quero testar a proteção de senha do Azure AD em apenas alguns DCs em meu domínio. É possível forçar as alterações de senha do usuário para usar esses DCs específicos?**

Não. O sistema operacional de cliente do Windows controla qual controlador de domínio é usado quando um usuário altera sua senha. O controlador de domínio é selecionado com base em fatores como Active Directory atribuições de site e sub-rede, configuração de rede específica do ambiente, etc. A proteção de senha do Azure AD não controla esses fatores e não pode influenciar qual controlador de domínio é selecionado para alterar a senha de um usuário.

Uma maneira de parcialmente atingir essa meta seria implantar a proteção de senha do Azure AD em todos os controladores de domínio em um determinado site do Active Directory. Essa abordagem fornecerá cobertura razoável para os clientes do Windows atribuídos a esse site e, portanto, também para os usuários que estão fazendo logon nesses clientes e alterando suas senhas.

**P: se eu instalar o serviço de agente de DC de proteção por senha do Azure AD apenas no controlador de domínio primário (PDC), todos os outros controladores de domínio no domínio também serão protegidos?**

Não. Quando uma senha do usuário for alterada em um controlador de domínio do PDC não fornecido, a senha com texto não criptografado nunca será enviada ao PDC (essa ideia é uma percepção incorreta comum). Depois que uma nova senha é aceita em um determinado controlador de domínio, esse controlador de domínio a usa para criar vários hashes específicos de protocolo de autenticação dessa senha e, em seguida, manter esses hashes no diretório. A senha com texto não criptografado não é persistente. Os hashes atualizados então são replicados para o controlador de domínio primário. As senhas de usuário podem, em alguns casos, ser alteradas diretamente no PDC dependendo de vários fatores, como topologia de rede e design de site do Active Directory. (Confira a pergunta anterior.)

Em resumo, a implantação do serviço de Agente de Controlador de Domínio de Proteção por Senha do Azure AD no PDC é necessária para alcançar 100% de cobertura de segurança do recurso em domínio. Implantar o recurso no PDC apenas não oferece benefícios de segurança de proteção por senha do Azure AD para todos os demais controladores de domínio no domínio.

**P: por que o bloqueio inteligente personalizado não funciona mesmo depois que os agentes são instalados em meu ambiente de Active Directory local?**

O bloqueio inteligente personalizado só tem suporte no Azure AD. As alterações nas configurações de bloqueio inteligente personalizadas no portal do Azure AD não têm nenhum efeito no ambiente de Active Directory local, mesmo com os agentes instalados.

**P: um pacote de gerenciamento do System Center Operations Manager disponível para a proteção de senha do Azure AD?**

Não.

**P: por que o Azure AD ainda rejeita senhas fracas, embora tenha configurado a política para estar no modo de auditoria?**

O modo de auditoria só tem suporte no ambiente de Active Directory local. O Azure AD está implicitamente sempre no modo "impor" quando avalia senhas.

**P: meus usuários veem a mensagem de erro tradicional do Windows quando uma senha é rejeitada pela proteção de senha do Azure AD. É possível personalizar essa mensagem de erro para que os usuários saibam o que realmente aconteceu?**

Não. A mensagem de erro vista pelos usuários quando uma senha é rejeitada por um controlador de domínio é controlada pelo computador cliente, não pelo controlador de domínio. Esse comportamento ocorre quando uma senha é rejeitada pelo padrão Active Directory políticas de senha ou por uma solução baseada em filtro de senha, como a proteção de senha do Azure AD.

## <a name="password-testing-procedures"></a>Procedimentos de teste de senha

Talvez você queira fazer alguns testes básicos de várias senhas para validar a operação adequada do software e obter uma melhor compreensão do [algoritmo de avaliação de senha](concept-password-ban-bad.md#how-are-passwords-evaluated). Esta seção descreve um método para esse teste que foi projetado para produzir resultados repetíveis.

Por que é necessário seguir essas etapas? Há vários fatores que dificultam o teste de senhas, que podem ser repetidas e reproduzíveis no ambiente de Active Directory local:

* A política de senha é configurada e persistida no Azure, e as cópias da política são sincronizadas periodicamente pelos agentes do DC local usando um mecanismo de sondagem. A latência inerente a esse ciclo de sondagem pode causar confusão. Por exemplo, se você configurar a política no Azure, mas esquecer de sincronizá-la com o agente de DC, seus testes poderão não produzir os resultados esperados. No momento, o intervalo de sondagem é codificado uma vez por hora, mas a espera de uma hora entre as alterações de política é não ideal para um cenário de teste interativo.
* Depois que uma nova política de senha é sincronizada com um controlador de domínio, mais latência ocorrerá enquanto ele é replicado para outros controladores de domínio. Esses atrasos podem causar resultados inesperados se você testar uma alteração de senha em um controlador de domínio que ainda não tenha recebido a versão mais recente da política.
* O teste de alterações de senha por meio de uma interface do usuário dificulta a confiança nos resultados. Por exemplo, é fácil digitar incorretamente uma senha inválida em uma interface do usuário, especialmente desde que a maioria das interfaces de usuário de senha oculte a entrada do usuário (por exemplo, como o Windows Ctrl-Alt-Delete-> a interface do usuário alterar senha).
* Não é possível controlar estritamente qual controlador de domínio é usado ao testar alterações de senha de clientes ingressados no domínio. O sistema operacional cliente do Windows seleciona um controlador de domínio com base em fatores como Active Directory atribuições de site e sub-rede, configuração de rede específica do ambiente, etc.

Para evitar esses problemas, as etapas abaixo se baseiam no teste de linha de comando de redefinições de senha enquanto estiverem conectados a um controlador de domínio.

> [!WARNING]
> Esses procedimentos devem ser usados somente em um ambiente de teste, uma vez que todas as alterações de senha de entrada e redefinições serão aceitas sem validação enquanto o serviço de agente de DC for interrompido e também para evitar os maiores riscos inerentes ao logon em um controlador de domínio.

As etapas a seguir pressupõem que você instalou o agente de DC em pelo menos um controlador de domínio, instalou pelo menos um proxy e registrou o proxy e a floresta.

1. Faça logon em um controlador de domínio usando credenciais de administrador de domínio (ou outras credenciais que tenham privilégios suficientes para criar contas de usuário de teste e redefinir senhas), que tem o software de agente de controlador de domínio instalado e foi reinicializado.
1. Abra Visualizador de Eventos e navegue até o [log de eventos administrador de agente do DC](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Abra uma janela de prompt de comando elevado.
1. Criar uma conta de teste para fazer testes de senha

   Há várias maneiras de criar uma conta de usuário, mas uma opção de linha de comando é oferecida aqui como uma maneira de torná-la mais fácil durante os ciclos de teste repetitivos:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Para fins de discussão abaixo, suponha que criamos uma conta de teste chamada "ContosoUser", por exemplo:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Abra um navegador da Web (talvez seja necessário usar um dispositivo separado em vez do controlador de domínio), entre no [portal do Azure](https://portal.azure.com)e navegue até Azure Active Directory > segurança > métodos de autenticação > proteção por senha.
1. Modifique a política de proteção de senha do Azure AD conforme necessário para os testes que você deseja executar.  Por exemplo, você pode decidir configurar o modo imposto ou de auditoria, ou pode decidir modificar a lista de termos proibidos na sua lista de senhas excluídas personalizadas.
1. Sincronize a nova política interrompendo e reiniciando o serviço de agente de controlador de domínio.

   Essa etapa pode ser realizada de várias maneiras. Uma delas seria usar o console administrativo de gerenciamento de serviços clicando com o botão direito do mouse no serviço agente DC de proteção de senha do Azure AD e escolhendo "reiniciar". Outra maneira pode ser executada na janela de prompt de comando da seguinte forma:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Verifique o Visualizador de Eventos para verificar se uma nova política foi baixada.

   Sempre que o serviço de agente de controlador de domínio for interrompido e iniciado, você verá os eventos 2 30006 emitidos em sucessão de fechamento. O primeiro evento 30006 refletirá a política que foi armazenada em cache no disco no compartilhamento do SYSVOL. O segundo evento 30006 (se presente) deve ter uma data de política de locatário atualizada e, se for, refletirá a política que foi baixada do Azure. O valor de data da política de locatário é codificado no momento para exibir o carimbo de data/hora aproximado de que a política foi baixada do Azure.
   
   Se o segundo evento 30006 não for exibido, você deverá solucionar o problema antes de continuar.
   
   Os eventos 30006 serão semelhantes a este exemplo:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Por exemplo, alterar entre o modo imposto e de auditoria resultará na modificação do sinalizador AuditOnly (a política acima com AuditOnly = 0 está no modo imposto); as alterações na lista de senhas excluídas personalizadas não são refletidas diretamente no evento 30006 acima (e não são registradas em nenhum outro lugar por motivos de segurança). O download da política do Azure após essa alteração também incluirá a lista de senhas excluídas personalizada modificada.

1. Execute um teste tentando redefinir uma nova senha na conta de usuário de teste.

   Esta etapa pode ser feita na janela de prompt de comando da seguinte maneira:

   ```text
   net.exe user ContosoUser <password>
   ```

   Depois de executar o comando, você pode obter mais informações sobre o resultado do comando examinando o Visualizador de eventos. Os eventos de resultado da validação de senha são documentados no tópico [log de eventos do administrador de agente do DC](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) ; Você usará esses eventos para validar o resultado do teste, além da saída interativa dos comandos de net.exe.

   Vamos experimentar um exemplo: tentando definir uma senha que é banida pela lista global da Microsoft (Observe que a lista não está [documentada](concept-password-ban-bad.md#global-banned-password-list) , mas podemos testá-la em relação a um termo banido conhecido). Este exemplo pressupõe que você configurou a política para estar no modo imposto e adicionou termos zero à lista de senhas excluídas personalizada.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   De acordo com a documentação, como nosso teste foi uma operação de redefinição de senha, você deve ver um evento 10017 e 30005 para o usuário ContosoUser.

   O evento 10017 deve ser semelhante a este exemplo:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   O evento 30005 deve ser semelhante a este exemplo:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Isso foi divertido – vamos tentar outro exemplo! Desta vez, tentaremos definir uma senha que é banida pela lista de proibidos personalizada enquanto a política está no modo de auditoria. Este exemplo pressupõe que você concluiu as seguintes etapas: configurada a política para estar no modo de auditoria, adicionou o termo "LACHRYMOSE" à lista de senhas excluídas personalizada e sincronizou a nova política resultante para o controlador de domínio, alternando o serviço de agente de DC conforme descrito acima.

   Ok, defina uma variação da senha banida:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Lembre-se, desta vez, com êxito porque a política está em modo de auditoria. Você deverá ver um evento 10025 e 30007 para o usuário ContosoUser.

   O evento 10025 deve ser semelhante a este exemplo:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   O evento 30007 deve ser semelhante a este exemplo:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Continue testando várias senhas de sua escolha e verificando os resultados no Visualizador de eventos usando os procedimentos descritos nas etapas anteriores. Se você precisar alterar a política na portal do Azure, não se esqueça de sincronizar a nova política com o agente de DC, conforme descrito anteriormente.

Abordamos procedimentos que permitem que você faça testes controlados do comportamento de validação de senha da proteção de senha do Azure AD. Redefinir senhas de usuário da linha de comando diretamente em um controlador de domínio pode parecer um meio estranho de fazer esses testes, mas conforme descrito anteriormente, ele foi projetado para produzir resultados repetíveis. Conforme você está testando várias senhas, mantenha o [algoritmo de avaliação de senha](concept-password-ban-bad.md#how-are-passwords-evaluated) em mente, pois pode ajudar a explicar os resultados que você não esperava.

> [!WARNING]
> Quando todos os testes forem concluídos, não se esqueça de excluir nenhuma conta de usuário criada para fins de teste!

## <a name="additional-content"></a>Conteúdo adicional

Os links a seguir não fazem parte da principal documentação da Proteção de Senha do Azure AD, mas podem ser uma fonte útil de informações adicionais sobre o recurso.

[A proteção de senha do Azure AD já está disponível para o público geral!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guia de proteção por email phishing – parte 15: implemente o serviço de proteção de senha Microsoft Azure AD (para o local também!)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[O Bloqueio Inteligente e a Proteção por senha do Azure AD agora estão em Versão Prévia Pública!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Treinamento em suporte da Microsoft Premier\Unified disponível

Se você estiver interessado em aprender mais sobre a proteção por senha do Azure AD e como implantá-la em seu ambiente, poderá aproveitar um serviço proativo da Microsoft disponível para clientes com um contrato de suporte Premier ou Unified. O serviço é chamado de Azure Active Directory: proteção por senha. Para obter mais informações, entre em contato com seu Gerente Técnico de Conta.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma pergunta sobre proteção de senha do Azure AD local que não foi respondida aqui, envie um item de Comentário abaixo. Obrigado!

[Implantar proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)