---
title: Perguntas frequentes sobre o Azure Active Directory | Microsoft Docs
description: Este artigo responde perguntas frequentes sobre o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fa195fd8d40c6b67a9c446f0b2320fe47d75259
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092658"
---
# <a name="azure-active-directory-connect-faq"></a>Perguntas frequentes do Azure Active Directory Connect

## <a name="general-installation"></a>Instalação geral

**P: Como posso fortalecer meu servidor do Azure AD Connect para diminuir a superfície de ataque de segurança?**

A Microsoft recomenda fortalecer o servidor do Azure AD Connect para diminuir a superfície de ataque de segurança desse componente crítico de seu ambiente de TI.  Seguindo as recomendações abaixo, os riscos de segurança para sua organização serão reduzidos.

* Implantar o Azure AD Connect em um servidor ingressado no domínio e restringir o acesso administrativo a administradores de domínio ou a outros grupos de segurança rigidamente controlados

Para obter mais informações, consulte: 

* [Como proteger grupos de administradores](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Como proteger contas de administrador interno](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Aprimoramento e manutenção da segurança reduzindo as superfícies de ataque](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Como reduzir a superfície de ataque do Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**P: A instalação funcionará se o Administrador Global do Azure Active Directory (Azure AD) tiver a autenticação de dois fatores (2FA) habilitada?**  
A partir das compilações de fevereiro de 2016, este cenário é suportado.

**P: Existe uma forma de instalar o Azure AD Connect autônomo?**  
Instalação do Azure AD Connect é suportada apenas quando você usa o Assistente de instalação. Não há suporte para uma instalação silenciosa e autônoma.

**P: Tenho uma floresta em que um domínio não pode ser contatado. Como instalo o Azure AD Connect?**  
A partir das compilações de fevereiro de 2016, este cenário é suportado.

**P: O agente de integridade do Azure Active Directory Domain Services (Azure AD DS) funciona no núcleo do servidor?**  
Sim. Depois de instalar o agente, você pode concluir o processo de registro usando o seguinte cmdlet do PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**P: O Azure AD Connect é compatível com a sincronização de dois domínios a um Azure AD?**  
Sim, há suporte para esse cenário. Consulte [Vários domínios](how-to-connect-install-multiple-domains.md).
 
**P: É possível ter vários conectores para o mesmo domínio do Active Directory no Azure AD Connect?**  
Não há suporte para nenhum, vários conectores para o mesmo domínio do AD. 

**P: Posso mover o banco de dados do Azure AD Connect do banco de dados local para uma instância remota do SQL Server?**    
Sim, as etapas a seguir fornecem diretrizes gerais sobre como fazer isso. Estamos trabalhando atualmente em um documento mais detalhado.
1. Fazer backup de banco de dados ADSync do LocalDB.
A maneira mais simples de fazer isso é usar o SQL Server Management Studio instalado no mesmo computador que o Azure AD Connect. Conectar-se ao *(LocalDb).\ADSync*, e, em seguida, fazer backup de banco de dados ADSync.

2. Restaurar o banco de dados "ADSync" para a instância remota do SQL.

3. Instalar o Azure AD Connect no existente [banco de dados SQL remoto](how-to-connect-install-existing-database.md).
   O artigo demonstra como migrar para o uso de um banco de dados SQL local. Se você estiver migrando para um banco de dados SQL remoto, na etapa 5 do processo, também será necessário inserir uma conta de serviço existente na qual o serviço Windows Sync será executado. Essa conta de serviço do mecanismo de sincronização é descrita aqui:
   
      **Usar uma conta de serviço existente**: Por padrão, o Azure AD Connect usa uma conta de serviço virtual para os serviços de sincronização a serem usados. Se você usa uma instância remota do SQL Server ou usa um proxy que exija autenticação, use uma conta de serviço gerenciado ou uma conta de serviço no domínio e saber a senha. Nesses casos, insira a conta a usar. Certifique-se de que os usuários que estão executando a instalação sejam administradores de sistema no SQL, para que as credenciais de login da conta de serviço possam ser criadas. Para saber mais, confira [Contas e permissões do Azure AD Connect](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Com a compilação mais recente, o provisionamento do banco de dados agora pode ser executado fora da banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário de banco de dados. Para obter mais informações, confira [Instale o Azure AD Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md).

Para manter as coisas simples, é recomendável que os usuários que instalam o Azure AD Connect ser administradores do sistema no SQL. No entanto, com compilações recentes, agora é possível usar administradores SQL delegados, conforme descrito em [ Instalar o Azure AD Connect usando permissões de administrador delegadas do SQL ](how-to-connect-install-sql-delegation.md).

**P: Quais são algumas das melhores práticas de campo?**  

Este é um documento informativo que apresenta algumas das melhores práticas que a engenharia, o suporte e os nossos consultores desenvolveram ao longo dos anos.  Isso é apresentado em uma lista com marcadores que pode ser referenciada rapidamente.  Embora essa lista busque ser abrangente, pode haver mais melhores práticas que talvez ainda não façam parte dela.

- Se estiver usando o SQL completo, ele deverá permanecer local versus remoto
    - Menos saltos
    - Mais fácil de solucionar problemas
    - Menos complexidade
    - É necessário designar recursos para o SQL e permitir a sobrecarga para o Azure AD Connect e o sistema operacional
- Ignorar proxy se possível; se não for possível ignorar o proxy, você precisará garantir que o valor de tempo limite seja maior que cinco minutos.
- Se o proxy for necessário, você deverá adicionar o proxy ao arquivo machine.config
- Esteja atento aos trabalhos e à manutenção locais do SQL e no impacto deles sobre o Azure AD Connect, especialmente quanto à reindexação
- Assegure-se de que o DNS pode ser resolvido externamente
- Assegure-se de que as [especificações do servidor](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) são por recomendação, esteja você usando servidores físicos ou virtuais
- Assegure-se de que você está usando um servidor virtual ao qual os recursos necessários são dedicados
- Verifique se você tem o disco e a configuração de disco que atendem melhor às práticas recomendadas para o SQL Server
- Instalar e configurar o Azure AD Connect Health para monitoramento
- Use o limite de exclusão interno do Azure AD Connect.
- Examine cuidadosamente as atualizações de versão a serem preparadas para todas as alterações e novos atributos que possam ser adicionados
- Faça backup de tudo
    - Faça backup de chaves
    - Faça backup de regras de sincronização
    - Faça backup da configuração do servidor
    - Faça backup do Banco de Dados SQL
- Verifique se não há agentes de backup de terceiros que estejam fazendo backup do SQL sem o gravador VSS do SQL (comum em servidores virtuais com instantâneos de terceiros)
- Limite a quantidade de regras de sincronização personalizadas que são usadas à medida que adicionam complexidade
- Trate servidores do Azure AD Connect como servidores de camada 0
- Tenha muita cautela ao modificar regras de sincronização de nuvem se não tiver uma compreensão abrangente do impacto e dos fatores comerciais corretos
- Verifique se as portas da URL e do firewall corretas estão abertas para suporte do Azure AD Connect e do Azure AD Connect Health
- Aproveite o atributo de nuvem filtrado para solucionar problemas e impedir objetos fantasmas
- Com o servidor de preparo, verifique se você está usando o Documentador de Configuração do Azure AD Connect quanto à consistência entre os servidores
- Os servidores de preparo devem estar em datacenters (locais físicos) separados
- Os servidores de preparo não são destinados a ser uma solução de alta disponibilidade, mas você pode ter vários servidores de preparo
- A introdução de servidores de preparo de "retardo" pode mitigar algum tempo de inatividade em potencial em caso de erro
- Testar e validar todas as atualizações no servidor de preparo primeiro
- Sempre valide as exportações antes de passar para o servidor de preparo.  Aproveite o servidor de preparo para obter importações completas e sincronizações completas a fim de reduzir o impacto nos negócios
- Mantenha o máximo possível de consistência de versão entre servidores do Azure AD Connect 

**P: Posso permitir que Azure AD Connect crie a conta de conector do Azure AD no computador do grupo de trabalho?**
Não.  Para permitir que Azure AD Connect crie automaticamente a conta do conector do Azure AD, o computador precisa estar ingressado no domínio.  

## <a name="network"></a>Rede
**P: Tenho um firewall, um dispositivo de rede ou outro item que limita o tempo que as conexões podem permanecer abertas na minha rede. O que meu limite de tempo limite do lado do cliente deve ser quando usa o Azure AD Connect?**  
Todos os softwares de rede, dispositivos físicos ou qualquer outra coisa que limite o tempo máximo que as conexões podem permanecer abertas deve usar um limiar de pelo menos 300 minutos (300 segundos) para conectividade entre o servidor no qual o cliente do Azure AD Connect está instalado e o Active Directory do Azure. Esta recomendação também se aplica a todas as ferramentas de sincronização do Microsoft Identity lançadas anteriormente.

**P: Há suporte para SLDs (domínios de rótulo único)?**  
Embora seja altamente não usar essa configuração de rede ([consulte o artigo](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), o uso da sincronização do Azure AD Connect com um único domínio de rótulo é compatível, desde que a configuração de rede para o domínio de nível único esteja funcionando corretamente.

**P: Há suporte para florestas com domínios do AD não contíguos?**  
Não, o Azure AD Connect não oferece suporte a florestas locais que contêm namespaces contíguos.

**P: Há suporte para nomes NetBios com pontos?**  
Não, o Azure AD Connect não oferece suporte a florestas/domínios locais em que o nome NetBios contém um ponto (.) no nome.

**P: Há suporte para ambiente puro do IPv6?**  
Não, o Azure AD Connect não oferece suporte ao ambiente puro do IPv6.

**P: Eu tenho um ambiente de várias florestas e a rede entre as duas florestas está usando NAT (Network Address Translation). Está usando o Azure AD Connect entre essas duas florestas com suporte?**</br>
Não, o uso do Azure AD Connect via NAT não é suportado. 

## <a name="federation"></a>Federação
**P: o que devo fazer se eu receber um email que me peça para renovar o certificado de Microsoft 365?**  
Para obter orientação sobre como renovar o certificado, consulte [renovar certificados](how-to-connect-fed-o365-certs.md).

**P: Eu tenho "atualizar automaticamente a terceira parte confiável" definida para o Microsoft 365 terceira parte confiável. É necessário realizar qualquer ação quando meu certificado de autenticação de tokens passa automaticamente?**  
Use as diretrizes descritas no artigo [Renovar certificados](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: É possível renomear o servidor após a instalação do Azure AD Connect?**  
Não. Alterar o nome do servidor torna o mecanismo de sincronização incapaz de se conectar à instância do banco de dados SQL e o serviço não pode ser iniciado.

**P: Regras de sincronização do NGC (criptografia de próxima geração) são compatíveis com computadores habilitados para FIPS?**  
Não.  Elas não são compatíveis.

**P. Se eu desabilito um dispositivo sincronizado (por exemplo: HAADJ) no portal do Azure, por que ele é habilitado novamente?**<br>
Dispositivos sincronizados podem ser criados ou controlados localmente. Se um dispositivo sincronizado for habilitado localmente, ele poderá ser habilitado novamente no portal do Azure mesmo que tenha sido desabilitado anteriormente por um administrador. Para desabilitar um dispositivo sincronizado, use o Active Directory local para desabilitar a conta do computador.

**Perguntas. Se eu bloquear a entrada do usuário na Microsoft 365 ou no portal do AD do Azure para usuários sincronizados, por que ele será desbloqueado após entrar novamente?**<br>
Os usuários sincronizados podem ser criados ou controlados localmente. Se a conta estiver habilitada localmente, ela poderá desbloquear o bloqueio de entrada imposto pelo administrador.

## <a name="identity-data"></a>Dados de identidade
**P: Por que o atributo userPrincipalName (UPN) do Azure AD não corresponde ao UPN local?**  
Para obter informações, consulte estes artigos:

* [Os nomes de acesso no Microsoft 365, no Azure ou no Intune não correspondem ao UPN local ou à ID de logon alternativa](https://mskb.pkisolutions.com/kb/2523192)
* [As alterações não são sincronizadas pela ferramenta de sincronização do Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://mskb.pkisolutions.com/kb/2669550)

Você também pode configurar o Azure AD para permitir que o mecanismo de sincronização atualize da forma descrita em [Azure AD Connect sync service features (Recursos do serviço de sincronização do Azure AD Connect)](how-to-connect-syncservice-features.md).

**P: É possível fazer a correspondência flexível de um grupo ou objeto de contato local do Azure AD com um grupo ou objeto de contato existente do Azure AD?**  
Sim, essa correspondência flexível se baseará o proxyAddress. A correspondência suave não tem suporte para grupos que não são habilitados para email.

**P: É possível definir manualmente o atributo ImmutableId em um grupo ou objeto de contato existente do Azure AD para fazer a correspondência fixa com um grupo ou objeto de contato local do Azure AD?**  
Não, definindo manualmente o atributo de ImmutableId em um objeto de contato/grupo do AD do Azure existente para correspondência de disco rígida no momento não há suporte.

## <a name="custom-configuration"></a>Configuração personalizada
**P: Onde estão documentados os cmdlets do PowerShell para o Azure AD Connect?**  
Com exceção dos cmdlets documentados neste site, não há suporte para outros cmdlets do PowerShell encontrados no Azure AD Connect para uso do cliente.

**P: Posso usar a opção de "exportação/importação do servidor" encontrada no Synchronization Service Manager para mover a configuração entre servidores?**  
Não. Essa opção não irá recuperar todos os parâmetros de configuração e não deve ser usada. Em vez disso, use o assistente para criar a configuração básica no segundo servidor e usar o editor de regra de sincronização para gerar scripts do PowerShell para mover qualquer regra personalizada entre servidores. Para mais informações, consulte [Migração Swing](how-to-upgrade-previous-version.md#swing-migration).

**P: As senhas podem ser armazenadas em cache para a página de entrada do Azure? Esse cache pode ser bloqueado por conter um elemento de entrada de senha com o atributo *autocomplete = "false"* ?**  
Atualmente, modificar os atributos HTML do campo de entrada de **senha**, incluindo a marca de preenchimento automático, não há suporte. Estamos trabalhando em um recurso que permitirá Javascript personalizado, e permitirá que você adicione qualquer atributo ao campo de **Senha**.

**P: A página de entrada do Azure exibe os nomes dos usuários que se conectaram anteriormente com êxito. Esse comportamento pode ser desativado?**  
Atualmente, modificar os atributos HTML do campo de entrada de **senha**, incluindo a marca de preenchimento automático, não há suporte. Estamos trabalhando em um recurso que permitirá Javascript personalizado, e permitirá que você adicione qualquer atributo ao campo de **Senha**.

**P: Há uma forma de evitar sessões simultâneas?**  
Não.

## <a name="auto-upgrade"></a>Atualização automática

**P: Quais são as vantagens e consequências do uso da atualização automática?**  
É recomendável que todos os clientes habilitem a atualização automática para a instalação do Azure AD Connect. Os benefícios são que eles sempre receba os patches mais recentes, incluindo atualizações de segurança de vulnerabilidades que foram encontradas no Azure AD Connect. O processo de atualização é simples e ocorrerá automaticamente assim que uma nova versão estiver disponível. Muitos milhares de clientes do Azure AD Connect usam a atualização automática com cada nova versão.

O processo de atualização automática sempre primeiro estabelece se uma instalação está qualificada para atualização automática. Se for qualificado, a atualização é executada e testada. O processo também inclui procurando alterações personalizadas para regras e fatores ambientais específicos. Se os testes mostram que uma atualização é bem-sucedida, a versão anterior é restaurada automaticamente.

Dependendo do tamanho do ambiente, o processo pode levar algumas horas. Enquanto a atualização está em andamento, acontece sem sincronização entre o Windows Server Active Directory e o Azure AD.

**P: Recebi um email informando que a atualização automática não funciona mais e que preciso instalar uma nova versão. Por que preciso fazer isso?**  
No ano passado, lançamos um versão do Azure AD Connect que, em algumas situações, pode ter desabilitado o recurso de atualização automática no seu servidor. Corrigimos o problema no Azure AD Connect versão 1.1.750.0. Se você tiver sido afetado pelo problema, poderá atenuá-lo executando um script do PowerShell para repará-lo ou atualizando manualmente para a versão mais recente do Azure AD Connect. 

Para executar o script do PowerShell, [ baixe o script ](https://aka.ms/repairaadconnect) e execute-o no servidor do Azure AD Connect em uma janela administrativa do PowerShell. Para saber como executar o script [exibir este vídeo rápido](https://aka.ms/repairaadcau).

Para atualizar manualmente, é necessário baixar e executar a versão mais recente do arquivo AADConnect.msi.
 
-  Se sua versão atual for anterior à 1.1.750.0, [Baixe e atualize para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).
- Se sua versão do Azure AD Connect for 1.1.750.0 ou posterior, nenhuma ação adicional é necessária. Você já está usando a versão que contém a correção de atualização automática. 

**P: Recebi um email solicitando a atualização para a versão mais recente para habilitar novamente a atualização automática. Estou usando a versão 1.1.654.0. Preciso atualizar?**  
Sim, você precisa atualizar para a versão 1.1.750.0 ou posterior para habilitar novamente a atualização automática. [Faça o download e atualize para a versão mais recente](https://www.microsoft.com/download/details.aspx?id=47594).

**P: Recebi um email solicitando a atualização para a versão mais recente para habilitar novamente a atualização automática. Usei o PowerShell para habilitar a atualização automática, ainda preciso instalar a versão mais recente?**  
Sim, você ainda precisa atualizar para a versão 1.1.750.0 ou mais recente. Habilitar o serviço de atualização automática com o PowerShell não atenuará o problema encontrado na versão 1.1.750.0 e anteriores.

**P: Quero atualizar para uma versão mais recente, mas não sei quem instalou o Azure AD Connect e não tenho o nome de usuário e a senha. Preciso disso?**
Você não precisa saber o nome de usuário e a senha que foi usada inicialmente para atualizar o Azure AD Connect. Use qualquer conta do Azure AD que tenha a função de Administrador Global.

**P: Como descobrir a versão do Azure AD Connect que estou usando?**  
Para verificar qual versão do Azure AD Connect está instalada no servidor, vá ao Painel de Controle e procure a versão instalada do Microsoft Azure AD Connect selecionando **Programas** > **Programas e Recursos**, como mostrado aqui:

![Versão do Azure AD Connect no Painel de Controle](./media/reference-connect-faq/faq1.png)

**P: Como atualizar para a versão mais recente do Azure AD Connect?**  
Para saber como atualizar para a versão mais recente, consulte [Azure AD Connect: atualização de uma versão anterior para a mais recente](how-to-upgrade-previous-version.md). 

**P: Já atualizamos para a versão mais recente do Azure AD Connect no ano passado. Precisamos fazer a atualização novamente?**  
A equipe do Azure AD Connect faz atualizações frequentes para o serviço. Para se beneficiar de correções de bugs e atualizações de segurança, bem como novos recursos, é importante manter o servidor atualizado com a versão mais recente. Se você habilitar a atualização automática, sua versão do software é atualizado automaticamente. Para localizar o histórico de lançamento de versões do Azure AD Connect, consulte [Azure AD Connect: histórico de lançamento de versões](reference-connect-version-history.md).

**P: Quanto tempo demora para realizar a atualização, e qual é o impacto para os meus usuários?**  
O tempo necessário para atualizar depende do tamanho do seu locatário. Para organizações maiores, pode ser melhor realizar a atualização à noite ou no fim de semana. Durante a atualização nenhuma atividade de sincronização ocorre.

**P: Acredito que já atualizei para o Azure AD Connect, mas o portal do Office ainda menciona o DirSync. Por que é isso?**  
A equipe do Office está trabalhando para receber atualizações de portal do Office para refletir o nome do produto atual. Ele não reflete a ferramenta de sincronização que você está usando.

**P: Meu status de atualização automática informa: “Suspenso”. Por que está suspenso? Devo habilitá-lo?**  
Um bug foi introduzido em uma versão anterior que, em determinadas circunstâncias, deixa o status de atualização automática definido como "Suspenso". Manualmente, permitindo que ele seja tecnicamente possível, mas exige várias etapas complexas. A melhor coisa que você pode fazer é instalar a versão mais recente do Azure AD Connect.

**P: Minha empresa tem requisitos rigorosos de gerenciamento de alterações, e quero controlar quando eles são lançados. É possível controlar quando a atualização automática é iniciada?**  
Não há nenhum desses recursos hoje mesmo. O recurso está sendo avaliado para um lançamento futuro.

**P: Receberei um email se a atualização automática falhar? Como saberei se ela foi bem-sucedida?**  
Você não será notificado do resultado da atualização. O recurso está sendo avaliado para um lançamento futuro.

**P: Vocês publicam uma linha do tempo de quando planejam lançar atualizações automáticas?**  
Atualização automática é a primeira etapa no processo de lançamento de uma versão mais recente. Sempre que houver uma nova versão, as atualizações são enviadas automaticamente. As versões mais recentes do Azure AD Connect são pré-anunciadas no [Roteiro do Azure AD](../fundamentals/whats-new.md).

**P: A atualização automática também atualiza o Azure AD Connect Health?**  
Sim, atualização automática também atualiza o Azure AAD Connect Health.

**P: Vocês também fazem a atualização automática dos servidores do Azure AD Connect no modo de preparo?**  
Sim, você pode atualização automática um servidor de conexão do AD do Azure que está no modo de preparo.

**P: O que devo fazer se a atualização automática falhar e meu servidor do Azure AD Connect não iniciar?**  
Em casos raros, o serviço do Azure AD Connect não inicia após executar a atualização. Nesses casos, reinicialize o servidor, que geralmente corrige o problema. Se o serviço do Azure AD Connect não iniciar, abra um tíquete de suporte. Para obter mais informações, consulte [criar uma solicitação de serviço para contatar Microsoft 365 suporte](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support). 

**P: Não sei quais são os riscos de atualizar para uma versão mais recente do Azure AD Connect. Vocês podem me ligar para ajudar com a atualização?**  
Se precisar de ajuda para atualizar para uma versão mais recente do Azure AD Connect, abra um tíquete de suporte em [criar uma solicitação de serviço para entrar em contato com o suporte Microsoft 365](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support).

## <a name="operational-best-practice"></a>Prática recomendada operacional    
Abaixo estão algumas práticas recomendadas que você deve implementar ao sincronizar entre o Windows Server Active Directory e o Azure Active Directory.

**Aplicar a autenticação multifator para todas as contas sincronizadas** A autenticação multifator do Azure AD ajuda a proteger o acesso a dados e aplicativos, mantendo a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de métodos de autenticação fáceis de usar. Os usuários podem ou não ser desafiados para MFA com base em decisões de configuração tomadas por um administrador. Você pode ler mais sobre o MFA aqui: https://www.microsoft.com/security/business/identity/mfa?rtc=1

**Siga as diretrizes de segurança do Azure ad Connect Server** O servidor de Azure AD Connect contém dados de identidade críticos e deve ser tratado como um componente da camada 0, conforme documentado no [modelo de camada administrativa Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material). Consulte também nossas [diretrizes para proteger seu servidor AADConnect](./how-to-connect-install-prerequisites.md#azure-ad-connect-server).

**Habilitar PHS para detecção de credenciais vazadas** A sincronização de hash de senha também permite a [detecção de credenciais vazadas](../identity-protection/concept-identity-protection-risks.md) para suas contas híbridas. A Microsoft trabalha juntamente com pesquisadores da dark Web e autoridades competentes para encontrar pares de nome de usuário/senha disponíveis publicamente. Se qualquer um desses pares corresponder àqueles de seus usuários, a conta associada será movida para alto risco. 


## <a name="troubleshooting"></a>Solução de problemas
**P: Como posso obter ajuda com o Azure AD Connect?**

[Pesquise a Base de Dados de Conhecimento (KB) Microsoft](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Pesquise a KB para soluções técnicas de conserto de problemas comuns sobre o suporte do Azure AD Connect.

[Página de perguntas de P e R da Microsoft para o Azure Active Directory](/answers/topics/azure-active-directory.html)

* Pesquisar perguntas e respostas técnicas ou fazer suas próprias perguntas, acessando [a comunidade do Azure AD](/answers/topics/azure-active-directory.html).

[Obter suporte para o Azure AD](../fundamentals/active-directory-troubleshooting-support-howto.md)

**P: Por que estou vendo os eventos 6311 e 6401 ocorrerem após erros de etapa de sincronização?**

Os eventos 6311 – **O servidor encontrou um erro inesperado ao executar um retorno de chamada** e 6401 – **O controlador do agente de gerenciamento encontrou um erro inesperado** são sempre registrados após um erro de etapa de sincronização. Para resolver esses erros, você precisa limpar os erros de etapa de sincronização.  Para obter mais informações, confira [Solucionar erros durante a sincronização](tshoot-connect-sync-errors.md) e [Solucionar problemas de sincronização de objeto com a sincronização do Azure AD Connect](tshoot-connect-objectsync.md)