---
title: Implantar a ferramenta de diagnóstico para a Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como implantar a ferramenta de UX de diagnóstico para a Área de Trabalho Virtual do Windows (clássica).
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ed010b3eed875c8b14892a588be13a32fbbd95ac
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445084"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Implantar a ferramenta de diagnóstico da Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

Veja o que a ferramenta de diagnóstico para a Área de Trabalho Virtual do Windows pode fazer por você:

- Pesquisar atividades de diagnóstico (gerenciamento, conexão ou feed) para um usuário durante um período de uma semana.
- Reunir informações de host da sessão para atividades de conexão do workspace do Log Analytics.
- Examinar os detalhes de desempenho da VM (máquina virtual) para um host específico.
- Ver quais usuários estão conectados ao host da sessão.
- Enviar mensagem para usuários ativos em um host da sessão específico.
- Desconectar usuários de um host da sessão.

## <a name="prerequisites"></a>Pré-requisitos

Crie um Registro de Aplicativo do Azure Active Directory e um workspace do Log Analytics para implantar o modelo do Azure Resource Manager para a ferramenta. Você ou o administrador precisa destas permissões para fazer isso:

- Proprietário da assinatura do Azure
- Permissão para criar recursos em sua assinatura do Azure
- Permissão para criar um aplicativo do Azure AD
- Direitos de Proprietário ou Colaborador do RDS

Você também precisa instalar estes dois módulos do PowerShell antes de começar:

- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps)
- [Módulo do Azure AD](/powershell/azure/active-directory/install-adv2)

Verifique se sua ID de assinatura está pronta para quando você entrar.

Depois de ter tudo em ordem, você poderá criar o registro de aplicativo do Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar um registro de aplicativo do Azure Active Directory

Esta seção mostrará como usar o PowerShell para criar o aplicativo do Azure Active Directory com uma entidade de serviço e obter permissões de API para ela.

>[!NOTE]
>As permissões de API são Área de Trabalho Virtual do Windows. As permissões de API do Log Analytics e do Microsoft Graph são adicionadas ao aplicativo do Azure Active Directory.

1. Abra o PowerShell como Administrador.
2. Entre no Azure com uma conta que tenha permissões de Proprietário ou Colaborador na assinatura do Azure que você gostaria de usar para a ferramenta de diagnóstico:
   ```powershell
   Login-AzAccount
   ```
3. Entre no Azure AD com a mesma conta:
   ```powershell
   Connect-AzureAD
   ```
4. Acesse o [repositório GitHub do RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateADAppRegistrationforDiagnostics.ps1** no PowerShell.
5.  Quando o script solicitar que você nomeie seu aplicativo, insira um nome de aplicativo exclusivo.


Depois que o script for executado com êxito, ele deverá mostrar o seguinte na saída:

-  Uma mensagem que confirma que seu aplicativo agora tem uma atribuição de função de entidade de serviço.
-  A ID do cliente e a chave secreta do cliente que será necessária quando você implantar a ferramenta de diagnóstico.

Agora que você registrou seu aplicativo, é hora de configurar o workspace do Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Configurar o workspace do Log Analytics

Para obter a melhor experiência possível, recomendamos que você configure o workspace do Log Analytics com os contadores de desempenho a seguir que permitem derivar instruções da experiência do usuário em uma sessão remota. Para obter uma lista de contadores recomendados com limites sugeridos, confira [Limites do contador de desempenho do Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Criar um workspace do Azure Log Analytics usando o PowerShell

Você pode executar um script do PowerShell para criar um workspace do Log Analytics e configurar os contadores de desempenho do Windows recomendados para monitorar a experiência do usuário e o desempenho do aplicativo.

>[!NOTE]
>Se você já tiver um workspace do Log Analytics existente que você fez sem o script do PowerShell que deseja usar, pule para [Validar os resultados do script no portal do Azure](#validate-the-script-results-in-the-azure-portal).

Para executar o script do PowerShell:

1.  Abrir o PowerShell como administrador.
2.  Acesse o [repositório GitHub do RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** no PowerShell.
3. Insira os seguintes valores para os parâmetros:

    - Para **ResourceGroupName**, insira o nome para o grupo de recursos.
    - Para **LogAnalyticsWorkspaceName**, insira um nome exclusivo para o workspace do Log Analytics.
    - Para **Localização**, insira a região do Azure que você está usando.
    - Insira a **ID da Assinatura do Azure**, que pode ser encontrada no portal do Azure em **Assinaturas**.

4. Insira as credenciais de um usuário com acesso de administrador delegado.
5. Entre no portal do Azure com as mesmas credenciais do usuário.
6. Anote ou memorize a ID do LogAnalyticsWorkspace para depois.
7. Se você já tiver configurado o workspace do Log Analytics com o script do PowerShell, os contadores de desempenho já deverão estar configurados e você poderá pular para [Validar os resultados do script no portal do Azure](#validate-the-script-results-in-the-azure-portal). Caso contrário, prossiga para a próxima seção.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configurar contadores de desempenho do Windows no workspace do Log Analytics existente

Esta seção é para os usuários que desejam usar um workspace do Azure Log Analytics existente criado sem o script do PowerShell na seção anterior. Se você não tiver usado o script, precisará configurar os contadores de desempenho do Windows recomendados manualmente.

Veja como configurar manualmente os contadores de desempenho recomendados:

1. Abra seu navegador da Internet e entre no [portal do Azure](https://portal.azure.com/) com sua conta administrativa.
2. Em seguida, acesse os **workspaces do Log Analytics** para examinar os contadores de desempenho do Windows configurados.
3. Na seção **Configurações**, selecione **Configurações avançadas**.
4. Depois disso, navegue até **Dados** > **Contadores de Desempenho do Windows** e adicione os seguintes contadores:

    -   LogicalDisk(\*)\\%Free Space
    -   LogicalDisk(C:)\\Avg. Disk Queue Length
    -   Memory(\*)\\Available Mbytes
    -   Processor Information(\*)\\Processor Time
    -   User Input Delay per Session(\*)\\Max Input Delay

Saiba mais sobre os contadores de desempenho em [Fontes de dados de desempenho do Windows e do Linux no Azure Monitor](../../azure-monitor/agents/data-sources-performance-counters.md).

>[!NOTE]
>Contadores adicionais que você configurar não aparecerão na ferramenta de diagnóstico em si. Para que eles sejam exibidos na ferramenta de diagnóstico, você precisa configurar o arquivo de configuração da ferramenta. As instruções sobre como fazer isso com a administração avançada estarão disponíveis no GitHub em uma data posterior.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Validar os resultados do script no portal do Azure

Antes de continuar a implantação da ferramenta de diagnóstico, recomendamos que você verifique se o aplicativo do Azure Active Directory tem permissões de API e se o workspace do Log Analytics tem os contadores de desempenho do Windows pré-configurados.

### <a name="review-your-app-registration"></a>Examinar o registro do seu aplicativo

Para verificar se o seu registro de aplicativo tem permissões de API:

1. Abra um navegador e conecte-se ao [portal do Azure](https://portal.azure.com/) com sua conta de administrador.
2. Acesse o **Azure Active Directory**.
3. Acesse os **Registros de aplicativo** e selecione **Todos os Aplicativos**.
4. Procure seu registro de aplicativo do Azure AD com o mesmo nome de aplicativo que você inseriu na etapa 5 de [Criar um registro de aplicativo do Azure Active Directory](deploy-diagnostics.md#create-an-azure-active-directory-app-registration).

### <a name="review-your-log-analytics-workspace"></a>Examinar o workspace do Log Analytics

Para verificar se o workspace do Log Analytics tem os contadores de desempenho do Windows pré-configurados:

1. No [portal do Azure](https://portal.azure.com/), acesse **workspaces do Log Analytics** para examinar os contadores de desempenho do Windows configurados.
2. Em **Configurações**, selecione **Configurações avançadas**.
3. Depois disso, acesse **Dados** > **Contadores de Desempenho do Windows**.
4. Verifique se os seguintes contadores estão pré-configurados:

   - LogicalDisk(\*)\\%Free Space: exibe a quantidade de espaço livre do espaço total utilizável no disco como um percentual.
   - LogicalDisk(C:)\\Avg. Disk Queue Length: o comprimento da solicitação de transferência de disco para sua unidade C. O valor não deve exceder 2 por mais de um curto período.
   - Memory(\*)\\Available Mbytes: a memória disponível para o sistema em megabytes.
   - Processor Information(\*)\\Processor Time: o percentual de tempo decorrido que o processador gasta para executar um thread não ocioso.
   - User Input Delay per Session(\*)\\Max Input Delay

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Conectar-se a VMs em seu workspace do Log Analytics

Para ver a integridade das VMs, você precisará habilitar a conexão do Log Analytics. Siga estas etapas para se conectar suas VMs:

1. Abra um navegador e entre no [portal do Azure](https://portal.azure.com/) com sua conta de administrador.
2. Acesse o workspace do Log Analytics.
3. No painel esquerdo, em Fontes de Dados do Workspace, selecione **máquinas virtuais**.
4. Selecione o nome da VM à qual você deseja se conectar.
5. Selecione **Conectar**.

## <a name="deploy-the-diagnostics-tool"></a>Implantar a ferramenta de diagnóstico

Para implantar o modelo de Gerenciamento de Recursos do Azure para a ferramenta de diagnóstico:

1.  Vá até a [página de Modelos de RDS do Azure no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Implante o modelo no Azure e siga as instruções no modelo. Verifique se você tem as seguintes informações disponíveis:

    -   Client-Id
    -   Client-Secret
    -   ID do espaço de trabalho do Log Analytics

3.  Depois que os parâmetros de entrada forem fornecidos, aceite os termos e condições e selecione **Comprar**.

A implantação levará de 2 a 3 minutos. Após a implantação bem-sucedida, acesse o grupo de recursos e verifique se o aplicativo Web e os recursos do plano do Serviço de Aplicativo estão lá.

Depois disso, você precisa definir o URI de redirecionamento.

### <a name="set-the-redirect-uri"></a>Definir o URI de Redirecionamento

Para definir o URI de Redirecionamento:

1.  No [portal do Azure](https://portal.azure.com/), vá para **Serviços de Aplicativos** e localize o aplicativo que você criou.
2.  Acesse a página de visão geral e copie a URL que encontrar.
3.  Navegue até **Registros de aplicativo** e selecione o aplicativo que você deseja implantar.
4.  No painel esquerdo, na seção Gerenciar, selecione **Autenticação**.
5.  Insira o URI de Redirecionamento desejado na caixa de texto **URI de Redirecionamento** e selecione **Salvar** no canto superior esquerdo do menu.
6. Selecione **Web** no menu suspenso em Tipo.
7. Insira a URL na página de visão geral do aplicativo e adicione **/security/signin-callback** ao final dela. Por exemplo: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![A página do URI de redirecionamento](../media/redirect-uri-page.png)

8. Agora, acesse seus recursos do Azure, selecione o recurso dos Serviços de Aplicativos do Azure com o nome fornecido no modelo e navegue até a URL associada a ele. (Por exemplo, se o nome do aplicativo usado no modelo era `contosoapp45`, então a URL associada é <http://contoso.azurewebsites.net>).
9. Entre usando a conta de usuário do Azure Active Directory apropriada.
10.   Selecione **Aceitar**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuir a ferramenta de diagnóstico

Antes de disponibilizar a ferramenta de diagnóstico para seus usuários, verifique se eles têm as seguintes permissões:

- Os usuários precisam de acesso de leitura para a análise de logs. Para obter mais informações, confira [Introdução às funções, permissões e segurança com o Azure Monitor](../../azure-monitor/roles-permissions-security.md).
-  Os usuários também precisam de acesso de leitura para o locatário da Área de Trabalho Virtual do Windows (função Leitor do RDS). Para obter mais informações, confira [Acesso delegado na Área de Trabalho Virtual do Windows](delegated-access-virtual-desktop-2019.md).

Você também precisa dar aos seus usuários as seguintes informações:

- A URL do aplicativo
- Os nomes do locatário individual do grupo de locatários que eles podem acessar.

## <a name="use-the-diagnostics-tool"></a>Use a ferramenta de diagnóstico

Depois de entrar na sua conta usando as informações que você recebeu da sua organização, deixe o UPN pronto para o usuário para o qual você deseja consultar as atividades. Uma pesquisa fornecerá a você todas as atividades sob o tipo de atividade especificado que ocorreram na última semana.

### <a name="how-to-read-activity-search-results"></a>Como ler os resultados da pesquisa de atividade

As atividades são classificadas por carimbo de data/hora, com a atividade mais recente primeiro. Se os resultados retornarem um erro, primeiro verifique se é um erro de serviço. Para erros de serviço, crie um tíquete de suporte com as informações da atividade para nos ajudar a depurar o problema. Todos os outros tipos de erro geralmente podem ser resolvidos pelo usuário ou administrador. Para obter uma lista dos cenários de erro mais comuns e como resolvê-los, confira [Identificar e diagnosticar problemas](diagnostics-role-service-2019.md#common-error-scenarios).

>[!NOTE]
>Os erros de serviço são chamados de "erros externos" na documentação vinculada. Isso será alterado quando atualizarmos a referência do PowerShell.

As atividades de conexão podem ter mais de um erro. Você pode expandir o tipo de atividade para ver outros erros que o usuário tenha encontrado. Selecione o nome do código de erro para abrir uma caixa de diálogo e ver mais informações sobre ele.

### <a name="investigate-the-session-host"></a>Investigar o host da sessão

Nos resultados da pesquisa, localize e selecione o host da sessão sobre o qual você deseja obter informações.

Você pode analisar a integridade do host da sessão:

- Com base em um limite predefinido, você pode recuperar as informações de integridade do host da sessão que o Log Analytics consulta.
- Quando não houver atividade ou o host da sessão não estiver conectado ao Log Analytics, as informações não estarão disponíveis.

Você também pode interagir com os usuários no host da sessão:

- Você pode sair ou enviar uma mensagem para usuários conectados.
- O usuário que você pesquisou originalmente está selecionado por padrão, mas você também pode selecionar usuários adicionais para enviar mensagens ou desconectar vários usuários ao mesmo tempo.

### <a name="windows-performance-counter-thresholds"></a>Limites do contador de desempenho do Windows

- LogicalDisk(\*)\\%Free Space:

    - exibe o percentual do espaço total utilizável no disco lógico que está livre.
    - Limite: menos de 20% é marcado como não íntegro.

- LogicalDisk(C:)\\Avg. Disk Queue Length:

    - representa as condições do sistema de armazenamento.
    - Limite: mais de 5 é marcado como não íntegro.

- Memory(\*)\\Available Mbytes:

    - a memória disponível para o sistema.
    - Limite: menos de 500 megabytes é marcado como não íntegro.

- Processor Information(\*)\\Processor Time:

    - Limite: mais de 80% é marcado como não íntegro.

- [User Input Delay per Session(\*)\\Max Input Delay](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Limite: mais de 2.000 ms é marcado como não íntegro.

## <a name="next-steps"></a>Próximas etapas

- Saiba como monitorar logs de atividades em [Usar diagnóstico com o Log Analytics](diagnostics-log-analytics-2019.md).
- Leia sobre cenários de erro comuns e como corrigi-los em [Identificar e diagnosticar problemas](diagnostics-role-service-2019.md).