---
title: Tópicos avançados de atualização de aplicativos
description: Este artigo aborda alguns tópicos avançados relativos à atualização de um aplicativo do Service Fabric.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792041"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização do aplicativo Service Fabric: Tópicos avançados

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Adicionar ou remover tipos de serviço durante uma atualização de aplicativo

Se um novo tipo de serviço é adicionado a um aplicativo publicado como parte de uma atualização, o novo serviço é adicionado ao aplicativo implantado. Essa atualização não afeta nenhuma das instâncias de serviço que já fazem parte do aplicativo, mas uma instância do tipo de serviço que foi adicionada deve ser criada para o novo tipo de serviço estar ativo (consulte [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

Da mesma forma, os tipos de serviços também podem ser removidos de um aplicativo como parte de uma atualização. No entanto, todas as instâncias de serviço do tipo de serviço a ser removido devem ser removidas antes de prosseguir com a atualização (consulte [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Evitar quedas de conexão durante o tempo de inatividade planejado do serviço sem estado

Para tempos de inatividade planejados de instância sem monitoração de estado, como atualização de aplicativo/cluster ou desativação de nó, as conexões podem ser descartadas conforme o ponto de extremidade exposto é removido após a instância falhar, o que resulta em fechamentos de conexão forçada.

Para evitar isso, configure o recurso *RequestDrain* adicionando uma *duração de atraso de fechamento de instância* na configuração de serviço para permitir que as solicitações existentes de dentro do cluster sejam drenadas nos pontos de extremidade expostos. Isso é obtido, pois o ponto de extremidade anunciado pela instância sem estado é removido *antes* de o atraso começar antes de fechar a instância. Esse atraso permite que as solicitações existentes sejam descarregadas normalmente antes que a instância realmente fique inativa. Os clientes são notificados sobre a alteração do ponto de extremidade por uma função de retorno de chamada no momento da inicialização do atraso, para que eles possam reresolver o ponto de extremidade e evitar o envio de novas solicitações para a instância que está sendo desativada. Essas solicitações podem ser originadas de clientes usando [proxy reverso](./service-fabric-reverseproxy.md) ou usando APIs de resolução de ponto de extremidade de serviço com o modelo de notificação ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) para atualizar os pontos de extremidade.

### <a name="service-configuration"></a>Configuração de serviço

Há várias maneiras de configurar o atraso no lado do serviço.

 * **Ao criar um novo serviço**, especifique um `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **Ao definir o serviço na seção padrões no manifesto do aplicativo**, atribua a `InstanceCloseDelayDurationSeconds` Propriedade:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Ao atualizar um serviço existente**, especifique um `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Ao criar ou atualizar um serviço existente por meio do modelo ARM**, especifique o `InstanceCloseDelayDuration` valor (versão mínima da API com suporte: 2019-11-01-Preview):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Configuração do cliente

Para receber uma notificação quando um ponto de extremidade for alterado, os clientes deverão registrar um retorno de chamada, consulte [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
A notificação de alteração é uma indicação de que os pontos de extremidade foram alterados, o cliente deve reresolver os pontos de extremidade e não usar os pontos de extremidade que não são mais anunciados, pois eles ficarão inativos em breve.

### <a name="optional-upgrade-overrides"></a>Substituições de atualização opcionais

Além de definir as durações de atraso padrão por serviço, você também pode substituir o atraso durante a atualização de aplicativo/cluster usando a mesma `InstanceCloseDelayDurationSec` opção ():

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

A duração do atraso substituído só se aplica à instância de atualização invocada e não altera as configurações de atraso de serviço individual. Por exemplo, você pode usar isso para especificar um atraso de a fim de `0` ignorar os atrasos de atualização pré-configurados.

> [!NOTE]
> * As configurações para drenar solicitações não poderão impedir que o balanceador de carga do Azure envie novas solicitações para os pontos de extremidade que estão sendo descarregados.
> * Um mecanismo de resolução baseado em reclamações não resultará em descarga normal de solicitações, pois dispara uma resolução de serviço após uma falha. Conforme descrito anteriormente, isso deve ser aprimorado para assinar as notificações de alteração do ponto de extremidade usando o [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
> * As configurações não são respeitadas quando a atualização é um dos impactantes, ou seja Quando as réplicas não serão trazidas durante a atualização.
>
>

> [!NOTE]
> Esse recurso pode ser configurado em serviços existentes usando Update-ServiceFabricService cmdlet ou o modelo ARM, conforme mencionado acima, quando a versão do código do cluster é 7.1.XXX ou superior.
>
>

## <a name="manual-upgrade-mode"></a>Modo de atualização manual

> [!NOTE]
> O modo de atualização *Monitorado* é recomendado para todas as atualizações do Service Fabric.
> O modo de atualização *UnmonitoredManual* só deve ser considerado em caso de atualização com falha ou suspensa. 
>
>

Em modo *Monitorado*, o Service Fabric aplica políticas de integridade para garantir que o aplicativo esteja íntegro conforme o andamento da atualização. Se as políticas de integridade forem violadas, a atualização será suspensa ou revertida automaticamente, dependendo do *FailureAction* especificado.

Em modo *UnmonitoredManual*, o administrador do aplicativo tem controle total sobre o andamento da atualização. Esse modo é útil durante a aplicação de políticas de avaliação de integridade personalizadas ou a realização de atualizações não convencionais para ignorar completamente o monitoramento de integridade (por exemplo, o aplicativo já está perdendo dados). Uma atualização em execução nesse modo se suspenderá depois de concluir cada UD e deverá ser retomada explicitamente usando-se [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade). Quando uma atualização está suspensa e pronta para ser retomada pelo usuário, o estado de atualização mostrará *RollforwardPending* (consulte [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)).

Por fim, o modo *UnmonitoredAuto* é útil para realizar iterações de atualização rápidas durante o desenvolvimento ou o teste do serviço porque nenhuma entrada do usuário é necessária e nenhuma política de integridade do aplicativo é avaliada.

## <a name="upgrade-with-a-diff-package"></a>Atualizar usando um pacote diff

Em vez de provisionar um pacote de aplicativos completo, as atualizações também podem ser realizadas pelo provisionamento de pacotes diferentes que contenham apenas os pacotes de dados/configurações/códigos atualizados com o manifesto do aplicativo completo e os manifestos do serviço completo. Os pacotes de aplicativo completos só são necessários para a instalação inicial de um aplicativo no cluster. Atualizações subsequentes podem ser de pacotes de aplicativos completos ou diferentes.  

Qualquer referência no manifesto do aplicativo ou nos manifestos do serviço de um pacote diferente que não possa ser encontrada no pacote de aplicativos é automaticamente substituída pela versão provisionada no momento.

Os cenários para usar um pacote diferente são:

* Quando você tiver um pacote de aplicativo grande que faça referência a vários arquivos de manifesto do serviço e/ou a vários pacotes de código, de configuração ou de dados.
* Quando você tiver um sistema de implantação que gere o layout de compilação diretamente do seu processo de compilação do aplicativo. Nesse caso, embora o código não tenha mudado, assemblies recém-criados têm uma soma de verificação diferente. O uso de um pacote de aplicativo completo exige que você atualize a versão de todos os pacotes de código. Com o uso de um pacote diff, você fornece somente os arquivos alterados e os arquivos de manifesto em que a versão foi alterada.

Quando um aplicativo é atualizado usando o Visual Studio, um pacote diff é publicado automaticamente. Para criar um pacote diff manualmente, o manifesto do aplicativo e os manifestos do serviço devem ser atualizados, mas apenas os pacotes alterados devem ser incluídos no pacote de aplicativos final.

Por exemplo, vamos começar com o aplicativo a seguir (números de versão fornecidos para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Vamos supor que você quisesse atualizar apenas o pacote de códigos de service1 usando um pacote diff. Seu aplicativo atualizado tem as seguintes alterações feitas na versão:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Nesse caso, você atualiza o manifesto do aplicativo para 2.0.0 e o manifesto do serviço para service1 para refletir a atualização do pacote de códigos. A pasta para o pacote de aplicativos teria a seguinte estrutura:

```text
app1/
  service1/
    code/
```

Em outras palavras, crie um pacote de aplicativos completo normalmente e, em seguida, remova todas as pastas de códigos/configurações/pacotes de dados para as quais a versão não foi alterada.

## <a name="upgrade-application-parameters-independently-of-version"></a>Atualizar parâmetros do aplicativo independentemente da versão

Às vezes, é desejável alterar os parâmetros de um aplicativo Service Fabric sem alterar a versão do manifesto. Isso pode ser feito convenientemente usando o sinalizador **-ApplicationParameter** com o cmdlet **Start-ServiceFabricApplicationUpgrade** do PowerShell do Azure Service Fabric. Suponha um aplicativo Service Fabric com as seguintes propriedades:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Agora, atualize o aplicativo usando o cmdlet **Start-ServiceFabricApplicationUpgrade** . Este exemplo mostra uma atualização monitorada, mas uma atualização não monitorada também pode ser usada. Para ver uma descrição completa dos sinalizadores aceitos por esse cmdlet, consulte a [referência de módulo do PowerShell Service Fabric do Azure](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters)

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Após a atualização, confirme se o aplicativo tem os parâmetros atualizados e a mesma versão:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Reverter atualizações de aplicativos

Embora atualizações possam ser roladas para frente em um dos três modos (*Monitored*, *UnmonitoredAuto* ou *UnmonitoredManual*), elas só podem ser revertidas em modo *UnmonitoredAuto* ou *UnmonitoredManual*. A reversão em modo *UnmonitoredAuto* funciona da mesma forma que o roll forward, com a exceção de que o valor padrão de *UpgradeReplicaSetCheckTimeout* é diferente - consulte [Parâmetros de atualização do aplicativo](service-fabric-application-upgrade-parameters.md). A reversão em modo *UnmonitoredManual* funciona da mesma forma que o roll forward - a reversão se suspenderá depois de concluir cada UD e deve ser retomada explicitamente usando [Resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) para continuar a reversão.

As reversões poderão ser disparadas automaticamente quando as políticas de integridade de uma atualização em modo *Monitored* com um *FailureAction* de *Rollback* forem violadas (consulte [Parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md)) ou usando-se explicitamente [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback).

Durante a reversão, o valor de *UpgradeReplicaSetCheckTimeout* e o modo ainda podem ser alterados a qualquer momento usando-se [Update-ServiceFabricApplicationUpgrad](/powershell/module/servicefabric/update-servicefabricapplicationupgrade).

## <a name="next-steps"></a>Próximas etapas
[Atualização do aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante a atualização de aplicativo usando o Visual Studio.

[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).
