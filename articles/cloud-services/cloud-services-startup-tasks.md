---
title: Executar Tarefas de Inicialização nos Serviços de Nuvem do Azure | Microsoft Docs
description: As tarefas de inicialização ajudam a preparar o ambiente de serviço de nuvem para seu aplicativo. Isso mostra o funcionamento das tarefas de inicialização e como criá-las
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: gwallace
ms.openlocfilehash: cea28aba4c57f69a030d05ac192f9578967cbc3f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359469"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de inicialização para um serviço de nuvem
Você pode usar as tarefas de inicialização para executar operações antes do início de uma função. As operações que talvez você queira executar incluem a instalação de um componente, o registro de componentes COM, a configuração de chaves do Registro ou o início de um processo de longa duração.

> [!NOTE]
> As tarefas de inicialização não são aplicáveis às Máquinas Virtuais, apenas às funções Web e de Trabalho do Serviço de Nuvem.
> 
> 

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de inicialização
As tarefas de inicialização são as ações executadas antes de suas funções começarem e são definidas no arquivo [ServiceDefinition.csdef] usando o elemento [Tarefa] dentro do elemento [Startup]. Com frequência, as tarefas de inicialização são arquivos em lotes, mas elas também podem ser aplicativos de console ou arquivos em lotes que iniciam scripts do PowerShell.

As variáveis de ambiente passam informações para uma tarefa de inicialização e o armazenamento local pode ser usado para transmitir informações para fora de uma tarefa de inicialização. Por exemplo, uma variável de ambiente pode especificar o caminho para um programa que você deseja instalar e arquivos podem ser gravados no armazenamento local que poderá então ser lido posteriormente por suas funções.

Sua tarefa de inicialização pode registrar informações e erros no diretório especificado pela variável de ambiente **TEMP** . Durante a tarefa de inicialização, a variável de ambiente **TEMP** determina o diretório *C:\\Resources\\temp\\[guid].[nomefunção]\\RoleTemp* ao executar na nuvem.

As tarefas de inicialização também podem ser executadas várias vezes entre as reinicializações. Por exemplo, a tarefa de inicialização será executada sempre que a função for reciclada, e as reciclagens de função nem sempre incluirão uma reinicialização. As tarefas de inicialização devem ser gravadas de uma maneira que permita que sejam executadas diversas vezes sem problemas.

As tarefas de inicialização devem terminar com um **errorlevel** (ou código de saída) zero para que o processo de inicialização seja concluído. Se uma tarefa de inicialização terminar com um **errorlevel**diferente de zero, a função não será iniciada.

## <a name="role-startup-order"></a>Ordem de inicialização de função
A seguir, o procedimento de inicialização da função no Azure:

1. A instância está marcada como **Iniciando** e não recebe o tráfego.
2. Todas as tarefas de inicialização são executadas de acordo com o atributo **taskType** .
   
   * As tarefas **simples** são executadas de forma síncrona, uma de cada vez.
   * As tarefas em **segundo plano** e **primeiro plano** são iniciadas de forma assíncrona, paralelas com a tarefa de inicialização.  
     
     > [!WARNING]
     > O IIS pode não estar totalmente configurado durante a fase de tarefas de inicialização no processo de inicialização e, portanto, os dados específicos da função podem não estar disponíveis. As tarefas de inicialização que exigem dados específicos da função devem usar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. O processo de host da função é iniciado e o site é criado no IIS.
4. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) é chamado.
5. A instância é marcada como **Pronta** e o tráfego é roteado para a instância.
6. O método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) é chamado.

## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de inicialização
As tarefas de inicialização são definidas no arquivo [ServiceDefinition.csdef] , no elemento **Tarefa** . O atributo **commandLine** especifica o nome e os parâmetros do arquivo de inicialização em lote ou do comando de console, o atributo **executionContext** especifica o nível de privilégio da tarefa de inicialização e o atributo **taskType** especifica como a tarefa será executada.

Neste exemplo, uma variável de ambiente, **MyVersionNumber**, é criada para a tarefa de inicialização e definida para o valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

No exemplo a seguir, o arquivo em lotes **Startup.cmd** grava a linha "A versão atual é 1.0.0.0" no arquivo StartupLog.txt no diretório especificado pela variável de ambiente TEMP. A linha `EXIT /B 0` garante que a tarefa de inicialização termine com um **errorlevel** zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> No Visual Studio, a propriedade **Copy to Output Directory** do arquivo de inicialização em lote deve ser definida para **Copy Always** para verificar se o arquivo de inicialização em lote está implantado corretamente em seu projeto no Azure (**approot\\bin** para as funções Web e **approot** para as funções de trabalho).
> 
> 

## <a name="description-of-task-attributes"></a>Descrição dos atributos da tarefa
A seguir, a descrição dos atributos do elemento **Task** do arquivo [ServiceDefinition.csdef] :

**commandLine** - especifica a linha de comando para a tarefa de inicialização:

* O comando, com parâmetros de linha de comando opcionais, que inicia a tarefa de inicialização.
* Geralmente, esse é o nome do arquivo de um arquivo em lotes .cmd ou .bat.
* A tarefa é relativa à pasta Bin do \\AppRoot da implantação. As variáveis de ambiente não são expandidas para a determinação do caminho e do arquivo da tarefa. Se a expansão de ambiente for necessária, você poderá criar um script .cmd pequeno que chame sua tarefa de inicialização.
* Pode ser um aplicativo de console ou um arquivo em lotes que inicie um [script do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - especifica o nível de privilégio para a tarefa de inicialização. O nível de privilégio pode ser limitado ou elevado:

* **limitado**  
  A tarefa de inicialização é executada com os mesmos privilégios da função. Quando o atributo **executionContext** do elemento [Tempo de execução] também é **limitado**, os privilégios do usuário são usados.
* **elevado**  
  A tarefa de inicialização é executada com privilégios de administrador. Isso permite que as tarefas de inicialização instalem programas, façam alterações de configuração no IIS, executem alterações no Registro e outras tarefas no nível de administrador sem aumentar o nível de privilégio da própria função.  

> [!NOTE]
> O nível de privilégio de uma tarefa de inicialização não precisa ser igual ao da própria função.
> 
> 

**taskType** - especifica a maneira como uma tarefa de inicialização é executada.

* **simpless**  
  As tarefas são executadas de forma síncrona, uma de cada vez, na ordem especificada no arquivo [ServiceDefinition.csdef] . Quando uma tarefa de inicialização **simples**termina com um **errorlevel** zero, a próxima tarefa de inicialização **simples** é executada. Se não houver nenhum mais tarefas de inicialização **simples** a serem executadas, então a função será iniciada.   
  
  > [!NOTE]
  > Se a tarefa **simples** terminar com um **errorlevel** diferente de zero, a instância será bloqueada. As tarefas de inicialização **simples** subsequentes , e a própria função, não serão iniciadas.
  > 
  > 
  
    Para garantir que o arquivo em lote terminará com um **errorlevel** zero, execute o comando `EXIT /B 0` no final do processo do arquivo em lote.
* **segundo plano**  
  As tarefas são executadas de forma assíncrona, em paralelo com a inicialização da função.
* **primeiro plano**  
  As tarefas são executadas de forma assíncrona, em paralelo com a inicialização da função. A principal diferença entre uma tarefa em **primeiro plano** e **segundo plano** é que uma tarefa em **primeiro plano** evita que a função recicle ou finalize até que a tarefa seja concluída. As tarefas em **segundo plano** não têm essa restrição.

## <a name="environment-variables"></a>Variáveis de ambiente
As variáveis de ambiente são uma maneira de passar informações para uma tarefa de inicialização. Por exemplo, você pode colocar o caminho para um blob que contenha um programa a ser instalado, ou números de porta que sua função usará, ou configurações para controlar recursos de sua tarefa de inicialização.

Há dois tipos de variáveis de ambiente para tarefas de inicialização; variáveis de ambiente estáticas e variáveis de ambiente baseadas nos membros da classe [RoleEnvironment] . Ambas estão na seção [Ambiente] do arquivo [ServiceDefinition.csdef] e usam o elemento [Variable] e o atributo **name**.

As variáveis de ambiente estáticas usam o atributo **value** do elemento [Variable] . O exemplo acima cria a variável de ambiente **MyVersionNumber** que tem um valor estático "**1.0.0.0**". Outro exemplo seria criar uma variável de ambiente **StagingOrProduction** que você pode definir manualmente para os valores de "**preparo**" ou "**produção**" para executar ações diferentes de inicialização com base no valor da variável de ambiente **StagingOrProduction**.

As variáveis de ambiente baseadas nos membros da classe RoleEnvironment não usam o atributo **value** do elemento [Variable] . Em vez disso, o elemento-filho [RoleInstanceValue], com o devido valor do atributo **XPath**, é usado para criar uma variável de ambiente com base em um membro específico da classe [RoleEnvironment]. Os valores do atributo **XPath** para acessar os diversos valores [RoleEnvironment] podem ser encontrados [aqui](cloud-services-role-config-xpath.md).

Por exemplo, para criar uma variável de ambiente "**true**" quando a instância está em execução no emulador de computação, e "**false**" quando em execução na nuvem, use os seguintes elementos [Variable] e [RoleInstanceValue] :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Próximas etapas
Saiba como executar algumas [tarefas de inicialização comuns](cloud-services-startup-tasks-common.md) com seu Serviço de Nuvem.

[Empacote](cloud-services-model-and-package.md) seu Serviço de Nuvem.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarefa]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Tempo de execução]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
