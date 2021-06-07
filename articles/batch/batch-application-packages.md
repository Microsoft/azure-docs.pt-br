---
title: Implantar pacotes de aplicativos em nós de computação
description: Use o recurso de pacotes de aplicativos do Lote do Azure para gerenciar facilmente vários aplicativos e versões para instalação nos nós de computação do Lote.
ms.topic: how-to
ms.date: 03/24/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: d0ed5cc47694d6576e6aea46b62dfab8ecb73459
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045783"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Implantar aplicativos em nós de computação com pacotes de aplicativos do Lote

Os pacotes de aplicativos podem simplificar o código em sua solução de lote do Azure e facilitar o gerenciamento dos aplicativos que suas tarefas executam. Com os pacotes de aplicativos, você pode carregar e gerenciar várias versões de aplicativos que suas tarefas executam, incluindo seus arquivos de suporte. Você pode implantar automaticamente um ou mais desses aplicativos nos nós de computação em seu pool.

As APIs para criar e gerenciar pacotes de aplicativos fazem parte da biblioteca [.NET do Gerenciamento do Lote](/dotnet/api/overview/azure/batch/management). As APIs para instalar pacotes de aplicativos em um nó de computação fazem parte da biblioteca [.NET do Lote](/dotnet/api/overview/azure/batch/client). Os recursos comparáveis estão nas APIs do lote disponível para outros idiomas.

Este artigo explica como carregar e gerenciar pacotes de aplicativos no portal do Azure. Ele também mostra como instalá-los em nós de computação de um pool com a biblioteca [.net do lote](/dotnet/api/overview/azure/batch/client) .

## <a name="application-package-requirements"></a>Requisitos do pacote de aplicativos

Para usar pacotes de aplicativos, você deve [vincular uma conta de Armazenamento do Azure](#link-a-storage-account) à sua conta do Lote.

Há restrições ao número de aplicativos e pacotes de aplicativos em uma conta do Lote, bem como ao tamanho máximo do pacote de aplicativos. Para obter mais informações, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md).

> [!NOTE]
> Os pools do lote criados antes de 5 de julho de 2017 não dão suporte a pacotes de aplicativos (a menos que eles tenham sido criados após 10 de março de 2016 usando a configuração de serviços de nuvem). O recurso de pacotes de aplicativos descrito aqui substitui o recurso Aplicativos do Lote, disponível nas versões anteriores do serviço.

## <a name="understand-applications-and-application-packages"></a>Entender aplicativos e pacotes de aplicativos

No Lote do Azure, um *aplicativo* refere-se a um conjunto de binários com versão que podem ser baixados automaticamente para os nós de computação no pool. Um aplicativo contém um ou mais *pacotes de aplicativos*, que representam versões diferentes do aplicativo.

Cada *pacote de aplicativo* é um arquivo. zip que contém os binários do aplicativo e os arquivos de suporte. Há suporte apenas para o formato. zip.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagrama mostrando uma exibição de alto nível de aplicativos e pacotes de aplicativos.":::

Você pode especificar pacotes de aplicativos no nível do pool ou da tarefa.

- **Pacotes de aplicativos do pool** são implantados em cada nó no pool. Os aplicativos são implantados quando um nó ingressa em um pool e quando ele é reinicializado ou tem a imagem recriada.
  
    Os pacotes de aplicativos de pool são apropriados quando todos os nós em um pool executam as tarefas de um trabalho. Você pode especificar um ou mais pacotes de aplicativos a serem implantados ao criar um pool. Você também pode adicionar ou atualizar os pacotes de um pool existente. Para instalar um novo pacote em um pool existente, você deve reiniciar seus nós.

- **pacotes de aplicativos de tarefa** são implantados somente em um nó de computação programado para executar uma tarefa, logo antes de executar a linha de comando da tarefa. Se o pacote de aplicativos especificado e a versão já estiverem no nó, ele não será reimplantado e o pacote existente será usado.
  
    Os pacotes de aplicativos de tarefas são úteis em ambientes de pool compartilhado, em que diferentes trabalhos são executados em um pool e o pool não é excluído quando um trabalho é concluído. Se seu trabalho tiver menos tarefas do que nós no pool, os pacotes de aplicativos de tarefa poderão minimizar a transferência de dados, já que seu aplicativo é implantado somente para os nós que executam tarefas.
  
    Outros cenários que podem aproveitar os pacotes de aplicativos de tarefa são os trabalhos que executam um aplicativo grande, mas para um pequeno número de tarefas. Por exemplo, os aplicativos de tarefas podem ser úteis para um estágio de pré-processamento pesado ou uma tarefa de mesclagem.

Com pacotes de aplicativos, a tarefa de inicialização do pool não precisa especificar uma longa lista de arquivos de recursos individuais para instalar nos nós. Não é preciso gerenciar manualmente diversas versões dos arquivos do aplicativo no Armazenamento do Azure nem em seus nós. E você não precisa se preocupar em gerar [URLs SAS](../storage/common/storage-sas-overview.md) para fornecer acesso aos arquivos em sua conta de armazenamento. O Lote funciona em segundo plano com o Armazenamento do Azure para armazenar os pacotes de aplicativos e implantá-los nos nós de computação.

> [!NOTE]
> O tamanho total de uma tarefa de início deve ser menor ou igual a 32768 caracteres, incluindo arquivos de recurso e variáveis de ambiente. Se a tarefa inicial exceder esse limite, o uso de pacotes de aplicativos será outra opção. Você também pode criar um arquivo. zip contendo os arquivos de recurso, carregá-lo como um blob no armazenamento do Azure e descompactá-lo da linha de comando da tarefa inicial.

## <a name="upload-and-manage-applications"></a>Carregar e gerenciar aplicativos

Você pode usar o [portal do Azure](https://portal.azure.com) ou as APIs de Gerenciamento do Lote para gerenciar os pacotes de aplicativos em sua conta do Lote. As seções a seguir explicam como vincular uma conta de armazenamento e como adicionar e gerenciar aplicativos e pacotes de aplicativos no portal do Azure.

> [!NOTE]
> Embora você possa definir valores de aplicativo no recurso [Microsoft.Batch/batchAccounts](/templates/microsoft.batch/batchaccounts) de um [modelo do ARM](quick-create-template.md), atualmente não é possível usar um modelo ARM para carregar pacotes de aplicativos para usar em sua conta do lote. Você deve carregá-los em sua conta de armazenamento vinculada, conforme descrito [abaixo](#add-a-new-application).

### <a name="link-a-storage-account"></a>Vincular uma conta de armazenamento

Para usar pacotes de aplicativos, você deve vincular uma [conta de armazenamento do Azure](accounts.md#azure-storage-accounts) à sua conta do lote. O serviço de lote usará a conta de armazenamento associada para armazenar seus pacotes de aplicativos. Recomendamos que você crie uma conta de armazenamento especificamente para uso com sua conta do lote.

Se você ainda não tiver configurado uma conta de armazenamento, o portal do Azure exibirá um aviso na primeira vez que você selecionar **aplicativos** em sua conta do lote. Para vincular uma conta de armazenamento à sua conta do lote, selecione **conta de armazenamento** na janela de **aviso** e, em seguida, selecione **conta de armazenamento** novamente.

Depois que você tiver vinculado as duas contas, o Lote poderá implantar automaticamente os pacotes armazenados na conta de armazenamento vinculada nos nós de computação.

> [!IMPORTANT]
> Você não pode usar pacotes de aplicativos com contas de armazenamento do Azure configuradas com [regras de firewall](../storage/common/storage-network-security.md)ou com o **namespace hierárquico** definido como **habilitado**.

O serviço de Lote usa o Armazenamento do Azure para armazenar os pacotes de aplicativos como blobs de blocos. Você é [cobrado normalmente](https://azure.microsoft.com/pricing/details/storage/) pelos dados de blob de blocos e o tamanho de cada pacote não pode exceder o tamanho máximo do blob de blocos. Para obter mais informações, confira [Escalabilidade e metas de desempenho do Armazenamento do Azure para contas de armazenamento](../storage/blobs/scalability-targets.md). Para minimizar os custos, certifique-se de considerar o tamanho e o número de seus pacotes de aplicativos e remover periodicamente os pacotes preteridos.

### <a name="view-current-applications"></a>Exibir aplicativos atuais

Para exibir os aplicativos em sua conta do lote, selecione **aplicativos** no menu de navegação à esquerda.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Captura de tela do item de menu aplicativos no portal do Azure.":::

A seleção dessa opção de menu abre a janela **aplicativos** . Essa janela exibe a ID de cada aplicativo em sua conta e as seguintes propriedades:

- **Pacotes**: O número de versões associadas a este aplicativo.
- **Versão padrão**: se aplicável, a versão do aplicativo que será instalada se nenhuma versão for especificada ao implantar o aplicativo.
- **Permitir atualizações**: especifica se as atualizações e exclusões do pacote são permitidas.

Para ver a [estrutura de arquivos](files-and-directories.md) do pacote de aplicativos em um nó de computação, navegue até sua conta do lote no portal do Azure. Selecione **pools**. em seguida, selecione o pool que contém o nó de computação. Selecione o nó de computação no qual o pacote de aplicativos está instalado e abra a pasta **aplicativos** .

### <a name="view-application-details"></a>Exibir detalhes do aplicativo

Para ver os detalhes de um aplicativo, selecione-o na janela **aplicativos** . Você pode definir as configurações a seguir para seu aplicativo.

- **Permitir atualizações**: indica se os pacotes de aplicativos podem ser [atualizados ou excluídos](#update-or-delete-an-application-package). O padrão é **Sim**. Se definido como **não**, os pacotes de aplicativos existentes não poderão ser atualizados ou excluídos, mas novas versões de pacote de aplicativos ainda poderão ser adicionadas.
- **Versão padrão**: o pacote de aplicativo padrão a ser usado quando o aplicativo for implantado, se nenhuma versão for especificada.
- **Nome de exibição**: um nome amigável que sua solução de lote pode usar ao exibir informações sobre o aplicativo. Por exemplo, esse nome pode ser usado na interface do usuário de um serviço que você fornece para seus clientes por meio do lote.

### <a name="add-a-new-application"></a>Adicionar um novo aplicativo

Para criar um novo aplicativo, você adiciona um pacote de aplicativos e especifica uma ID de aplicativo exclusiva.

Em sua conta do lote, selecione **aplicativos** e, em seguida, selecione **Adicionar**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Captura de tela do novo processo de criação de aplicativos no portal do Azure.":::

Insira as seguintes informações:

- **ID do aplicativo**: a ID do seu novo aplicativo.
- **Version**": a versão do pacote de aplicativos que você está carregando.
- **Pacote de aplicativos**: o arquivo. zip que contém os binários do aplicativo e os arquivos de suporte necessários para executar o aplicativo.

A **ID do aplicativo** e a **versão** que você inserir devem seguir estes requisitos:

- Em nós do Windows, a ID pode conter qualquer combinação de caracteres alfanuméricos, hifens e sublinhados. Em nós do Linux, são permitidos somente caracteres alfanuméricos e sublinhados.
- Não pode conter mais de 64 caracteres.
- Deve ser exclusiva na conta do Lote.
- As IDs fazem preservação de maiúsculas e minúsculas e não diferenciam maiúsculas.

Quando estiver tudo pronto, selecione **Fechar**. Depois que o arquivo. zip for carregado em sua conta de armazenamento do Azure, o portal exibirá uma notificação. Dependendo do tamanho do arquivo que você está carregando e da velocidade da conexão de rede, isso pode levar algum tempo.

### <a name="add-a-new-application-package"></a>Adicionar um novo pacote de aplicativos

Para adicionar uma versão do pacote de aplicativos a um aplicativo existente, selecione o aplicativo na seção **aplicativos** da sua conta do lote e, em seguida, selecione **Adicionar**.

Como você fez para o novo aplicativo, especifique a **versão** do novo pacote, carregue o arquivo. zip no campo **pacote de aplicativos** e, em seguida, selecione **Enviar**.

### <a name="update-or-delete-an-application-package"></a>Atualizar ou excluir um pacote de aplicativos

Para atualizar ou excluir um pacote de aplicativos existente, selecione o aplicativo na seção **aplicativos** da sua conta do lote. Selecione as reticências na linha do pacote de aplicativos que você deseja modificar e, em seguida, selecione a ação que deseja executar.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Captura de tela mostrando as opções de atualização e exclusão para pacotes de aplicativos no portal do Azure.":::

Se você selecionar **Atualizar**, poderá carregar um novo arquivo. zip. Isso substituirá o arquivo. zip anterior que você carregou para essa versão.

Se você selecionar **excluir**, será solicitado que você confirme a exclusão dessa versão. Depois de selecionar **OK**, o lote excluirá o arquivo. zip da sua conta de armazenamento do Azure. Se você excluir a versão padrão de um aplicativo, a configuração de **versão padrão** será removida para esse aplicativo.

## <a name="install-applications-on-compute-nodes"></a>Instalar aplicativos em nós de computação

Agora que você aprendeu como gerenciar pacotes de aplicativos no portal do Azure, podemos discutir como implantá-los em nós de computação e executá-los com tarefas em lote.

### <a name="install-pool-application-packages"></a>Instalar pacotes de aplicativos do pool

Para instalar um pacote de aplicativos em todos os nós de computação em um pool, especifique uma ou mais referências do pacote de aplicativos para o pool. Os pacotes de aplicativos que você especificar para um pool são instalados em cada nó de computação que une o pool e em qualquer nó reinicializado ou com a imagem recriada.

No .NET do lote, especifique um ou mais [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) ao criar um novo pool ou para um pool existente. A classe [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) especifica uma ID e versão do aplicativo para instalar nos nós de computação de um pool.

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
> Se uma implantação de pacote de aplicativos falhar, o serviço de lote marcará o nó como [inutilizável](/dotnet/api/microsoft.azure.batch.computenode.state)e nenhuma tarefa será agendada para execução nesse nó. Se isso acontecer, reinicie o nó para reinicializar a implantação do pacote. A reinicialização do nó também habilita novamente nele o agendamento de tarefas.

### <a name="install-task-application-packages"></a>Instalar pacotes de aplicativos de tarefa

Semelhante a um pool, você especifica as referências do pacote de aplicativos para uma tarefa. Quando uma tarefa está agendada para ser executada em um nó, o pacote é baixado e extraído um pouco antes da linha de comando da tarefa ser executada. Se o pacote especificado e a versão já estiverem instalados no nó, o pacote não será baixado e o pacote existente será usado.

Para instalar um pacote de aplicativo de tarefa, configure a propriedade [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) da tarefa:

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
> Confira [Configurações do ambiente para tarefas](jobs-and-tasks.md#environment-settings-for-tasks) para saber mais sobre as configurações do ambiente do nó de computação.

## <a name="update-a-pools-application-packages"></a>Atualizar pacotes de aplicativos de um pool

Se um pool existente já tiver sido configurado com um pacote de aplicativos, você poderá especificar um novo pacote para o pool. Isso significa:

- O serviço de Lote instala o pacote recém-especificado em todos os novos nós que ingressam no pool e em qualquer nó existente que seja reinicializado ou cuja imagem seja refeita.
- Os nós de computação que já estão no pool quando você atualiza as referências do pacote não instalam automaticamente o novo pacote de aplicativos. Esses nós de computação devem ser reinicializados ou ter sua imagem recriada para receber o novo pacote.
- Quando um novo pacote é implantado, as variáveis de ambiente criadas refletem as novas referências do pacote de aplicativos.

Neste exemplo, o pool existente tem a versão 2,7 do aplicativo de *fusão* configurado como um de seus [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Para atualizar os nós do pool com a versão 2.76b, especifique um novo [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) com a nova versão e confirme a alteração.

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

Agora que a nova versão foi configurada, o serviço de Lote instala a versão 2.76b em qualquer nó novo que ingresse no pool. Para instalar a versão 2.76b nos nós que já estão no pool, reinicialize-os ou refaça a imagem deles. Observe que os nós reinicializados retêm arquivos de implantações de pacotes anteriores.

## <a name="list-the-applications-in-a-batch-account"></a>Listar os aplicativos em uma conta do Lote

Você pode listar os aplicativos e seus pacotes em uma conta do lote usando o método [ApplicationOperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) .

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

## <a name="next-steps"></a>Próximas etapas

- A [API REST do Lote](/rest/api/batchservice) também dá suporte ao trabalho com pacotes de aplicativos. Por exemplo, consulte o elemento [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) para saber como especificar pacotes a serem instalados e [aplicativos](/rest/api/batchservice/application) para saber como obter informações sobre o aplicativo.
- Aprenda a [gerenciar de modo programático as contas e as cotas do Lote do Azure com o .NET de Gerenciamento do Lote](batch-management-dotnet.md). A biblioteca [.NET de Gerenciamento do Lote](/dotnet/api/overview/azure/batch/management) pode permitir os recursos de criação e exclusão de conta para seu aplicativo ou serviço do Lote.
