---
title: "Configuração de diagnóstico para os serviços de nuvem e máquinas virtuais do Azure | Microsoft Docs"
description: "Descreve como configurar informações de diagnóstico para depurar serviços de nuvem e máquinas virtuais (VMs) do Azure no Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: d3c85cde864d599871e195df2e81548180e647ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Configurando o diagnóstico para os serviços de nuvem do Azure e máquinas virtuais
Quando você precisar solucionar problemas de um serviço de nuvem do Azure ou da máquina virtual do Azure, pode configurar o diagnóstico do Azure com mais facilidade usando o Visual Studio. O diagnóstico do Azure captura dados do sistema e dados do log em máquinas virtuais e instâncias de máquinas virtuais que executam o serviço de nuvem e transfere os dados para uma conta de armazenamento de sua escolha. Consulte [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-enable-diagnostic-log.md) para obter mais informações sobre o registro em log de diagnóstico no Azure.

Este tópico mostra como habilitar e configurar o diagnóstico do Azure no Visual Studio, antes e depois da implantação, bem como em máquinas virtuais do Azure. Ele também mostra como selecionar os tipos de informações de diagnóstico para coletar e exibir as informações depois que são coletadas.

Você pode configurar o Diagnóstico do Azure das seguintes maneiras:

* Você pode alterar as definições de configuração de diagnóstico na caixa de diálogo **Configuração de diagnóstico** no Visual Studio. As configurações são salvas em um arquivo chamado diagnostics.wadcfgx (diagnostics.wadcfg no SDK do Azure 2.4 ou anterior). Como alternativa, você pode modificar diretamente o arquivo de configuração. Se você atualizar manualmente o arquivo, as alterações de configuração entrarão em vigor na próxima vez que implantar o serviço de nuvem no Azure ou executar o serviço no emulador.
* Usar **Cloud Explorer** ou **Gerenciador de Servidores** no Visual Studio para alterar as configurações de diagnóstico para um serviço de nuvem ou máquina virtual em execução.

## <a name="azure-26-diagnostics-changes"></a>Alterações de diagnóstico do Azure 2.6
Para projetos do SDK do Azure 2.6 no Visual Studio, as seguintes alterações foram feitas. (Essas alterações também se aplicam a versões mais recentes do SDK do Azure).

* O emulador local agora dá suporte a diagnósticos. Isso significa que você pode coletar dados de diagnóstico e garantir que seu aplicativo está criando os rastreamentos corretos enquanto está desenvolvendo e testando no Visual Studio. A cadeia de conexão `UseDevelopmentStorage=true` habilita a coleta de dados de diagnóstico durante a execução do seu projeto de serviço de nuvem no Visual Studio usando o emulador de armazenamento do Azure. Todos os dados de diagnóstico são coletados na conta de armazenamento (armazenamento de desenvolvimento).
* A cadeia de conexão da conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é armazenada novamente no arquivo de configuração de serviço (.cscfg). No SDK do Azure 2.5, a conta de armazenamento de diagnóstico foi especificada no arquivo diagnostics.wadcfgx.

Há algumas diferenças perceptíveis entre como a cadeia de conexão funcionava no SDK do Azure 2.4 e anteriores e como funciona no SDK do Azure 2.6 e posteriores.

* No SDK do Azure 2.4 e anteriores, a cadeia de conexão era usada como um tempo de execução pelo plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os logs de diagnóstico.
* No SDK do Azure 2.6 e posteriores, a cadeia de conexão de diagnóstico é usada pelo Visual Studio para configurar a extensão de diagnóstico com as informações da conta de armazenamento apropriadas durante a publicação. A cadeia de conexão permite definir contas de armazenamento diferentes para diferentes configurações de serviço que o Visual Studio usará ao publicar. No entanto, como o plug-in de diagnóstico não está mais disponível (após o SDK do Azure 2.5), o arquivo .cscfg sozinho por si só não é capaz de habilitar a extensão de diagnóstico. Você precisa habilitar a extensão separadamente por meio de ferramentas como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração da extensão de diagnóstico com o PowerShell, a saída do pacote do Visual Studio também contém o XML de configuração pública para a extensão de diagnóstico para cada função. O Visual Studio usa a cadeia de conexão de diagnóstico para preencher as informações da conta de armazenamento presentes na configuração pública. Os arquivos de configuração pública são criados na pasta Extensões e seguem o padrão PaaSDiagnostics.&lt;RoleName>.PubConfig.xml. Todas as implantações baseadas em PowerShell podem usar esse padrão para mapear cada configuração para uma função.
* A cadeia de conexão no arquivo .cscfg também é usada pelo [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) para acessar os dados de diagnóstico para que possam aparecer na guia **Monitoramento** . A cadeia de conexão é necessária para configurar o serviço para mostrar dados do monitoramento detalhado no portal.

## <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrando projetos para o SDK do Azure 2.6 e posteriores
Ao migrar do SDK do Azure 2.5 para SDK do Azure 2.6 ou posteriores, se você tiver uma conta de armazenamento de diagnóstico especificada no arquivo de .wadcfgx, lá ela permanecerá. Para aproveitar a flexibilidade de usar diferentes contas de armazenamento para diferentes configurações de armazenamento, você terá de adicionar manualmente a cadeia de conexão ao seu projeto. Se estiver migrando um projeto do SDK 2.4 ou anterior do Azure para o SDK 2.6 do Azure, as cadeias de conexão de diagnóstico serão preservadas. No entanto, observe as alterações na forma como as cadeias de conexão são tratadas no SDK do Azure 2.6 conforme especificado na seção anterior.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se uma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio a usa para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante o empacotamento.
* Se nenhuma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio voltará a usar a conta de armazenamento especificada no arquivo .wadcfgx para configurar a extensão de diagnóstico durante a publicação e ao gerar os arquivos xml de configuração pública durante o empacotamento.
* A cadeia de conexão de diagnóstico no arquivo .cscfg tem precedência sobre a conta de armazenamento no arquivo .wadcfgx. Se uma cadeia de conexão de diagnóstico é especificada no arquivo .cscfg, o Visual Studio a usa e ignora a conta de armazenamento em .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz a caixa de seleção "Atualizar cadeias de conexão do armazenamento de desenvolvimento..." faz a caixa de seleção?
A caixa de seleção **Atualizar cadeias de conexão do armazenamento de desenvolvimento para Diagnóstico e cache com credenciais de conta de armazenamento do Microsoft Azure durante a publicação no Microsoft Azure** oferece uma maneira conveniente para atualizar qualquer cadeia de conexão de conta de armazenamento de desenvolvimento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que você selecione esta caixa de seleção e a cadeia de conexão de diagnóstico especifique `UseDevelopmentStorage=true`. Quando você publicar o projeto no Azure, o Visual Studio atualizará automaticamente a cadeia de conexão de diagnóstico com a conta de armazenamento que você especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de conexão de diagnóstico, então essa conta será usada.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre SDK do Azure 2.4 e anteriores e SDK do Azure 2.5 e posteriores
Se você estiver atualizando seu projeto do SDK do Azure 2.4 para o SDK do Azure 2.5 ou posterior, considere as seguintes diferenças de funcionalidade de diagnóstico.

* **APIs de configuração estão preteridos** – A configuração programática de diagnóstico está disponível no SDK do Azure 2.4 ou anteriores, mas foi preterida no SDK do Azure 2.5 e posteriores. Se sua configuração de diagnóstico está definida atualmente no código, você precisará reconfigurar as configurações do zero no projeto migrado para que o diagnóstico continue a funcionar. O arquivo de configuração de diagnóstico do SDK do Azure 2.4 é diagnostics.wadcfg e para o SDK do Azure 2.5 e posteriores, diagnostics.wadcfgx.
* **Diagnóstico para aplicativos de serviço de nuvem só podem ser configurados no nível de função, não no nível de instância.**
* **Sempre que você implanta seu aplicativo, a configuração de diagnóstico é atualizada** – isso pode causar problemas de paridade, se você altera a configuração de diagnóstico do Gerenciador de Servidores e, em seguida, reimplanta o aplicativo.
* **No SDK do Azure 2.5 e posteriores, despejos de memória são configurados no arquivo de configuração de diagnóstico, não no código** – se você tiver despejos de memória configurados no código, precisará transferir manualmente a configuração de código para o arquivo de configuração, pois os despejos de memória não são transferidos durante a migração para o SDK do Azure 2.6.

## <a name="enable-diagnostics-in-cloud-service-projects-before-deploying-them"></a>Habilitar o diagnóstico em projetos de serviço de nuvem antes de implantá-los
No Visual Studio, você pode escolher coletar dados de diagnóstico para funções executadas no Azure, quando executa o serviço no emulador antes de implantá-lo. Todas as alterações às configurações de diagnóstico no Visual Studio são salvas no arquivo de configuração diagnostics.wadcfgx. Essas definições de configurações especificam a conta de armazenamento na qual os dados de diagnóstico são salvos quando você implanta seu serviço de nuvem.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-enable-diagnostics-in-visual-studio-before-deployment"></a>Para habilitar o diagnóstico no Visual Studio antes da implantação
1. No menu de atalho da função que lhe interessa, escolha **Propriedades** e, em seguida, escolha a guia **Configuração** na janela **Propriedades** da função.
2. Na seção **Diagnóstico**, verifique se a caixa de seleção **Habilitar Diagnóstico** está marcada.
   
    ![Acessando a opção Habilitar Diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Escolha o botão de reticências (...) para especificar a conta de armazenamento onde você deseja que os dados de diagnóstico sejam armazenados. A conta de armazenamento que você escolher será o local onde os dados de diagnósticos serão armazenados.
   
    ![Especifique a conta de armazenamento a ser usada](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. Na caixa de diálogo **Criar cadeia de conexão de armazenamento** , especifique se você deseja se conectar usando o Emulador de armazenamento do Azure, uma assinatura do Azure ou credenciais inseridas manualmente.
   
    ![Caixa de diálogo Conta de armazenamento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Se você escolher a opção Emulador de Armazenamento do Microsoft Azure, a cadeia de conexão é definida como UseDevelopmentStorage=true.
   * Se você escolher a opção sua assinatura, pode escolher a assinatura do Azure que deseja usar e o nome da conta. Você pode escolher o botão Gerenciar contas para gerenciar suas assinaturas do Azure.
   * Se você escolher a opção Credenciais inseridas manualmente, será solicitado a inserir o nome e a chave da conta do Azure que deseja usar.
5. Escolha o botão **Configurar** para exibir a caixa de diálogo **Configuração de diagnóstico**. Cada guia (exceto **Geral** e **Diretórios de log**) representa uma fonte de dados de diagnóstico que você pode coletar. A guia padrão, **Geral**, oferece as seguintes opções de coleta de dados de diagnóstico: **Somente erros**, **Todas as informações** e **Plano personalizado**. A opção padrão, **Somente erros**, traz a menor quantidade de armazenamento porque não transfere avisos ou mensagens de rastreamento. A opção Todas as informações transfere a maioria das informações e é, portanto, a opção mais cara em termos de armazenamento.
   
    ![Habilitar a configuração e diagnóstico do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. Neste exemplo, selecione a opção **Plano personalizado** , assim você pode personalizar os dados coletados.
7. A caixa **Cota de disco em MB** especifica a quantidade de espaço que você deseja alocar na sua conta de armazenamento para dados de diagnóstico. Você pode alterar o valor padrão se desejar.
8. Em cada guia de dados de diagnóstico que deseja coletar, selecione a caixa de seleção **Habilitar transferência de <log type>**. Por exemplo, se você quiser coletar logs de aplicativo, selecione a caixa de seleção **Habilitar transferência dos Logs de aplicativo** da guia **Logs de aplicativos**. Além disso, especifique outras informações necessárias para cada tipo de dados de diagnóstico. Consulte a seção **Configurar fontes de dados de diagnóstico** posteriormente neste tópico para obter informações de configuração em cada guia.
9. Depois de habilitar a coleção de todos os dados de diagnóstico que você deseja, escolha o botão **OK** .
10. Execute seu projeto de serviço de nuvem do Azure no Visual Studio, como de costume. Como você usa seu aplicativo, as informações de log habilitadas são salvas na conta de armazenamento do Azure especificada.

## <a name="enable-diagnostics-in-azure-virtual-machines"></a>Habilitar o diagnóstico em máquinas virtuais do Azure
No Visual Studio, você pode escolher coletar dados de diagnóstico para máquinas virtuais do Azure.

### <a name="to-enable-diagnostics-in-azure-virtual-machines"></a>Para habilitar o diagnóstico em máquinas virtuais do Azure
1. No **Gerenciador de Servidores**, escolha o nó do Azure e conecte-se à sua assinatura do Azure, se você ainda não estiver conectado.
2. Expanda o nó **Máquinas virtuais** . Você pode criar uma nova máquina virtual ou selecione uma que já está lá.
3. No menu de atalho para a máquina virtual que lhe interessa, escolha **Configurar**. Esta mostra a caixa de diálogo configuração da máquina virtual.
   
    ![Configurando uma máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Se ainda não estiver instalado, adicione a extensão do diagnóstico do Microsoft Monitoring Agent. Essa extensão permite que você colete dados de diagnóstico da máquina virtual do Azure. Na lista de extensões instalada, escolha o menu suspenso Selecionar uma extensão disponível e clique em Diagnóstico de Microsoft Monitoring Agent.
   
    ![Instalando uma extensão de máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
   > [!NOTE]
   > Outras extensões de diagnóstico estão disponíveis para as máquinas virtuais. Para obter mais informações, consulte recursos e extensões de VM do Azure.
   > 
   > 
5. Escolha o botão **Adicionar** para adicionar a extensão e exibir sua caixa de diálogo **Configuração de diagnóstico**.
6. Escolha o botão **Configurar** para especificar uma conta de armazenamento e, em seguida, escolha o botão **OK**.
   
    Cada guia (exceto **Geral** e **Diretórios de log**) representa uma fonte de dados de diagnóstico que você pode coletar.
   
    ![Habilitar a configuração e diagnóstico do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    A guia padrão, **Geral**, oferece as seguintes opções de coleta de dados de diagnóstico: **Somente erros**, **Todas as informações** e **Plano personalizado**. A opção padrão, **Somente erros**, traz a menor quantidade de armazenamento porque não transfere avisos ou mensagens de rastreamento. A opção **Todas as informações** transfere a maioria das informações e é, portanto, a opção mais cara em termos de armazenamento.
7. Neste exemplo, selecione a opção **Plano personalizado** , assim você pode personalizar os dados coletados.
8. A caixa **Cota de disco em MB** especifica a quantidade de espaço que você deseja alocar na sua conta de armazenamento para dados de diagnóstico. Você pode alterar o valor padrão se desejar.
9. Em cada guia de dados de diagnóstico que deseja coletar, selecione a caixa de seleção **Habilitar transferência de <log type>**.
   
    Por exemplo, se você quiser coletar logs de aplicativo, selecione a caixa de seleção **Habilitar transferência dos Logs de aplicativo** da guia **Logs de aplicativos**. Além disso, especifique outras informações necessárias para cada tipo de dados de diagnóstico. Consulte a seção **Configurar fontes de dados de diagnóstico** posteriormente neste tópico para obter informações de configuração em cada guia.
10. Depois de habilitar a coleção de todos os dados de diagnóstico que você deseja, escolha o botão **OK** .
11. Salve o projeto atualizado.
    
     Você verá uma mensagem na janela **Log de atividades do Microsoft Azure** que a máquina virtual foi atualizada.

## <a name="configure-diagnostics-data-sources"></a>Configurar fontes de dados de diagnóstico
Após habilitar a coleção de dados de diagnóstico, você pode escolher exatamente quais fontes de dados que deseja coletar e quais informações são coletadas. A seguir está uma lista de guias na caixa de diálogo **Configuração de diagnóstico** e o que significa cada opção de configuração.

### <a name="application-logs"></a>Logs de aplicativos
**Logs de aplicativos** contêm informações de diagnóstico produzidas por um aplicativo Web. Por exemplo, se você quiser coletar logs de aplicativo, selecione a caixa de seleção **Habilitar transferência dos logs de aplicativo** . Você pode aumentar ou diminuir o número de minutos quando os logs de aplicativo são transferidos para sua conta de armazenamento, alterando o valor **Período de transferência (min)** . Você também pode alterar a quantidade de informações capturadas no log definindo o valor de nível de log. Por exemplo, você pode escolher **Detalhado** para obter mais informações ou escolher **Crítico** para capturar apenas os erros críticos. Se você tiver um provedor de diagnósticos específico que emite os logs de aplicativo, pode capturá-los adicionando o GUID do provedor na caixa **GUID do provedor** .

  ![Logs de aplicativos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Consulte [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-enable-diagnostic-log.md) para obter mais informações sobre os logs de aplicativo.

### <a name="windows-event-logs"></a>Logs de eventos do Windows
Por exemplo, se você quiser capturar logs de evento do Windows, selecione a caixa de seleção **Habilitar transferência dos logs de evento do Windows** . Você pode aumentar ou diminuir o número de minutos quando os logs de eventos são transferidos para sua conta de armazenamento, alterando o valor **Período de transferência (min)** . Selecione as caixas de seleção para os tipos de eventos que você deseja controlar.

  ![Logs de eventos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Se você estiver usando o SDK do Azure 2.6 ou posterior e deseja especificar uma fonte de dados personalizada, insira a fonte na caixa de texto **<Data source name>** e, em seguida, escolha o botão **Adicionar** ao lado dela. A fonte de dados é adicionada ao arquivo diagnostics.cfcfg.

Se você estiver usando o SDK do Azure 2.5 e deseja especificar uma fonte de dados personalizada, você pode adicioná-la para a seção `WindowsEventLog` do arquivo diagnostics.wadcfgx, como o exemplo a seguir.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Contadores de desempenho
As informações do contador de desempenho podem ajudá-lo a localizar afunilamentos do sistema e ajustar o sistema e desempenho do aplicativo. Consulte [Criar e usar contadores de desempenho em um aplicativo do Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx) para obter mais informações. Se você deseja capturar os contadores de desempenho, selecione a caixa de seleção **Habilitar a transferência de contadores de desempenho** . Você pode aumentar ou diminuir o número de minutos quando os logs de eventos são transferidos para sua conta de armazenamento, alterando o valor **Período de transferência (min)** . Selecione as caixas de seleção para os contadores de desempenho que você deseja controlar.

  ![Contadores de desempenho](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Para controlar um contador de desempenho que não está listado, insira-o usando a sintaxe sugerida e escolha o botão **Adicionar** . O sistema operacional na máquina virtual determina quais contadores de desempenho que você pode controlar. Para obter mais informações sobre a sintaxe, consulte [Especificando um caminho de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Logs de infraestrutura
Se você desejar capturar logs de infraestrutura que contêm informações sobre a infraestrutura de diagnóstico do Azure, o módulo RemoteAccess e o módulo RemoteForwarder, selecione a caixa de seleção **Habilitar a transferência de Logs de infraestrutura** . Você pode aumentar ou diminuir o número de minutos quando os logs de eventos são transferidos para sua conta de armazenamento alterando o valor do Período de transferência (min).

  ![Logs de infraestrutura de diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Para saber mais, consulte [Coletar dados do log usando o Diagnóstico do Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) .

### <a name="log-directories"></a>Diretórios de log
Se você deseja capturar os diretórios de log que contêm os dados coletados dos diretórios de log das solicitações de Serviços de Informações da Internet (IIS), solicitações com falha ou pastas que você escolher, selecione a caixa de seleção **Habilitar transferência de diretórios de Log** . Você pode aumentar ou diminuir o número de minutos quando os logs de eventos são transferidos para sua conta de armazenamento alterando o valor do **Período de transferência (min)** .

Você pode marcar as caixas dos logs que deseja coletar, tais como os logs **Logs do IIS** e **Solicitação com falha**. Os nomes de contêiner de armazenamento padrão são fornecidos, mas você pode alterar os nomes, se desejar.

Além disso, você pode capturar logs de qualquer pasta. Basta especificar o caminho na seção **Log do diretório absoluto** e, em seguida, escolha o botão **Adicionar diretório**. Os logs serão capturados para os contêineres especificados.

  ![Diretórios de log](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Logs do ETW
Se você usar [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (Rastreamento de Eventos para Windows) e desejar capturar logs de ETW, selecione a caixa de seleção **Habilitar transferência de logs de ETW**. Você pode aumentar ou diminuir o número de minutos quando os logs de eventos são transferidos para sua conta de armazenamento alterando o valor do **Período de transferência (min)** .

Os eventos são capturados das origens de eventos e manifestos de evento que você especificar. Para especificar uma origem de evento, insira um nome na seção **Origens de evento** e, em seguida, escolha o botão **Adicionar origem de evento**. Da mesma forma, você pode especificar um manifesto de evento na seção **Manifestos de evento**s e, em seguida, escolha o botão **Adicionar manifesto de evento**.

  ![Logs do ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  A estrutura do ETW tem suporte no ASP.NET por meio de classes no namespace [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics(v=vs.110). O namespace Microsoft.WindowsAzure.Diagnostics, que herda e estende as classes padrão [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics(v=vs.110), permite o uso de [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) como uma estrutura de registros no ambiente do Azure. Para obter mais informações, consulte [Tomar controle de registro em log e rastreamento no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Habilitando o diagnóstico no serviços de nuvem e Máquinas virtuais do Azure](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Despejos de falhas
Se você quiser capturar informações de quando falha uma instância de função, selecione a caixa de seleção **Habilitar transferência de despejos de memória**. (Pelo fato do ASP.NET manipular mais exceções, isso geralmente é útil somente para uma função de trabalho.) Você pode aumentar ou diminuir o percentual de espaço de armazenamento dedicado aos despejos de memória, alterando o valor **Cota de diretório (%)**. Você pode alterar o contêiner de armazenamento no qual os despejos de memória são armazenados e selecionar se deseja capturar um despejo **Completo** ou **Mini**.

Os processos que estão sendo controlados no momento são listados. Selecione as caixas de seleção para os processos que você deseja capturar. Para adicionar outro processo à lista, insira o nome do processo e, em seguida, escolha o botão **Adicionar processo** .

  ![Despejos de falhas](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Consulte [Tomar controle do registro em log e rastreamento no Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) e [Diagnóstico do Microsoft Azure parte 4: componentes de registro de log personalizados e alterações do Diagnóstico do Azure 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) para obter mais informações.

## <a name="view-the-diagnostics-data"></a>Exibir os dados de diagnóstico
Depois que você coletou os dados de diagnóstico para um serviço de nuvem ou uma máquina virtual, pode exibi-lo.

### <a name="to-view-cloud-service-diagnostics-data"></a>Para exibir dados de diagnóstico do serviço de nuvem
1. Implante seu serviço de nuvem como de costume e, em seguida, execute-o.
2. Você pode exibir os dados de diagnóstico em um relatório que o Visual Studio gera ou em tabelas na conta de armazenamento. Para exibir os dados em um relatório, abra **Cloud Explorer** ou **Gerenciador de Servidores**, abra o menu de atalho do nó para a função que lhe interessa e, em seguida, escolha **Exibir dados de diagnóstico**.
   
    ![Exibir dados de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Um relatório que mostra os dados disponíveis é exibido.
   
    ![Relatório do Microsoft Azure Diagnostics no Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Se os dados mais recentes não aparecem, você terá que esperar o período de transferência.
   
    Escolha o link **Atualizar** para atualizar os dados imediatamente ou escolha um intervalo na caixa de listagem suspensa **Atualização automática** para ter os dados atualizados automaticamente. Para exportar os dados de erro, escolha o botão **Exportar para CSV** para criar um arquivo de valores separados por vírgula que pode ser aberto em uma planilha.
   
    Em **Cloud Explorer** ou **Gerenciador de Servidores**, abra a conta de armazenamento que está associada à implantação.
3. Abra as tabelas de diagnóstico no Visualizador de tabela e, em seguida, analise os dados coletados. Para logs do IIS e os logs personalizados, você pode abrir um contêiner de blob. Examinando a tabela a seguir, você pode encontrar a tabela ou o contêiner de blob que contém os dados que lhe interessam. Além dos dados para esse arquivo de log, as entradas de tabela contêm EventTickCount, DeploymentId, função e RoleInstance para ajudá-lo a identificar qual máquina virtual e a função que geraram os dados e quando. 
   
   | Dados de diagnóstico | Descrição | Local |
   | --- | --- | --- |
   | Logs de aplicativos |Logs que seu código gera chamando os métodos da classe System.Diagnostics.Trace. |WADLogsTable |
   | Logs de eventos |Esses dados são dos logs de eventos do Windows nas máquinas virtuais. O Windows armazena informações desses logs, mas os aplicativos e os serviços também as usam para relatar erros ou informações de log. |WADWindowsEventLogsTable |
   | Contadores de desempenho |Você pode coletar dados de qualquer contador de desempenho disponível na máquina virtual. O sistema operacional fornece contadores de desempenho, que inclui estatísticas, como tempo de processador e uso de memória. |WADPerformanceCountersTable |
   | Logs de infraestrutura |Esses logs são gerados por meio da própria infraestrutura de diagnóstico. |WADDiagnosticInfrastructureLogsTable |
   | Logs IIS |Esses logs registram solicitações da Web. Se seu serviço de nuvem obtém uma quantidade significativa de tráfego, esses logs podem ser muito longos. Portanto, você deve coletar e armazenar esses dados somente quando forem necessários. |Você pode encontrar os logs de solicitação com falha no contêiner de blob em wad-iis-failedreqlogs em um caminho para essa implantação, função e instância. Você pode encontrar logs completos em wad-iis-logfiles. As entradas para cada arquivo são feitas na tabela WADDirectories. |
   | Despejos de falhas |Essas informações fornecem imagens binárias do processo do serviço de nuvem (normalmente uma função de trabalho). |contêiner de blob wad-crush-dumps |
   | Arquivos de log personalizados |Logs de dados que você predefiniu. |Você pode especificar no código o local dos arquivos de log personalizados na sua conta de armazenamento. Por exemplo, você pode especificar um contêiner de blob personalizado. |
4. Se os dados de qualquer tipo estão truncados, você pode tentar aumentar o buffer para este tipo de dados ou encurtar o intervalo entre as transferências de dados da máquina virtual para sua conta de armazenamento.
5. (opcional) Limpe os dados da conta de armazenamento, ocasionalmente, para reduzir os custos gerais de armazenamento.
6. Quando você faz uma implantação completa, o arquivo diagnostics.cscfg (.wadcfgx do SDK do Azure 2.5) é atualizado no Azure e seu serviço de nuvem seleciona as alterações para a configuração do diagnóstico. Se, em vez disso, você atualizar uma implantação existente, o arquivo .cscfg não será atualizado no Azure. Você ainda pode alterar as configurações de diagnóstico, porém, seguindo as etapas da próxima seção. Para obter mais informações sobre como executar uma implantação completa e atualizar uma implantação existente, consulte [Assistente de publicação de aplicativo no Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Para exibir dados de diagnóstico de máquina virtual
1. No menu de atalho para a máquina virtual, escolha **Exibir dados de diagnóstico**.
   
    ![Exibir dados de diagnóstico na máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    Isso abre a janela **Resumo de diagnóstico** .
   
    ![Resumo de diagnóstico de máquina virtual do Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Se os dados mais recentes não aparecem, você terá que esperar o período de transferência.
   
    Escolha o link **Atualizar** para atualizar os dados imediatamente ou escolha um intervalo na caixa de listagem suspensa **Atualização automática** para ter os dados atualizados automaticamente. Para exportar os dados de erro, escolha o botão **Exportar para CSV** para criar um arquivo de valores separados por vírgula que pode ser aberto em uma planilha.

## <a name="configure-cloud-service-diagnostics-after-deployment"></a>Configurar o diagnóstico de serviço de nuvem após a implantação
Se você estiver investigando um problema com um serviço de nuvem que já está em execução, talvez deseje coletar os dados que não especificou antes de implantar originalmente a função. Nesse caso, você pode começar a coletar dados usando as configurações no Gerenciador de Servidores. Você pode configurar o diagnóstico para uma única instância ou todas as instâncias de uma função, dependendo se você abrir a caixa de diálogo Configuração de diagnóstico no menu de atalho para a instância ou a função. Se você configurar o nó da função, todas as alterações se aplicam a todas as instâncias. Se você configurar o nó da instância, todas as alterações se aplicam a apenas àquela instância.

### <a name="to-configure-diagnostics-for-a-running-cloud-service"></a>Para configurar o diagnóstico para um serviço de nuvem em execução
1. No Gerenciador de Servidores, expanda o nó **Serviços de nuvem** e em seguida, expanda os nós para localizar a função ou instância que você deseja investigar ou ambas.
   
    ![Configurando o diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. No menu de atalho para um nó da instância ou um nó de função, escolha **Atualizar configurações de diagnóstico**, e, em seguida, escolha as configurações de diagnóstico que você deseja coletar.
   
    Para obter informações sobre as definições de configuração, consulte **Configurar fontes de dados de diagnóstico** neste tópico. Para obter informações sobre como exibir os dados de diagnóstico, consulte **Exibir os dados de diagnóstico** neste tópico.
   
    Se você alterar a coleta de dados no **Gerenciador de Servidores**, essas alterações permanecerão em vigor até que você reimplante totalmente o seu serviço de nuvem. Se você usar as configurações de publicação padrão, as alterações não são substituídas, uma vez que a configuração de publicação padrão é atualizar a implantação existente, em vez de fazer uma reimplantação completa. Para verificar se as configurações estão limpas no momento da implantação, vá para a guia **Configurações avançadas** no Assistente de publicação e limpe a caixa de seleção **Atualização de implantação** . Quando você reimplanta com essa caixa de seleção desmarcada, as configurações revertem aquelas no arquivo .wadcfgx (ou wadcfg) conforme definido no Editor de propriedades da função. Se você atualizar a implantação, o Azure mantém as configurações antigas.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Solucionar problemas do serviço de nuvem do Azure
Se você tiver problemas com seus projetos de serviço de nuvem, como uma função travar no status "ocupado", repetidamente recicla ou lança um erro interno do servidor, existem ferramentas e técnicas que você pode usar para diagnosticar e corrigir esses problemas. Para obter exemplos específicos de problemas e soluções comuns, bem como uma visão geral dos conceitos e ferramentas usadas para diagnosticar e corrigir esses erros, consulte [Dados de diagnóstico de computação de PaaS do Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Perguntas e respostas
**Qual é o tamanho do buffer e que tamanho o buffer deve ter?**

Em cada instância de máquina virtual, as cotas limitam quantos dados de diagnóstico podem ser armazenados no sistema de arquivos local. Além disso, você pode especificar um tamanho do buffer para cada tipo de dados de diagnóstico disponíveis. Esse tamanho do buffer age como uma cota individual para esse tipo de dados. Verificando a parte inferior da caixa de diálogo, você pode determinar a cota geral e a quantidade de memória que permanece. Se você especificar buffers maiores ou mais tipos de dados, abordará a cota geral. Você pode alterar a cota geral modificando o arquivo de configuração diagnostics.wadcfg/.wadcfgx. Os dados de diagnóstico são armazenados no mesmo sistema de arquivos como os dados do seu aplicativo. Portanto, se seu aplicativo usa muito espaço em disco, você não deve aumentar a cota de diagnóstico geral.

**Qual é o período de transferência e por qual tempo deve ser?**

O período de transferência é a quantidade de tempo que decorreu as capturas de dados. Depois de cada período de transferência, os dados são movidos do sistema de arquivos local de uma máquina virtual para tabelas em sua conta de armazenamento. Se a quantidade de dados que são coletados excede a cota antes do final de um período de transferência, dados antigos serão descartados. Você talvez queira reduzir o período de transferência se está perdendo dados porque seus dados excedem o tamanho do buffer ou a cota geral.

**Os carimbos de data/hora estão inseridos em que fuso horário?**

Os carimbos de data/hora estão no fuso horário local do data center que hospeda o serviço de nuvem. As seguintes três colunas de carimbo de data/hora nas tabelas de log são usadas.

* **PreciseTimeStamp** é o carimbo de data/hora de ETW do evento. Ou seja, a hora em que o evento é registrado no cliente.
* **TIMESTAMP** é o PreciseTimeStamp arredondado para baixo até o limite de frequência de upload. Portanto, se a frequência de upload é 5 minutos e a hora do evento 00:17:12, TIMESTAMP será 00:15:00.
* **Timestamp** é o carimbo de data/hora em que a entidade foi criada na tabela do Azure.

**Como gerenciar custos ao coletar informações de diagnóstico?**

As configurações padrão (**Nível de log** definido como **Erro** e **Período de transferência** definido como **1 minuto**) são projetadas para minimizar os custos. Os custos de computação aumentarão se você coleta mais dados de diagnóstico ou diminui o período de transferência. Não colete mais dados do que você precisa e não se esqueça de desabilitar a coleta de dados quando não precisa mais dela. Você sempre poderá habilitá-lo novamente, mesmo em tempo de execução, conforme mostrado na seção anterior.

**Como posso coletar logs de solicitação com falha do IIS?**

Por padrão, o IIS não coleta logs de solicitação com falha. Você pode configurar o IIS para coletá-los se editar o arquivo web.config para sua função web.

**Não estou obtendo informações de rastreamento de métodos RoleEntryPoint como OnStart. O que está errado?**

Os métodos de RoleEntryPoint são chamados no contexto de WAIISHost.exe, não o IIS. Portanto, as informações de configuração no web.config que normalmente habilitam o rastreamento não se aplicam. Para resolver esse problema, adicione um arquivo .config ao seu projeto de função web e nomeie o arquivo para corresponder ao assembly de saída que contém o código RoleEntryPoint. No projeto de função web padrão, o nome do arquivo .config seria WAIISHost.exe.config. Em seguida, adicione as seguintes linhas ao arquivo:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

Agora, na janela **Propriedades**, defina a propriedade **Copiar para Diretório de saída** como **Copiar sempre**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o registro em log de diagnósticos no Azure, consulte [Habilitando o diagnóstico nos Serviços de Nuvem do Azure e Máquinas Virtuais](cloud-services/cloud-services-dotnet-diagnostics.md) e [Habilitar o registro em log de diagnóstico para aplicativos Web no Serviço de Aplicativo do Azure](app-service/web-sites-enable-diagnostic-log.md).

