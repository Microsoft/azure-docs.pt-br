---
title: "Solução de problemas: SSPR do Azure AD | Microsoft Docs"
description: "Solução de problemas de autoatendimento de redefinição de senha do Azure AD"
services: active-directory
keywords: 
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
ms.date: 09/21/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: d33e516628c56a7aa038e37b4498461de17f8433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-self-service-password-reset"></a>Como resolver problemas de autoatendimento de redefinição de senha

Se você estiver tendo problemas com o autoatendimento de redefinição de senha, os itens a seguir poderão ajudá-lo a resolver tudo rapidamente.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-may-see"></a>Como solucionar problemas de erros de redefinição de senha de autoatendimento que um usuário pode ver

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Desculpe, <br> você não pode redefinir sua senha neste momento porque o administrador desabilitou a redefinição de senha para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite a habilitação deste recurso. Para saber mais, leia [Preciso de ajuda, esqueci a minha senha do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: detectamos que a redefinição de senha não foi habilitada pelo administrador. Entre em contato com o seu administrador e solicite a habilitação da redefinição de senha para a sua organização. |
| WritebackNotEnabled = 10 |Desculpe, <br> você não pode redefinir sua senha neste momento porque o administrador não habilitou um serviço necessário para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a verificação da configuração da sua organização. Para saber mais sobre este serviço necessário, leia [Como configurar o write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configuring-password-writeback). | SSPR_0010: detectamos que o write-back de senha não foi habilitado. Entre em contato com o seu administrador e solicite a habilitação do write-back de senha. |
| SsprNotEnabledInUserPolicy = 11 | Desculpe,  <br> você não pode redefinir sua senha neste momento porque o administrador não configurou a redefinição de senha para sua organização. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a configuração da redefinição de senha. Para saber mais sobre a configuração de redefinição de senha, leia o artigo [Início rápido: redefinição da senha de autoatendimento do Azure AD ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: sua organização não tem uma política de redefinição de senha definida. Entre em contato com seu administrador e solicite a implantação de uma política de redefinição de senha. |
| UserNotLicensed = 12 | Desculpe, <br> você não pode redefinir sua senha neste momento porque a sua organização não tem as licenças necessárias. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite que verifique a atribuição de licença. Para ler mais sobre requisitos de licenciamento, veja o artigo [Requisitos de licenciamento para redefinição da senha de autoatendimento do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: a sua organização não tem as licenças necessárias para executar a redefinição de senha. Entre em contato com seu administrador e solicite que revise as atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Desculpe, <br> você não pode redefinir sua senha neste momento porque o administrador não configurou sua conta para usar a redefinição de senha. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite a configuração da sua conta para redefinição de senha. Para saber mais sobre a configuração de conta para redefinição de senha, leia o artigo [Como executar a redefinição de senha para usuários](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: você não é um membro de um grupo habilitado para redefinição de senha. Entre em contato com o administrador e peça para ser adicionado ao grupo. |
| UserNotProperlyConfigured = 14 | Desculpe, <br> você não pode redefinir sua senha neste momento porque a sua conta não tem as informações necessárias. Não há mais ações para solucionar esta situação. Entre em contato com o seu administrador e solicite a redefinição da sua senha. Depois de ter acesso à sua conta novamente, registre as informações necessárias seguindo as etapas do artigo [Como inscrever-se na redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register). | SSPR_0014: para redefinir a sua senha, são necessárias informações de segurança adicionais. Para continuar, entre em contato com o seu administrador e solicite a redefinição da sua senha. Depois que ter acesso à sua conta, registre as informações de segurança adicionais em https://aka.ms/ssprsetup. O administrador pode adicionar informações de segurança adicionais à sua conta seguindo as etapas em [Configuração e leitura de dados de autenticação para redefinição de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Desculpe, <br> não é possível redefinir a sua senha no momento devido a um problema com a configuração de redefinição de senha da sua organização. Não há mais ações para solucionar esta situação. Entre em contato com seu administrador e solicite uma investigação. Para saber mais sobre o possível problema em potencial, leia o artigo [Solução de problemas de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: não é possível redefinir sua senha devido a um erro em sua configuração local. Entre em contato com seu administrador e solicite uma investigação. |
| OnPremisesConnectivityError = 30 | Desculpe, <br> não é possível redefinir a sua senha no momento devido a problemas de conectividade para a sua organização. Não há nenhuma ação a ser executada no momento, mas o problema pode ser resolvido se você tentar novamente mais tarde. Se o problema persistir, entre em contato com o seu administrador e solicite uma investigação. Para saber mais sobre problemas de conectividade, leia [Solução de problemas de conectividade de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: não é possível redefinir a sua senha devido a uma conexão ruim com seu ambiente local. Entre em contato com seu administrador e solicite uma investigação.|


## <a name="troubleshoot-password-reset-configuration-in-the-azure-portal"></a>Solução de problemas de configuração de redefinição de senha no portal do Azure

| **Erro** | Solução |
| --- | --- |
| Não consigo ver a seção **Redefinição de Senha** no Azure AD no portal do Azure | Isso poderá ocorrer se você não tiver uma licença do Azure AD Premium ou Básico atribuída ao administrador que realiza a operação. <br> Isso pode ser resolvido com a atribuição de uma licença à conta de administrador em questão usando o artigo [Atribuir, verificar e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Não consigo ver uma opção de configuração específica | Vários elementos da interface do usuário ficam ocultos até que sejam necessários. Tente habilitar todas as opções que você deseja ver. |
| Não consigo ver a guia **Integração local** | Essa opção só fica visível se você baixou o Azure AD Connect e configurou o write-back de senha. Para obter mais informações sobre esse tópico, consulte o artigo [Introdução ao Azure AD Connect usando configurações expressas](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Solução de problemas de relatório de redefinição de senha

| **Erro** | Solução |
| --- | --- |
| Não consigo ver nenhum tipo de atividade de gerenciamento de senhas na categoria de evento de auditoria **Gerenciamento de Senhas de Autoatendimento** | Isso poderá ocorrer se você não tiver uma licença do Azure AD Premium ou Básico atribuída ao administrador que realiza a operação. <br> Isso pode ser resolvido com a atribuição de uma licença à conta de administrador em questão usando o artigo [Atribuir, verificar e resolver problemas com licenças] |
| Os registros do usuário aparecem várias vezes | Atualmente, quando um usuário se registra, registramos cada dado individual que é registrado como um evento separado. <br> Se você quiser agregar esses dados, pode baixar o relatório e abrir os dados como uma tabela dinâmica no Excel para ter maior flexibilidade.

## <a name="troubleshoot-password-reset-registration-portal"></a>Solução de problemas do portal de registro de redefinição de senha

| **Erro** | Solução |
| --- | --- |
| O diretório não está habilitado para a redefinição de senha **O administrador não permitiu que você use esse recurso** | Alterne o sinalizador **Autoatendimento de redefinição de senha habilitado** para **Um grupo** ou **Todos** e clique em **Salvar** |
| O usuário não tem uma licença do Azure AD Premium ou Básico atribuída **O administrador não permitiu que você use esse recurso** | Isso poderá ocorrer se você não tiver uma licença do Azure AD Premium ou Básico atribuída ao administrador que realiza a operação. <br> Isso pode ser resolvido com a atribuição de uma licença à conta de administrador em questão usando o artigo [Atribuir, verificar e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Erro ao processar solicitação | Isso pode ser causado por vários problemas, mas, em geral, esse erro é causado por uma interrupção do serviço ou um problema de configuração. Se você receber esse erro e ele estiver afetando seus negócios, contate o suporte da Microsoft para obter assistência adicional. |

## <a name="troubleshoot-password-reset-portal"></a>Solução de problemas do portal de redefinição de senha

| **Erro** | Solução |
| --- | --- |
| O diretório não está habilitado para redefinição de senha. | Alterne o sinalizador **Autoatendimento de redefinição de senha habilitado** para **Um grupo** ou **Todos** e clique em **Salvar** |
| O usuário não tem uma licença do Azure AD Premium ou Básico atribuída | Isso poderá ocorrer se você não tiver uma licença do Azure AD Premium ou Básico atribuída ao administrador que realiza a operação. <br> Isso pode ser resolvido com a atribuição de uma licença à conta de administrador em questão usando o artigo [Atribuir, verificar e resolver problemas com licenças](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| O diretório está habilitado para redefinição de senha, mas o usuário tem informações de autenticação ausentes ou incorretas | Verifique se o usuário preencheu corretamente os dados de contato no arquivo no diretório antes de continuar. Para obter mais informações sobre esse tópico, consulte o artigo [Dados usados pelo Autoatendimento de Redefinição de Senha do Azure AD](active-directory-passwords-data.md). |
| O diretório está habilitado para redefinição de senha, mas o usuário tem somente uma parte dos dados de contato no arquivo quando a política está definida para exigir duas etapas de verificação | Verifique se o usuário tem pelo menos dois métodos de contato configurados corretamente (Exemplo: Telefone Celular **e** Telefone Comercial) antes de continuar. |
| O diretório está habilitado para redefinição de senha e o usuário está configurado corretamente, mas não pode ser contatado | Isso pôde ser o resultado de um erro de serviço temporário ou de dados de contato incorretos que não pudemos detectar corretamente. <br> <br> Se o usuário aguardar 10 segundos, aparecerá um link tente novamente e "entre em contato com seu administrador". Clicar em Tentar Novamente faz uma nova chamada, enquanto clicar em “Contatar o administrador” envia um formulário de email para os administradores solicitando que uma redefinição de senha seja realizada para essa conta de usuário. |
| O usuário nunca recebe a chamada telefônica ou redefinição de senha de SMS | Isso pode ser o resultado de um número de telefone incorreto no diretório. Verifique se o número de telefone está no formato "+ ccc xxxyyyzzzzXeeee". <br> <br> A redefinição de senha não dá suporte a extensões, mesmo se você especificar uma no diretório (elas serão removidas antes que a chamada seja despachada). Tente usar um número sem uma extensão ou integrar a extensão ao número de telefone no seu PBX. |
| O usuário nunca receber o email de redefinição de senha | A causa mais comum desse problema é que a mensagem foi rejeitada por um filtro de spam. Verifique o email no spam, na pasta Lixo eletrônico ou nos itens de email excluídos. <br> Além disso, verifique o email correto da mensagem. |
| Defini uma política de redefinição de senha, mas quando uma conta de administrador usa a redefinição de senha, a política não é aplicada | A Microsoft gerencia e controla a política de redefinição de senha do administrador para garantir o nível mais alto de segurança. |
| O usuário foi impedido de tentar a redefinição de senha muitas vezes em um mesmo dia | Implementamos um mecanismo de limitação automático para impedir que os usuários tentem redefinir suas senhas muitas vezes em um curto período de tempo. Isso ocorre quando: <br> 1. O usuário tenta validar um número de telefone cinco vezes em uma hora. <br> 2. O usuário tenta usar o portão de perguntas de segurança cinco vezes em uma hora. <br> 3. O usuário tenta redefinir uma senha para a mesma conta de usuário cinco vezes em uma hora. <br> Para corrigir isso, instrua o usuário a aguardar 24 horas após a última tentativa para poder redefinir a senha. |
| O usuário vê um erro ao validar seu número de telefone | Esse erro ocorre quando o número de telefone digitado não coincide com o número de telefone no arquivo. Verifique se o usuário está inserindo o número de telefone completo, incluindo o código de área e país, ao tentar usar um método baseado em telefone para a redefinição de senha. |
| Erro ao processar solicitação | Isso pode ser causado por vários problemas, mas, em geral, esse erro é causado por uma interrupção do serviço ou um problema de configuração. Se você receber esse erro e ele estiver afetando seus negócios, contate o suporte da Microsoft para obter assistência adicional. |

## <a name="troubleshoot-password-writeback"></a>Solução de problemas de write-back de senha

| **Erro** | Solução |
| --- | --- |
| O serviço de redefinição de senha não é iniciado no local com erro 6800 no log de eventos de aplicativo do Azure AD Connect do computador. <br> <br> Após a integração, os usuários federados ou sincronizados com hash de senha não conseguem redefinir suas senhas. | Quando o Write-back de Senha está habilitado, o mecanismo de sincronização chama a biblioteca de write-back para realizar a configuração (integração) comunicando-se com o serviço de integração em nuvem. Todos os erros encontrados durante a integração ou ao iniciar o ponto de extremidade do WCF para o Write-back de Senha resultarão em erros no Log de eventos e no log de eventos do Azure AD Connect do computador. <br> <br> Durante a reinicialização do serviço ADSync, se o write-back tiver sido configurado, o ponto de extremidade do WCF será inicializado. No entanto, se a inicialização do ponto de extremidade falhar, registraremos o evento 6800 e permitiremos a inicialização do serviço de sincronização. A presença desse evento significa que o write-back de senha de ponto de extremidade não foi iniciado. Os detalhes do log de eventos desse evento (6800) juntamente com as entradas do log de eventos geradas pelo componente PasswordResetService indicam o motivo pelo qual o ponto de extremidade não pôde ser inicializado. Examine esses erros do log de eventos e tente reiniciar o Azure AD Connect se o Write-back de Senha não estiver funcionando. Se o problema persistir, tente desabilitar e reabilitar o write-back de senha.
| Quando um usuário tenta redefinir uma senha ou desbloquear uma conta com write-back de senha habilitada, a operação falha. <br> <br> Além disso, você vê um evento no log de eventos do Azure AD Connect contendo: "O mecanismo de sincronização retornou um erro hr=800700CE, message=O nome de arquivo ou a extensão é muito longo" após a operação de desbloqueio ocorrer. | Encontre a conta do Active Directory no Azure AD Connect e redefina a senha com até 127 caracteres. Em seguida, abra o Serviço de Sincronização no menu Iniciar. Navegue para Conectores e localize o Active Directory Connector. Selecione-o e clique em Propriedades. Navegue para a página Credenciais e insira a nova senha. Selecione OK para fechar a página. |
| Na última etapa do processo de instalação do Azure AD Connect, você verá um erro indicando que não foi possível configurar o write-back de senha. <br> <br> O log de eventos do aplicativo Azure AD Connect contém erro 32009 com texto "Erro ao obter token de autenticação". | Esse erro ocorre nos seguintes casos:<br> <br> a. Você especificou uma senha incorreta para a conta de administrador global indicada no início do processo de instalação do Azure AD Connect.<br> b. Você tentou usar um usuário federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.<br> <br> Para corrigir esse erro, verifique se você não está usando uma conta federada para o administrador global que você indicou no início do processo de instalação do Azure AD Connect e se a senha especificada está correta. |
| O log de eventos do computador do Azure AD Connect contém o erro 32002 gerado pelo PasswordResetService. <br> <br> O erro é: "erro de conexão com o barramento de serviço; o provedor de token não pôde fornecer um token de segurança..." | O ambiente local não pode se conectar ao ponto de extremidade do barramento de serviço na nuvem. Esse erro normalmente é causado por uma regra de firewall que bloqueia uma conexão de saída com uma porta ou um endereço web específico. Consulte [Requisitos de rede](active-directory-passwords-how-it-works.md#network-requirements) para obter mais informações. Depois de atualizar essas regras, reinicialize o computador do Azure AD Connect e o write-back de senha deve começar a funcionar novamente. |
| Depois de trabalhar por algum tempo, os usuários federados ou sincronizados com hash de senha não conseguem redefinir suas senhas. | Em alguns casos raros, o serviço Write-back de Senha pode falhar ao reiniciar quando o Azure AD Connect é reiniciado. Nesses casos, primeiro verifique se o write-back de senha parece estar habilitado no local. Isso pode ser feito usando o Assistente do Azure AD Connect ou o PowerShell (confira a seção de tutoriais acima). Se o recurso parece estar habilitado, tente habilitar ou desabilitar o recurso novamente por meio da interface do usuário ou do PowerShell. Se isso não funcionar, tente desinstalar o Azure AD Connect por completo e reinstalá-lo. |
| Os usuários federados ou sincronizados com hash de senha que tentam redefinir suas senhas recebem um erro depois de enviar a senha, indicando que houve um problema de serviço. <br ><br> Além disso, durante as operações de redefinição de senha, você pode ver um erro de que o agente de gerenciamento teve acesso negado em seus logs de eventos no local. | Se você vir esses erros no log de eventos, confirme se a conta do AD MA (que foi especificada no assistente no momento da configuração) tem as permissões necessárias para o write-back de senha. <br> <br> **Depois que essa permissão é fornecida, pode levar até 1 hora para que as permissões sejam repassadas por meio da tarefa em segundo plano sdprop no controlador de domínio.** <br> <br> Para que a redefinição de senha funcione, a permissão deve ser marcada no descritor de segurança do objeto do usuário cuja senha esteja sendo redefinida. Até que essa permissão seja exibida no objeto do usuário, a redefinição de senha continuará falhando com acesso negado. |
| Os usuários federados ou sincronizados com hash de senha que tentam redefinir suas senhas recebem um erro depois de enviar a senha, indicando que houve um problema de serviço. <br> <br> Além disso, durante as operações de redefinição de senha, você verá um erro em seus logs de eventos do serviço do Azure AD Connect indicando o erro "Objeto não encontrado". | Esse erro geralmente indica que o mecanismo de sincronização não consegue encontrar o objeto de usuário no espaço do conector AAD ou o objeto de espaço de conector AD ou MV vinculado. <br> <br> Para resolver esse problema, verifique se o usuário está realmente sincronizado do local para o AAD pela instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços conectores e no MV. Confirme se o objeto do AD CS é conector para o objeto de MV através da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Os usuários federados ou sincronizados com hash de senha que tentam redefinir suas senhas recebem um erro depois de enviar a senha, indicando que houve um problema de serviço. <br> <br> Além disso, durante as operações de redefinição de senha, você poderá ver um erro nos logs de eventos do serviço Azure AD Connect indicando o erro “Várias correspondências encontradas”. | Isso indica que o mecanismo de sincronização detectou que o objeto de MV está conectado a mais de um objeto de AD CS através de "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o usuário tem uma conta habilitada em mais de uma floresta. **Não há suporte para o write-back de senha nesse cenário.** |
| Falha nas operações de senha com um erro de configuração. O log de eventos do aplicativo contém <br> <br> o erro 6329 do Azure AD Connect com o texto: 0x8023061f (A operação falhou porque a sincronização de senha não está habilitada neste Agente de Gerenciamento). | Isso ocorre se a configuração do Azure AD Connect é alterada para adicionar uma nova floresta do AD (ou para remover e adicionar novamente uma floresta existente) depois que o recurso Write-back de Senha já foi habilitado. As operações de senha para usuários em florestas recém-adicionadas como essas falharão. Para corrigir o problema, desabilite e reabilite o recurso de write-back de senha após a conclusão das alterações de configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro do log de eventos de write-back de senha

É uma prática recomendada para solucionar problemas com write-back de senha inspecionar esse Log de eventos do aplicativo no computador do Azure AD Connect. Esse log de eventos contém eventos de duas fontes de interesse para o Write-back de Senha. A fonte PasswordResetService descreve operações e problemas relacionados à operação do Write-back de Senha. A fonte ADSync descreve operações e problemas relacionados à configuração de senhas no ambiente do AD.

### <a name="source-of-event-is-adsync"></a>A origem do evento é ADSync

| Código | Nome/Mensagem | Descrição |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619 – "Uma restrição impede que a senha seja alterada para a atual especificada." | Esse evento ocorre quando o serviço Write-back de Senha tenta definir uma senha no diretório local que não atende à idade, ao histórico, à complexidade da senha ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado a senha recentemente nessa janela de tempo, não poderá alterar a senha novamente até que ela atinja a duração especificada no domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas X vezes, onde X é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas X vezes, verá uma falha. Para fins de teste, o histórico deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou de alteração falhará. |
| HR 8023042 | O Mecanismo de Sincronização retornou um erro hr = 80230402, mensagem= Houve uma falha na tentativa de obter um objeto, porque existem entradas duplicadas com a mesma âncora | Esse evento ocorre quando a mesma ID de usuário está habilitada em vários domínios. Por exemplo, se você estiver sincronizando florestas de Contas/Recursos e tiver a mesma ID de usuário presente e habilitada em cada uma delas, esse erro poderá ocorrer. <br> <br> Esse erro também pode ocorrer se você estiver usando um atributo de âncora não exclusivo (como alias ou UPN) e dois usuários compartilharem o mesmo atributo de âncora. <br> <br> Para resolver esse problema, certifique-se de que você não tem nenhum usuário duplicado em seus domínios e de que você está usando um atributo de âncora exclusivo para cada usuário. |

### <a name="source-of-event-is-passwordresetservice"></a>A origem do evento é PasswordResetService

| Código | Nome/Mensagem | Descrição |
| --- | --- | --- |
| 31001 | PasswordResetStart | Esse evento indica que o serviço local detectou uma solicitação de redefinição de senha de um usuário federado ou sincronizado com hash de senha proveniente da nuvem. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha. |
| 31002 | PasswordResetSuccess | Esse evento indica que o usuário selecionou uma nova senha durante uma operação de redefinição de senha; determinamos que essa senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente AD local. |
| 31003 | PasswordResetFail | Esse evento indica que um usuário selecionou uma senha e que a senha chegou com sucesso ao ambiente local, mas, quando tentamos definir a senha no ambiente do AD local, ocorreu uma falha. Isso pode ocorrer por diversos motivos: <br> <br> A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Tente uma senha totalmente nova para resolver esse problema. <br> <br> A conta de serviço MA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão. <br> <br> A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha. |
| 31004 | OnboardingEventStart | Esse evento ocorre se você habilitar o write-back de senha com o Azure AD Connect e indica que começamos a integração de sua organização para o serviço Web de write-back de senha. |
| 31005 | OnboardingEventSuccess | Esse evento indica que o processo de integração foi bem-sucedido e que o recurso de write-back de senha está pronto para uso. |
| 31006 | ChangePasswordStart | Esse evento indica que o serviço local detectou uma solicitação de alteração de senha de um usuário federado ou sincronizado com hash de senha proveniente da nuvem. Esse evento é o primeiro evento em cada operação de write-back de alteração de senha. |
| 31007 | ChangePasswordSuccess | Esse evento indica que o usuário selecionou uma nova senha durante uma operação de alteração de senha; determinamos que essa senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente AD local. |
| 31008 | ChangePasswordFail | Esse evento indica que um usuário selecionou uma senha e que a senha chegou com sucesso ao ambiente local, mas, quando tentamos definir a senha no ambiente do AD local, ocorreu uma falha. Isso pode ocorrer por diversos motivos: <br> <br> A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Tente uma senha totalmente nova para resolver esse problema. <br> <br> A conta de serviço MA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão. <br> <br> A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha. |
| 31009 | ResetUserPasswordByAdminStart | O serviço local detectou uma solicitação de redefinição de senha de um usuário federado ou sincronizado com hash de senha proveniente do administrador em nome de um usuário. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha iniciada pelo administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova senha durante uma operação de redefinição de senha; determinamos que essa senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente AD local. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma senha em nome de um usuário e a senha chegou com sucesso no ambiente local, mas, quando tentamos definir a senha no ambiente do AD local, ocorreu uma falha. Isso pode ocorrer por diversos motivos: <br> <br> A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou de filtragem para o domínio. Tente uma senha totalmente nova para resolver esse problema. <br> <br> A conta de serviço MA não tem as permissões apropriadas para definir a nova senha da conta de usuário em questão. <br> <br> A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de conjunto de senha. |
| 31012 | OffboardingEventStart | Esse evento ocorre se você desabilitar o write-back de senha com o Azure AD Connect e indica que começamos a desvinculação de sua organização do serviço Web de write-back de senha. |
| 31013| OffboardingEventSuccess| Esse evento indica que o processo de integração foi bem-sucedido e que o recurso de write-back de senha foi desabilitado com êxito. |
| 31014| OffboardingEventFail| Esse evento indica que o processo de desvinculação não foi bem-sucedido. Isso pode ser devido a um erro de permissões da conta de administrador em nuvem ou no local especificada durante a configuração, ou porque você está tentando usar um administrador global de nuvem federado ao desabilitar o write-back de senha. Para corrigir esse problema, verifique as suas permissões administrativas e se não está usando uma conta federada ao configurar o recurso de write-back de senha.|
| 31015| WriteBackServiceStarted| Esse evento indica que o serviço de write-back de senha foi iniciado com êxito e está pronto para aceitar solicitações de gerenciamento de senha da nuvem.|
| 31016| WriteBackServiceStopped| Esse evento indica que o serviço de write-back de senha foi interrompido e as solicitações de gerenciamento de senha da nuvem não serão bem-sucedidas.|
| 31017| AuthTokenSuccess| Esse evento indica que recuperamos com êxito um token de autorização para o administrador global especificado durante a instalação do Azure AD Connect para iniciar o processo de remoção ou integração.|
| 31018| KeyPairCreationSuccess| Esse evento indica que criamos com êxito a chave de criptografia de senha que é usada para criptografar senhas da nuvem a serem enviadas ao ambiente local.|
| 32000| UnknownError| Esse evento indica um erro desconhecido durante uma operação de gerenciamento de senha. Examine o texto da exceção no evento para obter mais detalhes. Se você estiver tendo problemas, tente desabilitar e reabilitar o write-back de senha. Se isso não ajudar, inclua uma cópia do seu log de eventos com a id de acompanhamento especificada internamente ao seu engenheiro de suporte.|
| 32001| ServiceError| Esse evento indica que houve um erro ao conectar ao serviço de redefinição de senha na nuvem e isso geralmente ocorre quando o serviço local não pôde se conectar ao serviço Web de redefinição de senha.|
| 32002| ServiceBusError| Esse evento indica que houve um erro ao conectar-se a instância do barramento de serviço do seu locatário. Isso pode acontecer porque você está bloqueando as conexões de saída no seu ambiente local. Verifique o firewall para permitir conexões via TCP 443 e a https://ssprsbprodncu-sb.accesscontrol.windows.net/ e tente novamente. Se você ainda estiver tendo problemas, tente desabilitar e reabilitar o write-back de senha.|
| 32003| InPutValidationError| Esse evento indica que a entrada passada para a API do nosso serviço Web era inválida. Tente a operação novamente.|
| 32004| DecryptionError| Esse evento indica que houve um erro ao descriptografar a senha que chegou da nuvem. Isso pode ser devido a uma falta de correspondência de chave de descriptografia entre o serviço de nuvem e o ambiente local. Para resolver isso, desabilite e habilite o Write-back de Senha novamente no ambiente local.|
| 32005| ConfigurationError| Durante a migração, nós salvamos informações específicas de locatário em um arquivo de configuração no seu ambiente local. Esse evento indica que houve um erro ao salvar esse arquivo ou que, quando o serviço foi iniciado, havia erro na leitura do arquivo. Para corrigir esse problema, tente desabilitar e habilitar o Write-back de Senha novamente para forçar uma reescrita desse arquivo de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante a migração, podemos enviar dados da nuvem para o serviço de redefinição de senha local. Esses dados são gravados em um arquivo na memória antes de serem enviados ao serviço de sincronização para armazená-los com segurança no disco. Esse evento indica um problema com a gravação ou a atualização dos dados na memória. Para corrigir esse problema, tente desabilitar e habilitar o Write-back de Senha novamente para forçar uma reescrita dessa configuração.|
| 32008| ValidationError| Esse evento indica que recebemos uma resposta inválida do serviço Web de redefinição de senha. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha.|
| 32009| AuthTokenError| Esse evento indica que não conseguimos obter um token de autorização para a conta de administrador global especificada durante a instalação do Azure AD Connect. Esse erro pode ser causado por um nome de usuário ou senha inválidos especificados para a conta de administrador global ou porque a conta de administrador global especificada é federada. Para corrigir esse problema, execute a configuração novamente com o nome de usuário e senha corretos e verifique se o administrador é uma conta gerenciada (somente em nuvem ou sincronizada por senha).|
| 32010| CryptoError| Esse evento indica que houve um erro ao gerar a chave de criptografia de senha ou ao descriptografar uma senha que chegou do serviço de nuvem. Esse erro provavelmente indica um problema com o seu ambiente. Examine os detalhes de seu log de eventos para saber mais e resolver esse problema. Você também pode tentar desabilitar e reabilitar o serviço de write-back de senha para resolver o problema.|
| 32011| OnBoardingServiceError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode ser devido a uma regra de firewall ou a um problema na obtenção de um token de autenticação para o seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída via TCP 443 e TCP 9350 9354 ou a https://ssprsbprodncu-sb.accesscontrol.windows.net/ e se a conta do administrador do AAD que você está usando para a integração não é federada.|
| 32013| OffBoardingError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de desvinculação. Isso pode ser devido a uma regra de firewall ou a um problema na obtenção de um token de autorização para o seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída via 443 ou a https://ssprsbprodncu-sb.accesscontrol.windows.net/ e se a conta do administrador do AAD que você está usando para a remoção não é federada.|
| 32014| ServiceBusWarning| Esse evento indica que tivemos que tentar novamente nos conectar à instância de barramento de serviço do locatário. Em condições normais, isso deve não ser uma preocupação, mas se você vir esse evento muitas vezes, considere verificar a sua conexão de rede para o barramento de serviço, especialmente se for uma conexão de largura de banda baixa ou de alta latência.|
| 32015| ReportServiceHealthError| Para monitorar a integridade do seu serviço de write-back de senha, podemos enviar dados de pulsação para o nosso serviço Web de redefinição de senha a cada cinco minutos. Esse evento indica que houve um erro ao enviar essas informações de integridade para o serviço Web em nuvem. Essas informações de integridade não incluem dados OII ou PII e é essencialmente uma pulsação e estatísticas básicas de serviço para que possamos fornecer informações de status do serviço na nuvem.|
| 33001| ADUnKnownError| Esse evento indica que foi retornado um erro desconhecido pelo AD; verifique no log de eventos do servidor Azure AD Connect eventos da fonte ADSync para saber mais sobre esse erro.|
| 33002| ADUserNotFoundError| Esse evento indica que o usuário que está tentando redefinir ou alterar uma senha não foi encontrado no diretório local. Isso pode ocorrer quando o usuário foi excluído no local, mas não na nuvem, ou se houver um problema com a sincronização. Verifique os logs de sincronização e os detalhes das últimas execuções de sincronizações para obter mais informações.|
| 33003| ADMutliMatchError| Quando uma solicitação de redefinição ou de alteração de senha são provenientes da nuvem, usamos a âncora de nuvem especificada durante a instalação do Azure AD Connect para determinar como vincular essa solicitação a um usuário no seu ambiente local. Esse evento indica que encontramos dois usuários em seu diretório local com o mesmo atributo de âncora de nuvem. Verifique os logs de sincronização e os detalhes das últimas execuções de sincronizações para obter mais informações.|
| 33004| ADPermissionsError| Esse evento indica que a conta de serviço do Agente de Gerenciamento não tem as permissões apropriadas na conta em questão para definir uma nova senha. Verifique se a conta MA na floresta do usuário tem permissões para Redefinir e Alterar senha em todos os objetos na floresta. Para obter mais informações sobre como fazer isso, consulte Etapa 4: Configurar as permissões apropriadas do Active Directory.|
| 33005| ADUserAccountDisabled| Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi desabilitada no local. Habilite a conta e tente a operação novamente.|
| 33006| ADUserAccountLockedOut| Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um usuário tiver tentado alterar ou redefinir a senha muitas vezes em um curto período de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| ADUserIncorrectPassword| Esse evento indica que o usuário especificou uma senha atual incorreta durante a operação de alteração de senha. Especifique a senha atual correta e tente novamente.|
| 33008| ADPasswordPolicyError| Esse evento ocorre quando o serviço Write-back de Senha tenta definir uma senha no diretório local que não atende à idade, ao histórico, à complexidade da senha ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado a senha recentemente nessa janela de tempo, não poderá alterar a senha novamente até que ela atinja a duração especificada no domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas X vezes, onde X é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas X vezes, verá uma falha. Para fins de teste, o histórico deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou de alteração falhará.|
| 33009| ADConfigurationError| Esse evento indica que houve um problema na gravação de senha no seu diretório local devido a um problema de configuração com o Active Directory. Verifique as mensagens do serviço ADSync no log de eventos de aplicativo do Azure AD Connect do computador para saber mais sobre o erro.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Solucionar problemas de conectividade de write-back de senha

Se você estiver enfrentando interrupções de serviço com o componente de write-back de senha do Azure AD Connect, abaixo estão algumas etapas rápidas que você pode seguir para resolver esse problema:

* [Verificar a conectividade de rede](#confirm-network-connectivity)
* [Reiniciar o serviço de sincronização do Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Desabilitar e reabilitar o recurso de write-back de senha](#disable-and-re-enable-the-password-writeback-feature)
* [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Solucionar problemas de write-back de senha](#troubleshoot-password-writeback)

Em geral, recomendamos que você execute essas etapas na ordem acima para recuperar o serviço da maneira mais rápida.

### <a name="confirm-network-connectivity"></a>Verificar a conectividade de rede

O ponto mais comum de falha é que o firewall e ou tempos-limite de ociosidade e portas de proxy estão configuradas incorretamente. Revise os requisitos de rede no artigo [autoatendimento redefinição de senha no AD deep dive do Azure](active-directory-passwords-how-it-works.md#network-requirements) para obter mais informações.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o serviço de sincronização do Azure AD Connect

A reinicialização do serviço de sincronização do Azure AD Connect pode ajudar a resolver problemas de conectividade ou outros problemas temporários com o serviço.

1. Como administrador, clique em **Iniciar** no servidor que executa o **Azure AD Connect**.
2. Digite **“services.msc”** na caixa de pesquisa e pressione **Enter**.
3. Procure a entrada **Microsoft Azure AD Sync**.
4. Com o botão direito do mouse na entrada do serviço, clique em **Reiniciar**e aguarde a conclusão da operação.

   ![Reiniciar o serviço Azure AD Sync][Service Restart]

Essas etapas restabelecerão a conexão com o serviço de nuvem e resolverão as interrupções que estão ocorrendo. Se a reinicialização do serviço de sincronização não resolver o seu problema, recomendamos que você tente desabilitar e reabilitar o recurso de write-back de senha como uma próxima etapa.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desabilitar e reabilitar o recurso de write-back de senha

Desabilitar e reabilitar o recurso de write-back de senha podem ajudar a resolver problemas de conectividade.

1. Como administrador, abra o **Assistente de configuração do Azure AD Connect**.
2. No diálogo **Conectar ao Azure AD**, insira suas **credenciais de administrador global do Azure AD**
3. No diálogo **Conectar-se ao AD DS**, insira suas **credenciais de administrador de Serviços de Domínio do AD**.
4. No diálogo **Identificar exclusivamente os seus usuários**, clique no botão **Avançar**.
5. Na caixa de diálogo **Recursos opcionais**, desmarque a caixa de seleção **Write-back de senha**.
6. Clique em **Avançar** nas páginas de diálogo restantes sem alterar nada até chegar à página **Pronto para configurar**.
7. Verifique se a página de configuração mostra a opção **Write-back de senha como desabilitada** e, depois, clique no botão verde **Configurar** para confirmar as alterações.
8. No diálogo **Concluído**, desmarque a opção **Sincronizar agora** e clique em **Concluir** para fechar o assistente.
9. Abra novamente o **assistente de configuração do Azure AD Connect**.
10. **Repita as etapas 2 a 8**, mas confirme se você **marcou a opção Write-back de senha** na tela **Recursos opcionais** para habilitar o serviço novamente.

Essas etapas restabelecerão a conexão com nosso serviço de nuvem e resolverão as interrupções que estão ocorrendo.

Se desabilitar e habilitar novamente o recurso Write-back de Senha não resolver o problema, recomendamos que você tente reinstalar o Azure AD Connect como uma próxima etapa.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente do Azure AD Connect

A reinstalação do Azure AD Connect pode resolver problemas de configuração e conectividade entre nossos serviços de nuvem e seu ambiente do AD local.

Recomendamos que você execute esta etapa somente após tentar as duas primeiras etapas descritas acima.

> [!WARNING]
> Se você personalizou as regras de sincronização integradas, **faça backup delas antes de continuar com a atualização e reimplante-as manualmente após a conclusão**.

1. Baixe a última versão do Azure AD Connect no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).
2. Como você instalou o Azure AD Connect, é necessário realizar uma atualização in-loco para atualizar sua instalação do Azure AD Connect para a última versão.
3. Execute o pacote baixado e siga as instruções na tela para atualizar o computador do Azure AD Connect.

Essas etapas restabelecerão a conexão com nosso serviço de nuvem e resolverão as interrupções que estão ocorrendo.

Se a instalação da última versão do servidor Azure AD Connect não resolver o problema, recomendamos que você tente desabilitar e habilitar novamente o Write-back de Senha como uma etapa final depois de instalar a última versão.

## <a name="verify-whether-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Verificar se o Azure AD Connect tem a permissão necessária para write-back de senha
O Azure AD Connect requer a permissão para **Redefinir Senha** do AD a fim de executar o write-back de senha. Para descobrir se o Azure AD Connect tem a permissão para uma determinada conta de Usuário do AD local, você pode usar o recurso de Permissão Efetiva do Windows:

1. Faça o logon no servidor do Azure AD Connect e inicie o **Synchronization Service Manager** (Iniciar → Serviço de Sincronização).
2. Na guia **Conectores**, selecione o **Conector AD** local e clique em **Propriedades**.  
![Permissão Efetiva – Etapa 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
3. Na caixa de diálogo pop-up, selecione a guia **Conectar-se à Floresta do Active Directory** e anote a propriedade **Nome de usuário**. Essa é a conta de AD DS usada pelo Azure AD Connect para executar a sincronização de diretório. Para que o Azure AD Connect execute o write-back de senha, a conta do AD DS deve ter permissão para Redefinir Senha.  
![Permissão Efetiva – Etapa 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
4. Faça logon no Controlador de Domínio local e inicie o aplicativo **Usuários e Computadores do Active Directory**.
5. Clique em **Exibir** e certifique-se de que a opção **Recursos Avançados** esteja habilitada.  
![Permissão Efetiva – Etapa 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
6. Procure a conta de Usuário do AD que você deseja verificar. Clique com o botão direito do mouse na conta e selecione **Propriedades**.  
![Permissão Efetiva – Etapa 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 
7. Na caixa de diálogo pop-up, vá até a guia **Segurança** e clique em **Avançado**.  
![Permissão Efetiva – Etapa 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
8. Na caixa de diálogo pop-up Configurações de Segurança Avançadas, vá até a guia **Acesso Efetivo**.
9. Clique em **Selecionar um usuário** e selecione a conta do AD DS usada pelo Azure AD Connect (consulte a etapa 3). Em seguida, clique em **Exibir acesso efetivo**.  
![Permissão Efetiva – Etapa 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
10. Role para baixo e procure **Redefinir senha**. Se a entrada estiver marcada, isso significa que a conta do AD DS tem permissão para redefinir a senha da conta de Usuário do AD selecionada.  
![Permissão Efetiva – Etapa 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóruns do Azure AD

Caso você tenha uma pergunta geral sobre o Azure AD e o autoatendimento de redefinição de senha, peça ajuda à comunidade nos [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem Engenheiros, Gerentes de Produto, MVPs e colegas Profissionais de TI.

## <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Caso não encontre a resposta para um problema, nossas equipes de suporte sempre estarão disponíveis para fornecer assistência adicional.

Para uma assistência adequada, solicitamos que você forneça o máximo de detalhes possíveis ao abrir um caso, incluindo:

* **Descrição geral do erro** – Qual é o erro? Qual foi o comportamento observado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possíveis.
* **Página** – Em qual página você estava quando observou o erro? Se possível, inclua a URL e uma captura de tela.
* **Código de suporte** – Qual foi o código de suporte gerado quando o usuário viu o erro?
    * Para encontrá-lo, reproduza o erro, clique no link Código de Suporte na parte inferior da tela e envie o GUID resultante ao engenheiro de suporte.
    ![Localizar o código de suporte na parte inferior da tela][Support Code]
    * Se você estiver em uma página sem um código de suporte na parte inferior, pressione F12 para o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário** – inclua a data e hora exatas **com o fuso horário** em que ocorreu o erro.
* **ID de Usuário** – Quem foi o usuário que viu o erro? (user@contoso.com)
    * Trata-se de um usuário federado?
    * Um usuário sincronizado com hash de senha?
    * Ou de um usuário somente de nuvem?
* **Licenciamento** – O usuário possuía uma licença do Azure AD Premium ou Azure AD Básico atribuída?
* **Log de eventos do aplicativo** – se você estiver usando o write-back de senha e o erro estiver na infraestrutura local, inclua uma cópia compactada do log de eventos do aplicativo por meio do servidor Azure AD Connect ao contatar o suporte.



[Service Restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reiniciar o serviço Azure AD Sync"
[Support Code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "O Código de Suporte está localizado no canto inferior direito da janela"

## <a name="next-steps"></a>Próximas etapas

Os links a seguir fornecem informações adicionais sobre a redefinição de senha usando o Azure AD

* [**Início Rápido**](active-directory-passwords-getting-started.md): comece agora mesmo a usar o gerenciamento de autoatendimento de senhas do Azure AD
* [**Licenciamento**](active-directory-passwords-licensing.md): configure o licenciamento do Azure AD
* [**Dados**](active-directory-passwords-data.md): entenda os dados que são necessários e como eles são usados para o gerenciamento de senhas
* [**Distribuição**](active-directory-passwords-best-practices.md): planeje e implante o SSPR para seus usuários usando as diretrizes descritas aqui
* [**Personalizar**](active-directory-passwords-customize.md): personalize a aparência da experiência do SSPR em sua empresa.
* [**Política** ](active-directory-passwords-policy.md) - Como entender e definir políticas de senha do Azure AD
* [**Write-back de senha** ](active-directory-passwords-writeback.md) - Como o write-back de senha opera com o seu diretório local
* [**Relatório** ](active-directory-passwords-reporting.md) - Descubra se, quando e onde os usuários estão acessando a funcionalidade da SSPR
* [**Detalhamento Técnico**](active-directory-passwords-how-it-works.md): veja os bastidores para entender como o recurso funciona
* [**Perguntas frequentes**](active-directory-passwords-faq.md): como? Por quê? O quê? Onde? Quem? Quando? – respostas para perguntas que você sempre quis fazer
