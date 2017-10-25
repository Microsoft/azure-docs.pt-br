---
title: Solucionar problemas de Proxy de Aplicativo | Microsoft Docs
description: Aborda como solucionar erros no Proxy de Aplicativo do Azure do AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 970caafb-40b8-483c-bb46-c8b032a4fb74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 3ef74c8d5d5b75eb2bca4e6c994f4718b5226db8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Solucionar problemas e mensagens de erro do Proxy do Aplicativo
Se ocorrerem erros ao acessar um aplicativo publicado ou em aplicativos de publicação, verifique as seguintes opções para ver se o Proxy de Aplicativo do AD do Microsoft Azure está funcionando corretamente:

* Abra o console de serviços do Windows e verifique se o serviço de **Conector de Proxy de Aplicativo do Microsoft AAD** está habilitado e em execução. Também convém examinar a página de propriedades do serviço de Proxy de Aplicativo, conforme mostrado na imagem a seguir:   
  ![Captura de tela da janela Propriedades do Conector de Proxy de Aplicativo do Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Abra o Visualizador de Eventos e procure eventos de conector do Proxy de Aplicativo em **Logs de Aplicativos e Serviços**  > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* Se necessário, logs mais detalhados estão disponíveis via [ativação dos logs de sessão do conector do Proxy de Aplicativo](application-proxy-understand-connectors.md#under-the-hood).

Para obter mais informações sobre a ferramenta de Solução de problemas do Azure AD, consulte [Ferramenta da solução de problemas para validar os pré-requisitos de rede do conector](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>A página não é renderizada corretamente
Você pode ter problemas com a renderização do aplicativo ou de funcionamento incorreto sem receber mensagens específicas de erro. Isso pode ocorrer se você publicou o caminho do artigo, mas o aplicativo requer o conteúdo que existe fora desse caminho.

Por exemplo, se você publicar o caminho https://yourapp/app, mas o aplicativo chamar imagens em https://yourapp/media, elas não serão renderizadas. Publique o aplicativo usando o caminho de nível mais alto de que você precisa para incluir todo o conteúdo relevante. Neste exemplo, seria http://yourapp/.

Se você alterar o caminho para incluir o conteúdo referenciado, mas ainda precisar que os usuários cheguem a um link mais profundo, confira a postagem de blog [Setting the right link for Application Proxy applications in the Azure AD access panel and Office 365 app launcher (Definindo o link certo para aplicativos do Proxy de Aplicativo no painel de acesso do Azure AD e no inicializador de aplicativos do Office 365)](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Erros de conector

Use a [Ferramenta de Teste de Portas do Conector de Proxy de Aplicativo do Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar se o conector pode alcançar o serviço Proxy de Aplicativo. No mínimo, verifique se a região EUA Central e a região mais próxima de você tem todas as marcas de seleção verdes. Além disso, um número maior de marcas de seleção verdes significa maior resiliência. 

Se o registro falhar durante a instalação do assistente do Conector, há duas maneiras de exibir o motivo da falha. Examine o log de eventos em **Logs de Aplicativos e Serviços\Microsoft\AadApplicationProxy\Connector\Admin** ou execute o seguinte comando do Windows PowerShell:

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

Depois de encontrar o erro do Conector no log de eventos, use esta tabela de erros comuns para resolver o problema:

| Erro | Etapas recomendadas |
| ----- | ----------------- |
| Falha no registro de conector: verifique se você habilitou o Proxy de Aplicativo no Portal de Gerenciamento do Azure e se inseriu o nome de usuário e a senha do Active Directory corretamente. Erro: "ocorreram um ou mais erros”. | Se você fechou a janela de registro sem entrar no Azure AD, execute o assistente do Conector novamente e registrar o Conector. <br><br> Se a janela de registro abre e fecha imediatamente sem permitir que você faça logon, você provavelmente obterá este erro. Esse erro ocorre quando há algum erro de rede em seu sistema. Certifique-se de que é possível conectar-se de um navegador a um site público e que as portas estejam abertas como especificado nos [pré-requisitos do Proxy de Aplicativo](active-directory-application-proxy-enable.md). |
| Apagar erro é apresentado na janela de registro. Não é possível continuar | Caso esse erro seja exibido e a janela fechar, você inseriu o nome de usuário e a senha incorretos. Tente novamente. |
| Falha no registro de conector: verifique se você habilitou o Proxy de Aplicativo no Portal de Gerenciamento do Azure e se inseriu o nome de usuário e a senha do Active Directory corretamente. Erro: ‘AADSTS50059: nenhuma informação de identificação de organização foi encontrada na solicitação nem está implícita em quaisquer credenciais fornecidas; a pesquisa por URI de entidade de serviço falhou. | Você está tentando entrar usando uma Conta da Microsoft e não de um domínio que faz parte da ID da organização do diretório que você está tentando acessar. Certifique-se de que o administrador faça parte do mesmo nome de domínio que o domínio do locatário; por exemplo, se o domínio do AD do Azure for contoso.com, o do administrador deverá ser admin@contoso.com. |
| Falha ao recuperar a política de execução atual para executar scripts do PowerShell. | Se a instalação do Conector falhar, verifique se a política de execução do PowerShell não está desabilitada. <br><br>1. Abra o Editor de Política de Grupo.<br>2. Vá para **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows** > **Windows PowerShell** e clique duas vezes em **Ativar Execução de Scripts**.<br>3. A política de execução pode ser definida como **Não Configurada** ou **Habilitada**. Se estiver definido como **Habilitado**, verifique se a Política de Execução em Opções está definida como **Permitir scripts locais e scripts remotos assinados** ou como **Permitir todos os scripts**. |
| Falha ao baixar a configuração do Conector. | O certificado de cliente do Conector, que é usado para autenticação, expirou. Isso também pode ocorrer se você tiver o Conector instalado atrás de um proxy. Nesse caso, o Conector não poderá acessar a Internet e não será capaz de fornecer aplicativos a usuários remotos. Renove a confiança manualmente usando o cmdlet `Register-AppProxyConnector` do Windows PowerShell. Se seu Conector estiver atrás de um proxy, será necessário conceder acesso à Internet para as contas "serviços de rede" e "sistema local" do Conector. Isso pode ser feito concedendo acesso ao Proxy ou configurando-os para ignorar o proxy. |
| Falha no registro do conector: verifique se você é um Administrador Global do seu Active Directory para registrar o conector. Erro: “a solicitação de registro foi negada”. | O alias com o qual você está tentando fazer logon não é um administrador neste domínio. O Conector sempre é instalado para o diretório que possui o domínio do usuário. Certifique-se de que a conta do administrador com a qual você está tentando se conectar tenha as permissões globais para o locatário do Azure AD. |

## <a name="kerberos-errors"></a>Erros de Kerberos

Esta tabela cobre os erros mais comuns resultantes da instalação e configuração do Kerberos e inclui sugestões para resolução.

| Erro | Etapas recomendadas |
| ----- | ----------------- |
| Falha ao recuperar a política de execução atual para executar scripts do PowerShell. | Se a instalação do Conector falhar, verifique se a política de execução do PowerShell não está desabilitada.<br><br>1. Abra o Editor de Política de Grupo.<br>2. Vá para **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows** > **Windows PowerShell** e clique duas vezes em **Ativar Execução de Scripts**.<br>3. A política de execução pode ser definida como **Não Configurada** ou **Habilitada**. Se estiver definido como **Habilitado**, verifique se a Política de Execução em Opções está definida como **Permitir scripts locais e scripts remotos assinados** ou como **Permitir todos os scripts**. |
| 12008 - O Azure AD excedeu o número máximo de tentativas de autenticação Kerberos permitidas para o servidor back-end. | Esse erro pode indicar uma configuração incorreta entre o Azure AD e o servidor de aplicativos back-end ou um problema na configuração de data e hora nos dois computadores. O servidor back-end recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se o Azure AD e o servidor de aplicativos de back-end estão configurados corretamente. Verifique se a configuração de data e hora no AD do Azure e no servidor de aplicativos back-end estão sincronizadas. |
| 13016 - O AD do Azure não pode recuperar um tíquete Kerberos em nome do usuário porque não há nenhum UPN no token de borda ou no cookie de acesso. | Há um problema com a configuração de STS. Corrija a configuração de declaração UPN no STS. |
| 13019 - O AD do Azure não pode recuperar um tíquete Kerberos em nome do usuário devido ao seguinte erro geral da API. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de controlador de domínio ou um problema na configuração de data e hora nos dois computadores. O controlador de domínio recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se o Azure AD e do servidor de aplicativos de back-end estão configurados corretamente, especialmente a configuração de SPN. Verifique se que o AD do Azure é o domínio ingressado no mesmo domínio que o controlador de domínio para garantir que este estabeleça a relação de confiança com o AD do Azure. Verifique se a configuração de data e hora no AD do Azure e no controlador de domínio estão sincronizadas. |
| 13020 - O AD do Azure não pode recuperar um tíquete Kerberos em nome do usuário porque o SPN do servidor de back-end não está definido. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de controlador de domínio ou um problema na configuração de data e hora nos dois computadores. O controlador de domínio recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se o Azure AD e do servidor de aplicativos de back-end estão configurados corretamente, especialmente a configuração de SPN. Verifique se que o AD do Azure é o domínio ingressado no mesmo domínio que o controlador de domínio para garantir que este estabeleça a relação de confiança com o AD do Azure. Verifique se a configuração de data e hora no AD do Azure e no controlador de domínio estão sincronizadas. |
| 13022 - O AD do Azure não pode autenticar o usuário porque o servidor de back-end responde às tentativas de autenticação Kerberos com um erro HTTP 401. | Esse evento pode indicar uma configuração incorreta entre o AD do Azure e o servidor de aplicativos de back-end ou um problema na configuração de data e hora nos dois computadores. O servidor back-end recusou o tíquete Kerberos criado pelo AD do Azure. Verifique se o Azure AD e o servidor de aplicativos de back-end estão configurados corretamente. Verifique se a configuração de data e hora no AD do Azure e no servidor de aplicativos back-end estão sincronizadas. |

## <a name="end-user-errors"></a>Erros de usuário final

Esta lista cobre os erros que os usuários finais podem encontrar quando tentam acessar o aplicativo e falham. 

| Erro | Etapas recomendadas |
| ----- | ----------------- |
| O site não pode exibir a página. | O usuário poderá receber esse erro ao tentar acessar o aplicativo publicado se o aplicativo for um aplicativo IWA. O SPN definido para esse aplicativo pode estar incorreto. Para aplicativos IWA, certifique-se de que o SPN configurado para este aplicativo esteja correto. |
| O site não pode exibir a página. | O usuário poderá ver esse erro ao tentar acessar o aplicativo publicado se o aplicativo for um aplicativo OWA. Isso pode ser causado por um dos seguintes motivos: <br><li>O SPN definido para este aplicativo está incorreto. Certifique-se de que o SPN configurado para este aplicativo esteja correto.</li><li>O usuário que tentou acessar o aplicativo está usando uma conta da Microsoft em vez da conta corporativa apropriada para entrar, ou o usuário é um usuário convidado. Verifique se o usuário faz logon usando sua conta corporativa correspondente ao domínio do aplicativo publicado. Convidados e usuários de Conta da Microsoft não podem acessar aplicativos IWA.</li><li>O usuário que tentou acessar o aplicativo não está definido corretamente para esse aplicativo no lado do local. Certifique-se de que esse usuário tenha as permissões apropriadas, conforme definido para este aplicativo de back-end no computador local. |
| Não foi possível acessar este aplicativo corporativo. Você não está autorizado a acessar este aplicativo. Falha na autorização. Certifique-se de atribuir o acesso a este aplicativo ao usuário. | Os usuários podem receber esse erro ao tentar acessar o aplicativo publicado se eles usarem contas da Microsoft no lugar da respectiva conta corporativa para se conectar. Os usuários convidados também podem receber esse erro. Convidados e usuários de Conta da Microsoft não podem acessar aplicativos IWA. Verifique se o usuário faz logon usando sua conta corporativa correspondente ao domínio do aplicativo publicado.<br><br>Você pode não ter atribuído o usuário para esse aplicativo. Vá para a guia **Aplicativo** e, em **Usuários e Grupos**, atribua esse usuário ou grupo de usuários a esse aplicativo. |
| Não foi possível acessar esse aplicativo corporativo no momento. Tente novamente mais tarde... O conector atingiu o tempo limite. | Os usuários poderão receber esse erro ao tentar acessar o aplicativo publicado se eles não tiverem sido definidos corretamente para esse aplicativo no lado local. Verifique se os usuários têm as permissões adequadas, conforme definido para esse aplicativo de back-end no computador local. |
| Não foi possível acessar este aplicativo corporativo. Você não está autorizado a acessar este aplicativo. Falha na autorização. Certifique-se de que o usuário tenha licença para Azure Active Directory Premium ou Basic. | Os usuários poderão receber esse erro ao tentar acessar o aplicativo que você publicou se eles não tiverem sido explicitamente atribuídos a uma licença Premium/Basic pelo administrador do assinante. Acesse a guia **Licenças** do Active Directory do assinante e certifique-se de que esse usuário ou grupo tenha uma licença Premium ou Basic atribuída. |

## <a name="my-error-wasnt-listed-here"></a>Meu erro não estava listado aqui

Se você encontrar um erro ou problema com o Proxy de Aplicativo do Azure AD que não está listado neste guia de solução de problemas, conte-nos. Envie um email para nossa [equipe de comentários](mailto:aadapfeedback@microsoft.com) com os detalhes do erro encontrado.

## <a name="see-also"></a>Consulte também
* [Habilitar o Proxy de Aplicativo para o Azure Active Directory](active-directory-application-proxy-enable.md)
* [Publique aplicativos com proxy de aplicativo](active-directory-application-proxy-publish.md)
* [Habilitar logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
