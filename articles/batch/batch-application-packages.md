---
title: Instalar pacotes de aplicativos em nós de computação - Azure Batch | Microsoft Docs
description: Use o recurso de pacotes de aplicativos do Lote do Azure para gerenciar facilmente vários aplicativos e versões para instalação nos nós de computação do Lote.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/05/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee54d37050991763e60a6feb96c75d80384a42ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60722003"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote

O recurso de pacotes de aplicativos do Lote do Azure fornece um fácil gerenciamento dos aplicativos de tarefa e sua implantação para os nós de computação em seu pool. Com os pacotes de aplicativos, você pode carregar e gerenciar diversas versões dos aplicativos que suas tarefas executam, incluindo seus arquivos de suporte. Você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

Neste artigo, você aprenderá como carregar e gerenciar os pacotes de aplicativos usando o Portal do Azure. Em seguida, aprenderá como instalá-los nos nós de computação de um pool com a biblioteca [.NET do Lote][api_net].

> [!NOTE]
> Os pacotes de aplicativos têm suporte em todos os pools do Lote criados após 5 de julho de 2017. Elas só terão suporte em pools do Lote criados entre 10 de março de 2016 e 5 de julho de 2017 se o pool tiver sido criado usando uma configuração de Serviço de Nuvem. Os pools do Lote criados antes de 10 de março de 2016 não dão suporte a pacotes de aplicativos.
>
> As APIs para criar e gerenciar pacotes de aplicativos fazem parte da biblioteca [[Batch Management .NET]][api_net_mgmt]. As APIs para instalar pacotes de aplicativos em um nó de computação fazem parte da biblioteca [Batch .NET][api_net]. Os recursos comparáveis estão nas APIs do lote disponível para outros idiomas. 
>
> O recurso de pacotes de aplicativos descrito aqui substitui o recurso Aplicativos do Lote, disponível nas versões anteriores do serviço.

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicativos
Para usar pacotes de aplicativos, você deve [vincular uma conta de Armazenamento do Azure](#link-a-storage-account) à sua conta do Lote.

## <a name="about-applications-and-application-packages"></a>Sobre aplicativos e pacotes de aplicativos
No Lote do Azure, um *aplicativo* refere-se a um conjunto de binários com versão que podem ser baixados automaticamente para os nós de computação no pool. Um *pacote de aplicativos* refere-se a um *conjunto específico* desses binários e representa uma determinada *versão* do aplicativo.

![Diagrama de alto nível de aplicativos e pacotes de aplicativos][1]

### <a name="applications"></a>Aplicativos
Um aplicativo no Lote contém um ou mais pacotes de aplicativos e especifica as opções de configuração para o aplicativo. Por exemplo, um aplicativo pode especificar a versão do pacote de aplicativos padrão para instalar nos nós de computação e se seus pacotes podem ser atualizados ou excluídos.

### <a name="application-packages"></a>pacotes de aplicativos
Um pacote de aplicativos é um arquivo .zip contendo os binários de aplicativo e arquivos de suporte exigidos para que suas tarefas executem o aplicativo. Cada pacote de aplicativos representa uma versão específica do aplicativo.

Você pode especificar os pacotes de aplicativos no nível do pool e no de tarefa. Você pode especificar um ou mais desses pacotes e (opcionalmente) uma versão quando você cria uma tarefa ou um pool.

* **Pacotes de aplicativos do pool** são implantados em *cada* nó no pool. Os aplicativos são implantados quando um nó ingressa em um pool e quando ele é reinicializado ou tem a imagem recriada.
  
    Os pacotes de aplicativos do pool são adequados quando todos os nós em um pool executam as tarefas de um trabalho. Você pode especificar um ou mais pacotes de aplicativos quando cria um pool e pode adicionar ou atualizar os pacotes de um pool existente. Se você atualizar os pacotes de aplicativos de um pool existente, deverá reiniciar os nós para instalar o novo pacote.
* **pacotes de aplicativos de tarefa** são implantados somente em um nó de computação programado para executar uma tarefa, logo antes de executar a linha de comando da tarefa. Se o pacote de aplicativos especificado e a versão já estiverem no nó, ele não será reimplantado e o pacote existente será usado.
  
    Os pacotes de aplicativos de tarefa são úteis nos ambientes de pool compartilhado, onde diferentes trabalhos são executados em um pool, e o pool não é excluído quando um trabalho é concluído. Se o trabalho tiver menos tarefas do que os nós no pool, pacotes de aplicativos de tarefa poderão minimizar a transferência de dados, pois o aplicativo é implantado apenas para os nós que executam tarefas.
  
    Outros cenários que podem aproveitar os pacotes de aplicativos de tarefa são os trabalhos que executam um aplicativo grande, mas para um pequeno número de tarefas. Por exemplo, uma fase de pré-processamento ou uma tarefa de mesclagem na qual o aplicativo pré-processamento ou mesclagem é pesado pode beneficiar-se do uso de pacotes de aplicativos de tarefa.

> [!IMPORTANT]
> Há restrições ao número de aplicativos e pacotes de aplicativos em uma conta do Lote, bem como ao tamanho máximo do pacote de aplicativos. Consulte [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter detalhes sobre esses limites.
> 
> 

### <a name="benefits-of-application-packages"></a>Benefícios dos pacotes de aplicativos
Os pacotes de aplicativos podem simplificar o código em sua solução de Lote e reduzir a sobrecarga exigida para gerenciar os aplicativos que suas tarefas executam.

Com pacotes de aplicativos, a tarefa de inicialização do pool não precisa especificar uma longa lista de arquivos de recursos individuais para instalar nos nós. Não é preciso gerenciar manualmente diversas versões dos arquivos do aplicativo no Armazenamento do Azure nem em seus nós. E você não precisa preocupar-se com a geração de [URLs SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para fornecer acesso aos arquivos em sua conta de Armazenamento. O Lote funciona em segundo plano com o Armazenamento do Azure para armazenar os pacotes de aplicativos e implantá-los nos nós de computação.

> [!NOTE] 
> O tamanho total de uma tarefa de início deve ser menor ou igual a 32768 caracteres, incluindo arquivos de recurso e variáveis de ambiente. Se a tarefa de inicialização exceder esse limite, usar pacotes de aplicativos é outra opção. Você pode também criar um arquivo compactado contendo os arquivos de recurso, carregá-lo como um blob no armazenamento do Azure e descompactá-lo na linha de comando da tarefa inicial. 
>
>

## <a name="upload-and-manage-applications"></a>Carregar e gerenciar aplicativos
Você pode usar o [Portal do Azure][portal] ou as APIs de Gerenciamento de Lote para gerenciar os pacotes de aplicativos em sua conta do Lote. Nas próximas seções, primeiro vincularemos uma conta de Armazenamento e analisaremos como adicionar aplicativos e pacotes e como gerenciá-los com o portal.

### <a name="link-a-storage-account"></a>Vincular uma conta de armazenamento
Para usar pacotes de aplicativo, primeiro vincule uma [conta do Azure Storage](batch-api-basics.md#azure-storage-account) a sua conta de Lote. Se você ainda não configurou uma conta de Armazenamento, o Portal do Azure exibe um aviso na primeira vez em que clicar em **Aplicativos** na Conta do Lote.



![Aviso de 'Nenhuma conta de armazenamento configurada' no portal do Azure][9]

O serviço de Lote usa a conta de Armazenamento associada para armazenar os pacotes de aplicativos. Depois que você tiver vinculado as duas contas, o Lote poderá implantar automaticamente os pacotes armazenados na conta de armazenamento vinculada nos nós de computação. Para vincular uma Conta de armazenamento à sua conta do Lote, clique em **Conta de armazenamento** na janela **Aviso** e clique em **Conta de Armazenamento** novamente.

![Folha Escolher conta de armazenamento no portal do Azure][10]

Recomendamos que você crie uma conta de armazenamento para usar *especificamente* com sua conta do Lote e que a selecione aqui. Depois de ter criado uma conta de Armazenamento, você poderá vinculá-la à sua conta do Lote usando a janela **Conta de Armazenamento**.

> [!NOTE] 
> Atualmente você não pode usar pacotes de aplicativos com uma conta de Armazenamento do Microsoft Azure que está configurada com [as regras de firewall](../storage/common/storage-network-security.md).
> 

O serviço de Lote usa o Armazenamento do Azure para armazenar os pacotes de aplicativos como blobs de blocos. Você é [cobrado normalmente][storage_pricing] pelos dados de blob de blocos e o tamanho de cada pacote não pode exceder o [tamanho máximo do blob de blocos](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets). Não se esqueça de considerar o tamanho e o número de pacotes de aplicativos e, periodicamente, remova pacotes preteridos para minimizar o custo.
> 
> 

### <a name="view-current-applications"></a>Exibir aplicativos atuais
Para exibir os aplicativos em sua conta do Lote, clique no item de menu **Aplicativos** no menu à esquerda enquanto exibe sua **Conta do Lote**.

![Bloco Aplicativos][2]

Selecionar essa opção de menu abre a janela **Aplicativos**:

![Listar aplicativos][3]

Essa janela exibe a ID de cada aplicativo em sua conta e as seguintes propriedades:

* **Pacotes**: O número de versões associadas a este aplicativo.
* **Versão padrão**: A versão do aplicativo que será instalada se você não indicar uma versão ao especificar o aplicativo para um pool. Essa configuração é opcional.
* **Permitir atualizações**: O valor que especifica se são permitidas as atualizações, exclusões e adições do pacote. Se isso estiver definido para **Não**, as exclusões e atualizações do pacote ficarão desabilitadas para o aplicativo. Apenas novas versões do pacote de aplicativos poderão ser adicionadas. O padrão é **Sim**.

Se você quiser ver a estrutura de arquivo do pacote de aplicativo em seu nó de computação, navegue até sua conta do lote no portal. Em sua conta do lote, navegue até **Pools**. Selecione o pool que contém os nós de computação que você está interessado.

![Nós no pool][13]

Depois de selecionar o pool, navegue até que o pacote de aplicativo está instalado no nó de computação. A partir daí, os detalhes do pacote de aplicativo estão localizados na **aplicativos** pasta. Pastas adicionais no nó de computação contêm outros arquivos, como tarefas iniciais, arquivos de saída, saída de erro, etc.

![Arquivos no nó][14]

### <a name="view-application-details"></a>Exibir detalhes do aplicativo
Para ver os detalhes de um aplicativo, selecione o aplicativo na janela **Aplicativos**.

![Detalhes do aplicativo][4]

Nos detalhes do aplicativo, você pode configurar as definições a seguir para o aplicativo.

* **Permitir atualizações**: Especifique se seus pacotes de aplicativos podem ser atualizados ou excluídos. Consulte "Atualizar ou excluir um pacote de aplicativos" mais adiante neste artigo.
* **Versão padrão**: Especifique um pacote de aplicativos padrão para implantar nos nós de computação.
* **Nome de exibição**: Especifique um nome amigável que sua solução de Lote pode usar ao exibir informações sobre o aplicativo, como na interface do usuário de um serviço que você fornece aos clientes por meio do Lote.

### <a name="add-a-new-application"></a>Adicionar um novo aplicativo
Para criar um novo aplicativo, adicione um pacote de aplicativos e especifique uma ID de aplicativo nova e exclusiva. O primeiro pacote de aplicativos que você adiciona com a nova ID de aplicativo também cria o novo aplicativo.

Clique em **Aplicativos** > **Adicionar**.

![Folha Novo aplicativo no portal do Azure][5]

A janela **Novo aplicativo** fornece os campos a seguir para especificar as configurações do seu novo aplicativo e do pacote de aplicativos.

**ID do aplicativo**

Este campo especifica a ID do novo aplicativo, que está sujeita às regras de validação padrão de ID do Lote do Azure. As regras para fornecer uma ID de aplicativo são conforme descrito a seguir:

* Em nós do Windows, a ID pode conter qualquer combinação de caracteres alfanuméricos, hifens e sublinhados. Em nós do Linux, são permitidos somente caracteres alfanuméricos e sublinhados.
* Não pode conter mais de 64 caracteres.
* Deve ser exclusiva na conta do Lote.
* Não diferencia maiúsculas de minúsculas e preserva maiúsculas e minúsculas.

**Versão**

Este campo especifica a versão do pacote de aplicativos que você está carregando. As cadeias de caracteres da versão estão sujeitas às seguintes regras de validação:

* Em nós do Windows, a cadeia de caracteres de versão pode conter qualquer combinação de caracteres alfanuméricos, hifens, sublinhados e pontos. Em nós do Linux, a cadeia de caracteres de versão pode conter somente caracteres alfanuméricos e sublinhados.
* Não pode conter mais de 64 caracteres.
* Devem ser exclusivas no aplicativo.
* Não diferenciam maiúsculas de minúsculas e preservam maiúsculas e minúsculas.

**Pacote de aplicativos**

Esse campo especifica o arquivo .zip que contém os binários do aplicativo e os arquivos de suporte que necessários à execução do aplicativo. Clique na caixa **Selecionar um arquivo** ou no ícone de pasta para procurar e selecionar um arquivo .zip que contém os arquivos do seu aplicativo.

Depois de ter selecionado um arquivo, clique em **OK** para começar a carregar no Armazenamento do Azure. Quando a operação de upload for concluída, o portal exibirá uma notificação. Dependendo do tamanho do arquivo que você estiver carregando e da velocidade da conexão de rede, essa operação pode demorar um pouco.

> [!WARNING]
> Não feche a janela **Novo aplicativo** antes de a operação de carregamento estar concluída. Isso interromperá o processo de carregamento.
> 
> 

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicativos
Para adicionar uma nova versão do pacote de aplicativos a um aplicativo existente, selecione um aplicativo na janela **Aplicativos** e clique em **Adicionar** > **Pacotes**.

![Folha Adicionar pacote de aplicativos no portal do Azure][8]

Como você pode ver, os campos correspondem aos da janela **Novo aplicativo**, mas a caixa **ID do Aplicativo** fica desabilitada. Assim como para o novo aplicativo, especifique a **Versão** do novo pacote, procure o arquivo .zip do **Pacote de aplicativos** e clique em **OK** para carregar o pacote.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou excluir um pacote de aplicativos
Para atualizar ou excluir um pacote de aplicativos existente, abra os detalhes do aplicativo, clique em **Pacotes** clique nas **reticências** na linha do pacote de aplicativos que você deseja modificar e selecione a ação que deseja executar.

![Atualizar ou excluir pacote no portal do Azure][7]

**Atualização**

Quando você clica em **Atualizar**, a janela **Atualizar pacote** é exibida. Essa janela é semelhante à folha **Novo pacote de aplicativos**. No entanto, somente o campo de seleção de pacotes está habilitado, permitindo que você especifique um novo arquivo ZIP a carregar.

![Folha do pacote de atualização no portal do Azure][11]

**Excluir**

Quando você clica em **Excluir**, é preciso confirmar a exclusão da versão do pacote e o Lote exclui o pacote do Armazenamento do Azure. Se você excluir a versão padrão de um aplicativo, a configuração da **Versão padrão** será removida para o aplicativo.

![Excluir aplicativo ][12]

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicativos em nós de computação
Agora que você aprendeu como gerenciar os pacotes de aplicativos com o portal do Azure, podemos analisar como implantá-los para os nós de computação e executá-los com tarefas em Lote.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicativos do pool
Para instalar um pacote de aplicativos em todos os nós de computação em um pool, especifique uma ou mais *referências* do pacote de aplicativos para o pool. Os pacotes de aplicativo que você especifica para um pool são instalados em cada nó de computação quando esse nó se une ao pool, e quando o nó é reinicializado ou tem sua imagem refeita.

No .NET do Lote, especifique um ou mais [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] quando você criar um novo pool ou para um pool existente. A classe [ApplicationPackageReference][net_pkgref] especifica uma ID e versão do aplicativo para instalar nos nós de computação de um pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Se uma implantação do pacote de aplicativos falhar por algum motivo, o serviço do Lote marcará o nó como [inutilizável][net_nodestate] e nenhuma tarefa será agendada para a execução nesse nó. Nesse caso, você deve **reiniciar** o nó para reiniciar a implantação do pacote. A reinicialização do nó também habilita novamente nele o agendamento de tarefas.
> 
> 

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicativos de tarefa
Semelhante a um pool, você especifica as *referências* do pacote de aplicativos para uma tarefa. Quando uma tarefa está agendada para ser executada em um nó, o pacote é baixado e extraído um pouco antes da linha de comando da tarefa ser executada. Se o pacote especificado e a versão já estiverem instalados no nó, o pacote não será baixado e o pacote existente será usado.

Para instalar um pacote de aplicativos de tarefa, configure a propriedade [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] da tarefa:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Executar os aplicativos instalados
Os pacotes que você especificou para uma tarefa ou um pool são baixados e extraídos para um diretório nomeado dentro do `AZ_BATCH_ROOT_DIR` do nó. O Lote também cria uma variável de ambiente que contém o caminho para o diretório nomeado. As linhas de comando da tarefa usam essa variável de ambiente ao referenciar o aplicativo no nó. 

Em nós do Windows, a variável está no seguinte formato:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Em nós do Linux, o formato é ligeiramente diferente. Pontos (.), hifens (-) e teclas jogo da velha (#) são transformados em sublinhados na variável de ambiente. Além disso, observe que o caso da ID do aplicativo é preservado. Por exemplo: 

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` e `version` são os valores que correspondem à versão do aplicativo e do pacote que você especificou para a implantação. Por exemplo, se você especificou que a versão 2.7 do *blender* de aplicativos deve ser instalada em nós do Windows, as linhas de comando da tarefa usarão essa variável de ambiente para acessar seus arquivos:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Em nós do Linux, especifique a variável de ambiente neste formato. Mescle os pontos (.), hifens (-) e a tecla jogo da velha (#) como sublinhados e preserve a capitalização da ID do aplicativo:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Quando você carrega um pacote de aplicativos, você pode especificar uma versão padrão para implantar em seus nós de computação. Se você tiver especificado uma versão padrão para um aplicativo, poderá omitir o sufixo da versão ao referenciar o aplicativo. Você pode especificar a versão padrão do aplicativo no portal do Azure, na janela de **Aplicativos**, como mostrado em [Carregue e gerencie aplicativos](#upload-and-manage-applications).

Por exemplo, se você definiu "2.7" como a versão padrão do *blender* de aplicativos e suas tarefas referenciam a variável de ambiente a seguir, seus nós do Windows executarão a versão 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

O snippet de código a seguir mostra uma linha de comando da tarefa de exemplo que inicializa a versão padrão do *blender* de aplicativos:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Consulte [Configurações do ambiente para tarefas](batch-api-basics.md#environment-settings-for-tasks) na [Visão geral do recurso de Lote](batch-api-basics.md) para saber mais sobre as configurações do ambiente do nó de computação.
> 
> 

## <a name="update-a-pools-application-packages"></a>Atualizar pacotes de aplicativos de um pool
Se um pool existente já tiver sido configurado com um pacote de aplicativos, você poderá especificar um novo pacote para o pool. Se você especificar uma nova referência de pacote para um pool, o seguinte se aplicará:

* O serviço de Lote instala o pacote recém-especificado em todos os novos nós que ingressam no pool e em qualquer nó existente que seja reinicializado ou cuja imagem seja refeita.
* Os nós de computação que já estão no pool quando você atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicativos. Esses nós de computação devem ser reinicializados ou ter sua imagem recriada para receber o novo pacote.
* Quando um novo pacote é implantado, as variáveis de ambiente criadas refletem as novas referências do pacote de aplicativos.

Neste exemplo, o pool existente tem a versão 2.7 do aplicativo *blender* configurada como um de seus [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Para atualizar os nós do pool com a versão 2.76b, especifique um novo [ApplicationPackageReference][net_pkgref] com a nova versão e confirme a mudança.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Agora que a nova versão foi configurada, o serviço de Lote instala a versão 2.76b em qualquer nó *novo* que ingresse no pool. Para instalar a versão 2.76b nos nós que já *estão* no pool, reinicialize-os ou refaça a imagem deles. Observe que os nós reinicializados retém os arquivos das implantações anteriores do pacote.

## <a name="list-the-applications-in-a-batch-account"></a>Listar os aplicativos em uma conta do Lote
Você pode listar os aplicativos e seus pacotes em uma conta do Lote usando o método [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries].

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Conclusão
Com os pacotes de aplicativos, você pode fornecer ajudar seus clientes a escolher os aplicativos para seus trabalhos e especificar a versão exata a ser usada ao processar trabalhos com o serviço habilitado para o Lote. Você também pode fornecer aos clientes a capacidade de carregar e rastrear os próprios aplicativos no serviço.

## <a name="next-steps"></a>Próximas etapas
* A [API REST do Lote][api_rest] também dá suporte ao trabalho com pacotes de aplicativos. Por exemplo, consulte o elemento [applicationPackageReferences][rest_add_pool_with_packages] em [Adicionar um pool a uma conta][rest_add_pool] para obter informações sobre como especificar os pacotes a instalar usando a API REST. Consulte [Aplicativos][rest_applications] para obter detalhes sobre como obter informações do aplicativo usando a API REST do Lote.
* Aprenda a [gerenciar de modo programático as contas e as cotas do Lote do Azure com o .NET de Gerenciamento do Lote](batch-management-dotnet.md). A biblioteca [.NET de Gerenciamento do Lote][api_net_mgmt] pode permitir os recursos de criação e exclusão de conta para seu aplicativo ou serviço do Lote.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicativos"
[2]: ./media/batch-application-packages/app_pkg_02.png "Bloco Aplicativos no portal do Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Folha Aplicativos no portal do Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Folha Detalhes do aplicativo no portal do Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Folha Novo aplicativo no portal do Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Menu suspenso Atualizar ou excluir pacotes no portal do Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Folha Novo pacote de aplicativos no portal do Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Alerta Nenhuma conta de armazenamento vinculada"
[10]: ./media/batch-application-packages/app_pkg_10.png "Folha Escolher conta de armazenamento no portal do Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Folha do pacote de atualização no portal do Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Caixa de diálogo de confirmação Excluir pacote no portal do Azure"
[13]: ./media/batch-application-packages/package-file-structure.png "No portal do Azure de informações do nó de computação"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Arquivos no nó de computação exibido no portal do Azure"