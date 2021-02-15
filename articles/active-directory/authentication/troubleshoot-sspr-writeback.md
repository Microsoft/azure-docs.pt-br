---
title: Solucionar problemas Azure Active Directory de write-back de autoatendimento de redefinição de senha
description: Saiba como solucionar problemas comuns e etapas de resolução para Write-back de redefinição de senha de autoatendimento no Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0620304de1866d24719b137836419502cd25bee9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682230"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Solucionar problemas de write-back de redefinição de senha Azure Active Directory de autoatendimento

Azure Active Directory (Azure AD) autoatendimento de redefinição de senha (SSPR) permite que os usuários redefinam suas senhas na nuvem. O write-back de senha é um recurso habilitado com [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de senha na nuvem possam efetuar write-back para um diretório local existente em tempo real.

Se você tiver problemas com o Write-back do SSPR, as seguintes etapas de solução de problemas e erros comuns podem ajudar. Se você não conseguir encontrar a resposta para o problema, [nossas equipes de suporte estarão sempre disponíveis](#contact-microsoft-support) para ajudá-lo.

## <a name="troubleshoot-connectivity"></a>Solucionar problemas de conectividade

Se você tiver problemas com o Write-back de senha para Azure AD Connect, examine as etapas a seguir que podem ajudar a resolver o problema. Para recuperar seu serviço, recomendamos que você siga estas etapas na ordem:

* [Verificar a conectividade de rede](#confirm-network-connectivity)
* [Reiniciar o serviço Sincronização do Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Desabilitar e reabilitar o recurso de write-back de senha](#disable-and-re-enable-the-password-writeback-feature)
* [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Solucionar problemas de write-back de senha](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Verificar a conectividade de rede

O ponto mais comum de falha é que o firewall ou as portas de proxy ou os tempos limite de ociosidade estão configurados incorretamente.

Para Azure AD Connect versão *1.1.443.0* e posterior, o acesso *https de saída* é necessário para os seguintes endereços:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

[Pontos de extremidade](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)do Azure gov:

* *\*. passwordreset.microsoftonline.us*
* *\*. servicebus.usgovcloudapi.net*

Se precisar de mais granularidade, consulte a [lista de intervalos de IP do datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Essa lista é atualizada todas as quartas-feiras e entra em vigor na próxima segunda-feira.

Para obter mais informações, consulte os [pré-requisitos de conectividade para Azure ad Connect](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o serviço Sincronização do Azure AD Connect

Para resolver problemas de conectividade ou outros problemas transitórios com o serviço, conclua as seguintes etapas para reiniciar o serviço de sincronização de Azure AD Connect:

1. Como administrador no servidor que executa o Azure AD Connect, selecione **Iniciar**.
1. Insira *services.msc* no campo de pesquisa e selecione **Enter**.
1. Procure a entrada *Microsoft Azure AD Sync*.
1. Clique com o botão direito do mouse na entrada do serviço, selecione **reiniciar** e aguarde a conclusão da operação.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="Reiniciar o serviço Azure AD Sync usando a GUI" border="false":::

Essas etapas restabelecem a conexão com o Azure AD e devem resolver seus problemas de conectividade.

Se a reinicialização do serviço de sincronização de Azure AD Connect não resolver o problema, tente desabilitar e reabilitar o recurso de write-back de senha na próxima seção.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desabilitar e reabilitar o recurso de write-back de senha

Para continuar a solucionar problemas, conclua as seguintes etapas para desabilitar e reabilitar o recurso de write-back de senha:

1. Como administrador no servidor que executa o Azure AD Connect, abra o **Assistente de configuração do Azure ad Connect**.
1. Em **Conectar-se ao Azure AD**, insira suas credenciais de administrador global do Azure AD.
1. Em **conectar a AD DS**, insira suas credenciais de administrador de Active Directory Domain Services local.
1. Em **Identificando seus usuários com exclusividade**, selecione o botão **Avançar**.
1. Em **Recursos opcionais**, desmarque a caixa de seleção **write-back de senha**.
1. Selecione **Avançar** nas páginas de diálogo restantes sem alterar nada até chegar à página **Pronto para configurar**.
1. Verifique se a **página pronto para configurar** mostra a opção de *write-back de senha* como *desabilitada*. Selecione o botão verde **Configurar** para confirmar suas alterações.
1. Em **Concluído**, desmarque a opção **Sincronizar agora** e selecione **Concluir** para fechar o assistente.
1. Reabra o **Assistente de configuração do Azure ad Connect**.
1. Repita as etapas 2-8, desta vez selecionando a opção *write-back de senha* na página **recursos opcionais** para reabilitar o serviço.

Essas etapas restabelecem a conexão com o Azure AD e devem resolver seus problemas de conectividade.

Se desabilitar e habilitar novamente o recurso de write-back de senha não resolver o problema, reinstale Azure AD Connect na próxima seção.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente do Azure AD Connect

A reinstalação de Azure AD Connect pode resolver problemas de configuração e conectividade entre o Azure AD e o ambiente de Active Directory Domain Services local. É recomendável que você execute essa etapa somente depois de tentar as etapas anteriores para verificar e solucionar problemas de conectividade.

> [!WARNING]
> Se você tiver personalizado as regras de sincronização prontas para uso, *faça backup deles antes de prosseguir com a atualização e reimplante-os manualmente depois de concluir.*

1. Baixe a última versão do Azure AD Connect no [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Como você já instalou o Azure AD Connect, execute uma atualização in-loco para atualizar sua instalação do Azure AD Connect para a versão mais recente.

    Execute o pacote baixado e siga as instruções na tela para atualizar Azure AD Connect.

Essas etapas devem restabelecer sua conexão com o Azure AD e resolver seus problemas de conectividade.

Se a instalação da versão mais recente do Azure AD Connect Server não resolver o problema, tente desabilitar e reabilitar o Write-back de senha como uma etapa final depois de instalar a versão mais recente.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Verifique se Azure AD Connect tem as permissões necessárias

Azure AD Connect requer AD DS permissão de **redefinição de senha** para executar o Write-back de senha. Para verificar se Azure AD Connect tem a permissão necessária para uma determinada conta de usuário local AD DS, use o recurso de **permissão efetiva do Windows** :

1. Faça o logon no servidor do Azure AD Connect e inicie o **Synchronization Service Manager** selecionando **Iniciar** > **Serviço de Sincronização**.
1. Na guia **Conectores**, selecione o conector do **Active Directory Domain Services** local e selecione **Propriedades**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Synchronization Service Manager mostrando como editar propriedades" border="false":::
  
1. Na janela pop-up, selecione **Conectar-se à Floresta do Active Directory** e anote a propriedade **User name**. Essa propriedade é a conta de AD DS usada pelo Azure AD Connect para executar a sincronização de diretório.

    Para que o Azure AD Connect execute o write-back de senha, a conta do AD DS deve ter permissão para redefinir senha. Você verifica as permissões nessa conta de usuário nas etapas a seguir.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Localizar a conta de usuário do Active Directory do serviço de sincronização" border="false":::
  
1. Entre em um controlador de domínio local e inicie o aplicativo **Usuários e Computadores do Active Directory**.
1. Selecione **Exibir** e verifique se a opção **Recursos Avançados** está habilitada.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Computadores e usuários do Active Directory mostram recursos avançados" border="false":::
  
1. Procure a conta de usuário AD DS que você deseja verificar. Clique com o botão direito do mouse na conta e selecione **Propriedades**.  
1. Na janela pop-up, vá para a guia **Segurança** e selecione **Avançado**.  
1. Na janela **Configurações de segurança Avançadas para o Administrador**, vá para a guia **Acesso Efetivo**.
1. Escolha **selecionar um usuário**, selecione a conta de AD DS usada pelo Azure ad Connect e, em seguida, selecione **exibir acesso efetivo**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Guia Acesso Efetivo mostrando a conta de sincronização" border="false":::
  
1. Role para baixo e procure **Redefinir senha**. Se a entrada tem uma marca de seleção, a conta do AD DS tem permissão para redefinir a senha da conta de usuário do Active Directory selecionada.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Verificar se a conta de sincronização tem a permissão Redefinir senha" border="false":::

## <a name="common-password-writeback-errors"></a>Erros comuns de write-back de senha

Os seguintes problemas mais específicos podem ocorrer com o Write-back de senha. Se você tiver um desses erros, examine a solução proposta e verifique se o Write-back de senha funciona corretamente.

| Erro | Solução |
| --- | --- |
| O serviço de redefinição de senha não é iniciado localmente. O erro 6800 aparece no log de eventos do aplicativo do Azure AD Connect Machine. <br> <br> Após a integração, os usuários federados, com autenticação de passagem ou sincronizados com hash de senha não conseguem redefinir suas senhas. | Quando o write-back de senha é habilitado, o mecanismo de sincronização chama a biblioteca de write-back para realizar a configuração (integração) comunicando-se com o serviço de integração em nuvem. Os erros encontrados durante a integração ou ao iniciar o ponto de extremidade do WCF (Windows Communication Foundation) para o write-back de senha resultam em erros no log de eventos e no computador do Azure AD Connect. <br> <br> Durante a reinicialização do serviço ADSync (Sincronização do Azure AD), se o write-back tiver sido configurado, o ponto de extremidade do WCF será inicializado. Mas, se a inicialização do ponto de extremidade falhar, registraremos o evento 6800 e permitiremos que o serviço de sincronização seja inicializado. A presença desse evento significa que o ponto de extremidade de write-back de senha não foi iniciado. Os detalhes do log de eventos desse evento (6800) juntamente com as entradas do log de eventos geradas pelo componente PasswordResetService indicam por que não é possível iniciar o ponto de extremidade. Examine esses erros de log de eventos e tente reiniciar o Azure AD Connect se o Write-back de senha ainda não estiver funcionando. Se o problema persistir, tente desabilitar e reabilitar o write-back de senha.
| Quando um usuário tenta redefinir uma senha ou desbloquear uma conta com write-back de senha habilitada, a operação falha. <br> <br> Além disso, você verá um evento no log de eventos Azure AD Connect que contém: "o mecanismo de sincronização retornou um erro HR = 800700CE, Message = o nome do arquivo ou a extensão é muito longa" após a operação de desbloqueio ocorrer. | Encontre a conta do Active Directory no Azure AD Connect e redefina a senha para que contenha no máximo 256 caracteres. Em seguida, abra o **serviço de sincronização** no menu **Iniciar** . Navegue para **Conectores** e localize o **Active Directory Connector**. Selecione-o e, em seguida, selecione **Propriedades**. Navegue até a página **Credenciais** e digite a nova senha. Selecione **OK** para fechar a página. |
| Na última etapa do processo de instalação do Azure AD Connect, você verá um erro que indica que não é possível configurar o write-back de senha. <br> <br> O log de eventos do aplicativo Azure AD Connect contém o erro 32009 com o texto "erro ao obter o token de autenticação". | Esse erro ocorre nos seguintes casos: <br><ul><li>Você especificou uma senha incorreta para a conta de administrador global fornecida no início do processo de instalação do Azure AD Connect.</li><li>Você tentou usar um usuário federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li></ul> Para corrigir esse problema, certifique-se de que você não está usando uma conta federada para o administrador global que você especificou no início do processo de instalação e que a senha especificada está correta. |
| O log de eventos do computador do Azure AD Connect contém o erro 32002, lançado pela execução de PasswordResetService. <br> <br> O erro diz: "erro ao conectar-se ao ServiceBus. O provedor de token não pôde fornecer um token de segurança. " | O ambiente local não pode se conectar ao ponto de extremidade do Barramento de Serviço do Azure na nuvem. Esse erro normalmente é causado por uma regra de firewall que bloqueia uma conexão de saída com uma porta ou um endereço web específico. Consulte [Pré-requisitos de conectividade](../hybrid/how-to-connect-install-prerequisites.md) para saber mais. Depois de atualizar essas regras, reinicie o servidor de Azure AD Connect e o Write-back de senha deve começar a funcionar novamente. |
| Após trabalhar um pouco, os usuários federados, com autenticação de passagem ou sincronizados com hash de senha não conseguem redefinir suas senhas. | Em alguns casos raros, o serviço write-back de senha poderá falhar ao reiniciar quando o Azure AD Connect for reiniciado. Nesses casos, primeiro verifique se o Write-back de senha está habilitado localmente. Você pode verificar usando o assistente de Azure AD Connect ou o PowerShell. Se o recurso parecer estar habilitado, tente habilitar ou desabilitar o recurso novamente. Se essa etapa de solução de problemas não funcionar, tente uma desinstalação completa e reinstale o Azure AD Connect. |
| Os usuários federados, com autenticação de passagem ou sincronizados por hash de senha que tentam redefinir suas senhas veem um erro quando tentam enviar a senha. O erro indica que houve um problema de serviço. <br ><br> Além disso, durante as operações de redefinição de senha, você pode ver um erro sobre o agente de gerenciamento ter acesso negado em seus logs de eventos no local. | Se você vir esses erros no log de eventos, confirme se a conta do ADMA (Agente de Gerenciamento do Active Directory) que foi especificada no assistente no momento da configuração tem as permissões necessárias para o write-back de senha. <br> <br> Após a permissão ser concedida, poderá levar até uma hora para que as permissões sejam repassadas pela tarefa em segundo plano `sdprop` no DC (controlador de domínio). <br> <br> Para que a redefinição de senha funcione, a permissão deve ser marcada no descritor de segurança do objeto do usuário cuja senha esteja sendo redefinida. Até que essa permissão seja exibida no objeto do usuário, a redefinição de senha continuará falhando com uma mensagem de acesso negado. |
| Os usuários federados, com autenticação de passagem ou sincronizados por hash de senha que tentam redefinir suas senhas veem um erro quando enviam a senha. O erro indica que houve um problema de serviço. <br> <br> Além desse problema, durante as operações de redefinição de senha, você poderá ver um erro nos logs de eventos do serviço de Azure AD Connect indicando um erro "não foi possível encontrar o objeto". | Esse erro geralmente indica que o mecanismo de sincronização não consegue encontrar o objeto de usuário no espaço do conector do Azure AD ou o objeto de espaço do conector do Azure AD ou MV (metaverso) vinculado. <br> <br> Para resolver esse problema, verifique se o usuário está realmente sincronizado do local para o Azure AD pela instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços conectores e no MV. Confirme se o objeto de serviços de certificados Active Directory (AD CS) está conectado ao objeto MV por meio da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Os usuários federados, com autenticação de passagem ou sincronizados por hash de senha que tentam redefinir suas senhas veem um erro quando enviam a senha. O erro indica que houve um problema de serviço. <br> <br> Além desse problema, durante as operações de redefinição de senha, você pode ver um erro nos logs de eventos do serviço Azure AD Connect que indica que há um erro "várias correspondências encontradas". | Isso indica que o mecanismo de sincronização detectou que o objeto MV está conectado a mais de um objeto AD CS por meio de "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o usuário tem uma conta habilitada em mais de uma floresta. Não há suporte para write-back de senha nesse cenário. |
| Falha nas operações de senha com um erro de configuração. O log de eventos do aplicativo contém o erro Azure AD Connect 6329 com o texto “0x8023061f (a operação falhou porque a sincronização de senha não está habilitada nesse agente de gerenciamento)”. | Esse erro ocorre quando a configuração do Azure AD Connect é alterada para adicionar uma nova floresta do Active Directory (ou para remover e adicionar novamente uma floresta existente) após o recurso de write-back de senha já ter sido habilitado. As operações de senha para usuários em florestas recém-adicionadas como essas falharão. Para corrigir o problema, desabilite e reabilite o recurso de write-back de senha após a conclusão das alterações de configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro do log de eventos de write-back de senha

Uma melhor prática para solucionar problemas com write-back de senha é inspecionar o log de eventos do aplicativo no computador do Azure AD Connect. Esse log de eventos contém eventos de duas fontes para Write-back de senha. A fonte *PasswordResetService* descreve operações e problemas relacionados à operação de write-back de senha. A fonte *AdSync* descreve operações e problemas relacionados à definição de senhas em seu ambiente de Active Directory Domain Services.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a origem do evento é ADSync

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 6329 | ALÇA: MMS (4924) 0x80230619: "uma restrição impede que a senha seja alterada para a atual especificada." | Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha em seu diretório local que não atende à duração da senha, ao histórico, à complexidade ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado a senha recentemente nessa janela de tempo, não poderá alterar a senha novamente até que ela atinja a duração especificada no domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deverá selecionar uma senha que não tenha sido usada nas últimas *N* vezes, em que *n* é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas *X* vezes, verá uma falha. Para fins de teste, o histórico de senha deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atenda aos critérios de filtragem, a operação de redefinição ou alteração falhará. |
| 6329 | MMS (3040): admaexport. cpp (2837): o servidor não contém o controle de política de senha LDAP. | Esse problema ocorre se o controle LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) não está habilitado nos controladores de domínio. Para usar o recurso de write-back de senha, é necessário habilitar o controle. Para fazer isso, os DCs devem estar no Windows Server 2008R2 ou posterior. |
| HR 8023042 | O Mecanismo de Sincronização retornou um erro hr = 80230402, mensagem= Houve uma falha na tentativa de obter um objeto, porque existem entradas duplicadas com a mesma âncora. | Esse erro ocorre quando a mesma ID de usuário está habilitada em vários domínios. Por exemplo, se você estiver sincronizando florestas de contas e recursos e tiver a mesma ID de usuário presente e habilitada em cada uma delas, esse erro poderá ocorrer. <br> <br> Esse erro também pode ocorrer se você usa um atributo de âncora não exclusivo (como alias ou UPN) e dois usuários compartilham o mesmo atributo de âncora. <br> <br> Para resolver esse problema, verifique se você não tem nenhum usuário duplicado em seus domínios e se está usando um atributo de âncora exclusivo para cada usuário. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento é PasswordResetService

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 31001 | PasswordResetStart | Esse evento indica que o serviço local detectou uma solicitação de redefinição de senha de um usuário federado, com autenticação de passagem ou sincronizado com hash de senha proveniente da nuvem. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha. |
| 31002 | PasswordResetSuccess | Esse evento indica que o usuário selecionou uma nova senha durante uma operação de redefinição de senha. Determinamos que essa senha atende aos requisitos de senha corporativa. A senha foi gravada com êxito novamente no ambiente do Active Directory local. |
| 31003 | PasswordResetFail | Esse evento indica que o usuário selecionou uma senha e a senha chegou com sucesso ao ambiente local. Mas quando tentamos definir a senha no ambiente do Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por diversos motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver esse problema, crie uma nova senha.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como domínio ou grupo de administradores corporativos, que não permite operações de definição de senha.</li></ul>|
| 31004 | OnboardingEventStart | Esse evento ocorrerá se você habilitar o Write-back de senha com Azure AD Connect e iniciarmos a integração de sua organização ao serviço Web de write-back de senha. |
| 31005 | OnboardingEventSuccess | Esse evento indica que o processo de integração foi bem-sucedido e que a funcionalidade de write-back de senha está pronta para uso. |
| 31006 | ChangePasswordStart | Esse evento indica que o serviço local detectou uma solicitação de alteração de senha de um usuário federado, com autenticação de passagem ou sincronizado com hash de senha proveniente da nuvem. Esse evento é o primeiro evento em cada operação de write-back de alteração de senha. |
| 31007 | ChangePasswordSuccess | Esse evento indica que o usuário selecionou uma nova senha durante uma operação de alteração de senha; determinamos que a senha atende aos requisitos de senha corporativa e essa senha foi gravada com êxito no ambiente Active Directory local. |
| 31008 | ChangePasswordFail | Esse evento indica que um usuário selecionou uma senha e que a senha chegou com sucesso ao ambiente local, mas, quando tentamos definir a senha no ambiente do Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por diversos motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver esse problema, crie uma nova senha.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permitem operações de definição de senha.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço local detectou uma solicitação de redefinição de senha de um usuário federado, com autenticação de passagem ou sincronizado com hash de senha proveniente do administrador em nome de um usuário. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha iniciada por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova senha durante uma operação de redefinição de senha iniciada pelo administrador. Determinamos que essa senha atende aos requisitos de senha corporativa. A senha foi gravada com êxito novamente no ambiente do Active Directory local. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma senha em nome do usuário. A senha chegou com sucesso ao ambiente local. Mas quando tentamos definir a senha no ambiente do Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por diversos motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Experimente uma nova senha para resolver esse problema.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permitem operações de definição de senha.</li></ul>  |
| 31012 | OffboardingEventStart | Esse evento ocorrerá se você desabilitar o write-back de senha com o Azure AD Connect e indicará que começamos a remoção de sua organização do serviço Web de write-back de senha. |
| 31013| OffboardingEventSuccess| Esse evento indica que o processo de integração foi bem-sucedido e que a funcionalidade de write-back de senha foi desabilitada com êxito. |
| 31014| OffboardingEventFail| Esse evento indica que o processo de remoção não foi bem-sucedido. Isso pode ser devido a um erro de permissões na conta de administrador de nuvem ou local especificada durante a configuração. O erro também pode ocorrer se você está tentando usar um administrador global da nuvem federada ao desabilitar o write-back de senha. Para corrigir esse problema, verifique suas permissões administrativas e se não está usando uma conta federada ao configurar a funcionalidade de write-back de senha.|
| 31015| WriteBackServiceStarted| Esse evento indica que o serviço de write-back de senha foi iniciado com êxito. Ele está pronto para aceitar solicitações de gerenciamento de senha da nuvem.|
| 31016| WriteBackServiceStopped| Esse evento indica que o serviço de write-back de senha parou. As solicitações de gerenciamento de senha da nuvem não serão bem-sucedidas.|
| 31017| AuthTokenSuccess| Esse evento indica que recuperamos com êxito um token de autorização para o administrador global especificado durante a instalação do Azure AD Connect para iniciar o processo de remoção ou integração.|
| 31018| KeyPairCreationSuccess| Esse evento indica que criamos a chave de criptografia de senha com êxito. Essa chave é usada para criptografar senhas da nuvem para envio ao seu ambiente local.|
| 31034| ServiceBusListenerError| Esse evento indica que houve um erro ao conectar-se ao ouvinte do barramento de serviço do locatário. Se a mensagem de erro incluir "o certificado remoto é inválido", verifique se o servidor de Azure AD Connect tem todas as CAs raiz necessárias, conforme descrito em [alterações no certificado TLS do Azure](../../security/fundamentals/tls-certificate-changes.md). |
| 32000| UnknownError| Esse evento indica a ocorrência de um erro desconhecido durante uma operação de gerenciamento de senha. Examine o texto da exceção no evento para obter mais detalhes. Se você está tendo problemas, tente desabilitar e reabilitar o write-back de senha. Se isso não ajudar, inclua uma cópia do seu log de eventos junto com a ID de rastreamento especificada quando você abrir uma solicitação de suporte.|
| 32001| ServiceError| Esse evento indica que houve um erro de conexão com o serviço de nuvem de redefinição de senha. Esse erro geralmente ocorre quando o serviço local não pôde se conectar ao serviço Web de redefinição de senha.|
| 32002| ServiceBusError| Esse evento indica que houve um erro ao conectar-se à instância do barramento de serviço do locatário. Isso pode acontecer porque você está bloqueando as conexões de saída no seu ambiente local. Verifique se o firewall permite conexões via TCP 443 e para https://ssprdedicatedsbprodncu.servicebus.windows.net e tente novamente. Se você ainda está com problemas, tente desabilitar e reabilitar o write-back de senha.|
| 32003| InPutValidationError| Esse evento indica que a entrada passada para a API do nosso serviço Web era inválida. Tente a operação novamente.|
| 32004| DecryptionError| Esse evento indica que houve um erro ao descriptografar a senha que chegou da nuvem. Isso pode ser devido a uma falta de correspondência de chave de descriptografia entre o serviço de nuvem e o ambiente local. Para resolver o problema, desabilite e reabilite o write-back de senha no seu ambiente local.|
| 32005| ConfigurationError| Durante a migração, nós salvamos informações específicas de locatário em um arquivo de configuração no seu ambiente local. Esse evento indica que houve um erro ao salvar esse arquivo ou que, quando o serviço foi iniciado, havia erro na leitura do arquivo. Para corrigir o problema, tente desabilitar e reabilitar o write-back de senha para forçar uma regeneração do arquivo de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante a migração, podemos enviar dados da nuvem para o serviço de redefinição de senha local. Esses dados são gravados em um arquivo na memória antes de serem enviados ao serviço de sincronização para armazenamento seguro no disco. Esse evento indica que há um problema com a gravação ou atualização desses dados na memória. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha para forçar uma regeneração desse arquivo de configuração.|
| 32008| ValidationError| Esse evento indica que recebemos uma resposta inválida do serviço Web de redefinição de senha. Para corrigir esse problema, tente desabilitar e reabilitar o write-back de senha.|
| 32009| AuthTokenError| Esse evento indica que não conseguimos obter um token de autorização para a conta de administrador global especificada durante a instalação do Azure AD Connect. Esse erro pode ser causado por um nome de usuário ou senha especificado para a conta de administrador global. Esse erro também pode ocorrer se a conta de administrador global especificada é federada. Para corrigir esse problema, execute a configuração novamente com o nome de usuário e senha corretos e verifique se o administrador é uma conta gerenciada (somente em nuvem ou sincronizada por senha).|
| 32010| CryptoError| Esse evento indica que houve um erro ao gerar a chave de criptografia de senha ou ao descriptografar uma senha que chegou do serviço de nuvem. Esse erro provavelmente indica um problema com o seu ambiente. Examine os detalhes de seu log de eventos para saber mais sobre como resolver esse problema. Você também pode tentar desabilitar e reabilitar o serviço de write-back de senha.|
| 32011| OnBoardingServiceError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode acontecer como resultado de uma regra de firewall ou se houver um problema ao obter um token de autenticação para seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída via TCP 443 e TCP 9350 a 9354 ou para https://ssprdedicatedsbprodncu.servicebus.windows.net. Também verifique se a conta de administrador do Azure AD que você está usando na integração não é federada.|
| 32013| OffBoardingError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode acontecer como resultado de uma regra de firewall ou se houver um problema ao obter um token de autorização para seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída via 443 ou para https://ssprdedicatedsbprodncu.servicebus.windows.net e se a conta do administrador do Azure Active Directory que você está usando para a remoção não é federada.|
| 32014| ServiceBusWarning| Esse evento indica que tivemos que tentar novamente se conectar à instância do barramento de serviço de seu locatário. Em condições normais, isso não deve ser uma preocupação, mas se você vir esse evento muitas vezes, considere verificar sua conexão de rede para o barramento de serviço, especialmente se for uma conexão de alta latência ou baixa largura de banda.|
| 32015| ReportServiceHealthError| Para monitorar a integridade do seu serviço de write-back de senha, enviamos dados de pulsação para o nosso serviço Web de redefinição de senha a cada cinco minutos. Esse evento indica que houve um erro ao enviar essas informações de integridade para o serviço Web em nuvem. Essas informações de integridade não incluem dados pessoais e são puramente uma pulsação e estatísticas básicas de serviço para que possamos fornecer informações de status de serviço na nuvem.|
| 33001| ADUnKnownError| Esse evento indica que houve um erro desconhecido retornado pelo Active Directory. Verifique o log de eventos do servidor do Azure AD Connect quanto a eventos provenientes do ADSync para saber mais.|
| 33002| ADUserNotFoundError| Esse evento indica que o usuário que está tentando redefinir ou alterar uma senha não foi encontrado no diretório local. Esse erro pode ocorrer quando o usuário foi excluído no local, mas não na nuvem. Esse erro também pode ocorrer se houver um problema com a sincronização. Verifique os logs de sincronização e os últimos detalhes da execução de sincronização para obter mais informações.|
| 33003| ADMutliMatchError| Quando uma solicitação de redefinição ou de alteração de senha são provenientes da nuvem, usamos a âncora de nuvem especificada durante a instalação do Azure AD Connect para determinar como vincular essa solicitação a um usuário no seu ambiente local. Esse evento indica que encontramos dois usuários em seu diretório local com o mesmo atributo de âncora de nuvem. Verifique os logs de sincronização e os detalhes das últimas execuções de sincronizações para saber mais.|
| 33004| ADPermissionsError| Esse evento indica que a conta de serviço ADMA (agente de gerenciamento de Active Directory) não tem as permissões apropriadas na conta em questão para definir uma nova senha. Verifique se a conta ADMA na floresta do usuário tem permissões de redefinição de senha em todos os objetos na floresta. Para obter mais informações sobre como definir as permissões, confira a Etapa 4: Configurar as permissões apropriadas do Active Directory. Esse erro também pode ocorrer quando o atributo AdminCount do usuário é definido como 1.|
| 33005| ADUserAccountDisabled| Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi desabilitada no local. Habilite a conta e tente a operação novamente.|
| 33006| ADUserAccountLockedOut| Esse evento indica que tentamos redefinir ou alterar uma senha de uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um usuário tiver tentado alterar ou redefinir a senha muitas vezes em um curto período de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| ADUserIncorrectPassword| Esse evento indica que o usuário especificou uma senha atual incorreta durante a operação de alteração de senha. Especifique a senha atual correta e tente novamente.|
| 33008| ADPasswordPolicyError| Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha em seu diretório local que não atende à duração da senha, ao histórico, à complexidade ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado a senha recentemente nessa janela de tempo, não poderá alterar a senha novamente até que ela atinja a duração especificada no domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deve selecionar uma senha que não foi usada nas últimas *X* vezes, em que *X* é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas *X* vezes, verá uma falha. Para fins de teste, o histórico de senha deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atenda aos critérios de filtragem, a operação de redefinição ou alteração falhará.|
| 33009| ADConfigurationError| Esse evento indica que houve um problema na gravação de senha no seu diretório local devido a um problema de configuração com o Active Directory. Verifique o log de eventos do aplicativo da máquina Azure AD Connect em busca de mensagens do serviço ADSync para obter mais informações sobre qual erro ocorreu.|

## <a name="azure-ad-forums"></a>Fóruns do Azure AD

Se você tiver perguntas gerais sobre o Azure AD e a redefinição de senha de autoatendimento, você poderá pedir à comunidade ajuda na [página do Microsoft Q&uma pergunta para Azure Active Directory](/answers/topics/azure-active-directory.html). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de TI.

## <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Caso não encontre a resposta para um problema, nossas equipes de suporte sempre estarão disponíveis para fornecer assistência adicional.

Para uma assistência adequada, solicitamos que você forneça o máximo de detalhes possíveis ao abrir um caso. Esses detalhes incluem o seguinte:

* **Descrição geral do erro**: Qual é o erro? Qual foi o comportamento observado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em qual página você estava quando observou o erro? Se possível, inclua a URL e uma captura de tela da página.
* **Código de suporte**: Qual foi o código de suporte gerado quando o usuário viu o erro?
   * Para encontrar o código, reproduza o erro, selecione o link **Código de Suporte** na parte inferior da tela e envie o GUID resultante ao engenheiro de suporte.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="O código de suporte está localizado na parte inferior direita da janela do navegador da Web.":::

  * Se você estiver em uma página sem um código de suporte na parte inferior, selecione F12 para o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora exatas *com o fuso horário* em que ocorreu o erro.
* **ID de Usuário**: Qual usuário viu o erro? Um exemplo é *user\@contoso.com*.
   * Trata-se de um usuário federado?
   * Trata-se de um usuário de autenticação de passagem?
   * Um usuário sincronizado com hash de senha?
   * Ou de um usuário somente de nuvem?
* **Licenciamento**: O usuário tem uma licença do Azure AD atribuída?
* **Log de eventos do aplicativo**: Se estiver usando o write-back de senha e o erro estiver na infraestrutura local, inclua uma cópia compactada do log de eventos do aplicativo por meio do servidor do Azure AD Connect.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o SSPR, veja [como ele funciona: redefinição de senha de autoatendimento do Azure ad](concept-sspr-howitworks.md) ou [como o Write-back de redefinição de senha de autoatendimento funciona no Azure ad?](concept-sspr-writeback.md).