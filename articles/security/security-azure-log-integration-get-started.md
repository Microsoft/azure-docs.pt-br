---
title: "Introdução à integração de log do Azure | Microsoft Docs"
description: "Saiba como instalar o serviço de integração do log do Azure e como integrar logs do armazenamento do Azure, dos Logs de Auditoria do Azure e dos alertas da Central de Segurança do Azure."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9d39ecd513386b75b4b640721f80991caaf9ade8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integração do log do Azure com o log de diagnóstico do Azure e encaminhamento de eventos do Windows
A integração do log do Azure (AzLog) permite integrar registros brutos de recursos do Azure em seus sistemas de SIEM (Segurança da Informação e Gerenciamento de Evento) locais. Essa integração proporciona um painel de segurança unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança associados aos aplicativos.
>[!NOTE]
Para obter mais informações sobre a Integração do Log do Azure, examine a [Visão geral da Integração do Log do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview).

Este artigo ajudará você a começar a usar a Integração do Log do Azure, concentrando-se na instalação do serviço Azlog e integrando o serviço de Diagnóstico do Azure. O serviço Integração do Log do Azure poderá coletar informações de Log de Eventos do Windows do canal de eventos de segurança do Windows de máquinas virtuais implantadas no Azure IaaS. Isso é muito semelhante ao “Encaminhamento de eventos” que você pode ter usado localmente.

>[!NOTE]
>A capacidade de trazer a saída de integração do log do Azure para o SIEM é fornecida pelo próprio SIEM. Consulte o artigo [Integrando a integração do log do Azure com o SIEM local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) para obter mais informações.

Para deixar claro: o serviço Integração do Log do Azure é executado em um computador físico ou virtual que usa o sistema operacional Windows Server 2008 R2 ou superior (de preferência Windows Server 2012 R2 ou Windows Server 2016).

O computador físico pode ser executado localmente (ou em um site do hoster). Se você optar por executar o serviço Integração do Log do Azure em uma máquina virtual, essa poderá estar localizada localmente ou em uma nuvem pública, como o Microsoft Azure.

A máquina virtual ou o computador físico que executa o serviço Integração do Log do Azure requer conectividade de rede com a nuvem pública do Azure. As etapas neste artigo fornecem detalhes sobre a configuração.

## <a name="prerequisites"></a>Pré-requisitos
No mínimo, a instalação do AzLog requer os seguintes itens:
* Uma **assinatura do Azure**. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma **conta de armazenamento** que pode ser usada para o log de diagnóstico do Microsoft Azure (você pode usar uma conta de armazenamento pré-configurada ou criar uma nova; demonstraremos como configurar a conta de armazenamento mais adiante neste artigo)
  >[!NOTE]
  Dependendo do cenário, poderá não ser necessário ter uma conta de armazenamento. Para o cenário de diagnóstico do Azure abordado neste artigo, é necessário.
* **Dois sistemas**: um computador que executará o serviço Integração do Log do Azure e um computador que será monitorado e terá suas informações de registro em log enviadas para o computador de serviço do Azlog.
   * Um computador que você deseja monitorar, o qual é uma VM em execução como uma [Máquina Virtual do Azure](../virtual-machines/virtual-machines-windows-overview.md)
   * Um computador que executará o serviço de integração do log do Azure; esse computador coletará todas as informações de log que posteriormente serão importadas para o SIEM.
    * Esse sistema pode ser local ou no Microsoft Azure.  
    * Ele precisa ser executado em uma versão x64 do Windows server 2008 R2 SP1 ou superior e ter o .NET 4.5.1 instalado. Você pode determinar a versão do .NET instalada seguindo o artigo intitulado [Como determinar qual versões do .NET Framework estão instaladas](https://msdn.microsoft.com/library/hh925568)  
    Ele deve ter conectividade com a conta de armazenamento do Azure usada para o log de diagnóstico do Azure. Forneceremos as instruções sobre como confirmar essa conectividade posteriormente neste artigo

Para ver uma demonstração rápida do processo de criar uma máquina virtual usando o Portal do Azure, veja o vídeo abaixo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>Considerações de implantação
Ao testar a integração do Log do Azure, você pode usar qualquer sistema que atenda aos requisitos mínimos do sistema operacional. No entanto, para um ambiente de produção, a carga pode exigir planejamento aumentar ou reduzir.

Será possível executar diversas instâncias do serviço Integração do Log do Azure (uma instância por máquina virtual ou física) se o volume de eventos for alto. Além disso, o balanceamento de carga de contas de armazenamento do Diagnóstico do Azure para Windows (WAD) e o número de assinaturas a serem fornecidas às instâncias devem se basear em sua capacidade.
>[!NOTE]
Neste momento, não temos recomendações específicas para quando expandir as instâncias de computadores da Integração do Log do Azure (ou seja, computadores que executam o serviço de integração do Log do Azure), ou para contas de armazenamento ou assinaturas. Decisões de escala devem ser baseadas na observações do desempenho em cada uma dessas áreas.

Também há a opção de aumentar o serviço Integração do Log do Azure para ajudar a melhorar o desempenho. As métricas de desempenho a seguir podem ajudar a dimensionar as máquinas que foram escolhidas para execução no serviço de integração do Log do Azure:
* Em uma máquina com oito processadores (núcleos), uma única instância do Integrador do Azlog pode processar cerca de 24 milhões de eventos por dia (~1M/hora).

* Em um computador com quatro processadores (núcleos), uma única instância do integrador Azlog pode processar cerca de 1,5 milhão de eventos por dia (~62,5K/hora).

## <a name="install-azure-log-integration"></a>Instalar a integração do log do Azure
Para instalar a Integração do Log do Azure, você precisa baixar o arquivo de instalação da [Integração do Log do Azure](https://www.microsoft.com/download/details.aspx?id=53324). Execute a rotina de instalação e decida se você deseja fornecer informações de telemetria para a Microsoft.  

![Tela de instalação com caixa de telemetria marcada](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> Recomendamos permitir que a Microsoft colete dados de telemetria. Você pode desativar a coleta dos dados de telemetria desmarcando essa opção.
>


O serviço Integração do Log do Azure coleta os dados de telemetria do computador no qual ele está instalado.  

Os dados de telemetria coletados são:

* Exceções que ocorrem durante a execução da integração do log do Azure
* Métricas sobre o número de consultas e eventos processados
* Estatísticas sobre quais opções de linha de comando do Azlog.exe estão sendo usadas

O processo de instalação é abordado no vídeo abaixo.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>Etapas pós-instalação e validação
Depois de concluir a rotina de instalação básica, você estará pronto para realizar as etapas pós-instalação e de validação:
1. Abra uma janela do PowerShell com privilégios elevados e navegue para **c:\Program Files\Microsoft Azure Log Integration**
2. A primeira etapa que você precisa concluir é importar os cmdlets do AzLog. Faça isso executando o script **LoadAzlogModule.ps1** (Observe o “.\” no comando a seguir). Digite **.\LoadAzlogModule.ps1** e pressione **ENTER**.  
Agora você verá algo como o mostrado na figura abaixo. </br></br>
![Tela de instalação com caixa de telemetria marcada](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. Agora você precisa configurar o AzLog para usar um ambiente específico do Azure. Um “ambiente do Azure” é o “tipo” de data center na nuvem do Azure com o qual você deseja trabalhar. Embora existam vários ambientes do Azure atualmente, as opções relevantes no momento são **AzureCloud** ou **AzureUSGovernment**.   Em seu ambiente do PowerShell com privilégios elevados, verifique se você está em **c:\program files\Microsoft Azure Log Integration\** </br></br>
    Quando estiver lá, execute o comando: </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (para Azure comercial)

      >[!NOTE]
      Quando o comando for bem-sucedido, você não receberá nenhum comentário.  Se você deseja usar a nuvem do Azure Governamental dos EUA, use **AzureUSGovernment** (para a variável -Name) para a nuvem governamental dos EUA. Não há suporte para outras nuvens do Azure no momento.  
4. Antes de poder monitorar um sistema, você precisará do nome da conta de armazenamento usada no Diagnóstico do Azure.  No Portal do Azure, navegue até **Máquinas virtuais** e procure a máquina virtual que será monitorada. Na seção **Propriedades**, escolha **Configurações de Diagnóstico**.  Clique em **Agente** e anote o nome da conta de armazenamento especificada. Você precisará desse nome de conta para uma etapa posterior.
![Configurações do Diagnóstico do Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Configurações do Diagnóstico do Azure](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      Se o Monitoramento não foi habilitado durante a criação da máquina virtual, você terá a opção de habilitá-lo, conforme mostrado acima.
5. Agora voltaremos nossa atenção para o computador da Integração do Log do Azure. Precisamos verificar se você tem conectividade com a conta de armazenamento do sistema no qual você instalou a Integração do Log do Azure. O computador físico ou máquina virtual que executa o serviço Integração do Log do Azure precisa acessar a conta de armazenamento para recuperar informações registradas pelo Diagnóstico do Azure, conforme configurado em cada um dos sistemas monitorados.  
  1. Você pode baixar o Gerenciador de Armazenamento do Azure [aqui](http://storageexplorer.com/).
  2. Executar a rotina de instalação
  3. Quando a instalação for concluída, clique em **Avançar** e deixe a caixa de seleção **Iniciar o Gerenciador de Armazenamento do Microsoft Azure** marcada.  
  4. Fazer logon no Azure.
  5. Verifique se que você pode ver a conta de armazenamento que você configurou para o Diagnóstico do Azure.  
![Contas de armazenamento](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. Observe que há algumas opções em contas de armazenamento. Uma delas é **Tabelas**. Em **Tabelas**, você deverá ver uma chamada **WADWindowsEventLogsTable**. </br></br>
   ![Contas de armazenamento](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Integrar o log de Diagnóstico do Azure
Nesta etapa, configurará o computador que executa o serviço Integração do Log do Azure para conectar-se à conta de armazenamento que contém os arquivos de log.
Para concluir esta etapa, precisaremos de algumas coisas antecipadamente.  
* **FriendlyNameForSource:** esse é um nome amigável que você pode aplicar à conta de armazenamento configurada na máquina virtual para armazenar informações de Diagnóstico do Azure
* **StorageAccountName:** esse é o nome da conta de armazenamento que você especificou quando configurou o Diagnóstico do Azure.  
* **StorageKey:** essa é a chave de armazenamento para a conta de armazenamento em que as informações de diagnóstico do Azure são armazenadas para esta máquina virtual.  

Execute as seguintes etapas para obter a chave de armazenamento:
 1. Navegue até o [Portal do Azure](http://portal.azure.com).
 2. No painel de navegação do console do Azure, role até a parte inferior e clique em **Mais serviços.**

 ![Mais serviços](./media/security-azure-log-integration-get-started/more-services.png)
 3. Digite **Armazenamento** na caixa de texto **Filtro**. Clique em **Contas de armazenamento** (isso será exibido depois que você inserir **Armazenamento**)

   ![caixa de filtro](./media/security-azure-log-integration-get-started/filter.png)
 4. Será exibida uma lista de contas de armazenamento, clique duas vezes na conta que você atribuiu ao armazenamento de Log.

   ![lista de contas de armazenamento](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Clique em **Chaves de acesso** na seção **Configurações**.

  ![teclas de acesso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Copie **key1** e coloque-a em um local seguro que você possa acessar para a próxima etapa.

   ![duas chaves de acesso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. No servidor em que você instalou a Integração do Log do Azure, abra um Prompt de comandos com privilégios elevados (observe que estamos usando uma janela de Prompt de comandos com privilégios elevados aqui, não um console do PowerShell com privilégios elevados).
 8. Navegue para **c:\Program Files\Microsoft Azure Log Integration**
 9. Execute o ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> Por exemplo ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` Se você quiser que a ID da assinatura apareça no XML do evento, acrescente a ID da assinatura ao nome amigável:``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` ou por exemplo, ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
Aguarde até 60 minutos e exiba os eventos extraídos da conta de armazenamento. Para exibir, abra **Visualizador de Eventos > Logs do Windows > Eventos Encaminhados** no Integrador do Azlog.

Aqui você pode ver um vídeo sobre as etapas abordadas acima.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>E se os dados não estiverem sendo mostrados na pasta Eventos Encaminhados?
Se depois de uma hora os dados não estiverem sendo mostrados na pasta **Eventos Encaminhados** :

1. Verifique o computador que executa o serviço Integração do Log do Azure e confirme se ele pode acessar o Azure. Para testar a conectividade, tente abrir o [portal do Azure](http://portal.azure.com) do navegador.
2. Verifique se a conta de usuário **Azlog** tem permissão de gravação na pasta **users\Azlog**.
  <ol type="a">
   <li>Abra o **Windows Explorer** </li>
  <li> Navegue para **c:\users** </li>
  <li> Clique com o botão direito do mouse em **c:\users\Azlog** </li>
  <li> Clique em **Segurança**  </li>
  <li> Clique em **NT Service\Azlog** e verifique as permissões da conta. Se a conta estiver ausente nessa guia ou se as permissões apropriadas não forem mostradas no momento, você poderá conceder os direitos de conta nesta guia.</li>
  </ol>
3. Verifique se a conta de armazenamento adicionada no comando **Azlog source add** é listada quando o comando **Azlog source list** é executado.
4. Vá para **Visualizador de Eventos > Logs do Windows > Aplicativo** para ver se há erros relatados da integração de log do Azure.


Se você tiver algum problema durante a instalação e configuração, abra uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md) e selecione **Integração de Log** como o serviço para o qual você está solicitando suporte.

Outra opção de suporte é o [Fórum do MSDN de Integração do Log do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Nele a comunidade apoiar uns aos outros com perguntas, respostas, dicas e truques sobre como aproveitar ao máximo a Integração do Log do Azure. Além disso, a equipe de Integração do Log do Azure monitora esse fórum e ajuda sempre que possível.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Integração do Log do Azure, consulte os seguintes documentos:

* [Integração de log do Microsoft Azure para os logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324) – visite o Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração de log do Azure.
* [Introdução à integração de log do Azure](security-azure-log-integration-overview.md) – este documento apresenta a integração do registro do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar. Esta é a nossa orientação atual sobre como configurar os componentes do SIEM. Primeiro, entre em contato com seu fornecedor do SIEM para obter detalhes adicionais.
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos Logs de Atividade do Azure, com o Log Analytics ou com a solução SIEM.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de Auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.
