---
title: Usar e gerenciar um Ambiente do Serviço de Aplicativo
description: Saiba como criar, publicar e dimensionar aplicativos em um Ambiente do Serviço de Aplicativo. Encontre todas as tarefas comuns neste artigo.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a7fa9ece3728214fad31f0bae769e1e50206df7e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594060"
---
# <a name="use-an-app-service-environment"></a>Usar um Ambiente do Serviço de Aplicativo

Um Ambiente do Serviço de Aplicativo (ASE) é uma implantação do serviço de Azure App em uma sub-rede na instância de rede virtual do Azure de um cliente. Um ASE consiste em:

- **Front-ends**: onde http ou HTTPS termina em um ambiente do serviço de aplicativo
- **Trabalhadores**: os recursos que hospedam seus aplicativos
- **Banco de dados**: contém informações que definem o ambiente
- **Armazenamento**: usado para hospedar os aplicativos publicados pelo cliente

Você pode implantar um ASE com um VIP (IP virtual) externo ou interno para acesso ao aplicativo. Uma implantação com um VIP externo é normalmente chamada de *ase externo*. Uma implantação com um VIP interno é chamada de *ase ILB* porque ele usa um ILB (balanceador de carga interno). Para saber mais sobre o ASE ILB, veja [Criar e usar um ASE ILB][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Criar um aplicativo em um ASE

Para criar um aplicativo em um ASE, você usa o mesmo processo que normalmente cria um aplicativo, mas com algumas pequenas diferenças. Quando você cria um novo plano do Serviço de Aplicativos:

- Em vez de escolher uma localização geográfica na qual implantar o aplicativo, escolha um ASE como a localização.
- Todos os planos do serviço de aplicativo criados em um ASE só podem estar em um tipo de preço isolado.

Se você não tiver um ASE, poderá criar um seguindo as instruções em [criar um ambiente do serviço de aplicativo][MakeExternalASE].

Para criar um aplicativo em um ASE:

1. Selecione **criar um recurso**  >  **Web + celular**  >  **aplicativo Web**.

1. Insira um nome do aplicativo. Se você já tiver selecionado um plano do serviço de aplicativo em um ASE, o nome de domínio do aplicativo refletirá o nome de domínio do ASE:

    ![Seleção de nome de aplicativo][1]

1. Selecione uma assinatura.

1. Insira um nome para um novo grupo de recursos ou selecione **Usar existente** e escolha um na lista suspensa.

1. Selecione seu SO.

1. Selecione um plano do Serviço de Aplicativo existente no ASE ou crie um novo com as seguintes etapas:

    a. No portal do Azure menu do lado esquerdo, selecione **criar um recurso > aplicativo Web**.

    b. Selecione a assinatura.

    c. Selecione ou crie o grupo de recursos.

    d. Insira o nome do seu aplicativo Web.

    e. Selecione **código** ou **DockerContainer**.

    f. Selecione uma pilha de tempo de execução.

    g. Selecione **Linux** ou **Windows**. 

    h. Selecione seu ASE na lista suspensa **região** . 

    i. Selecione ou crie um novo plano do serviço de aplicativo. Se estiver criando um novo plano do serviço de aplicativo, selecione o tamanho de SKU **isolado** apropriado.

    ![Tipos de preço Isolados][2]

    > [!NOTE]
    > Os aplicativos do Linux e os aplicativos do Windows não podem estar no mesmo plano do serviço de aplicativo, mas podem estar na mesma Ambiente do Serviço de Aplicativo.
    >

1. Selecione **revisão + criar**, verifique se as informações estão corretas e, em seguida, selecione **criar**.

## <a name="how-scale-works"></a>Como funciona a escala

Cada aplicativo do Serviço de Aplicativo é executado em um plano do Serviço de Aplicativo. Os Ambientes do Serviço de Aplicativo contêm os planos do Serviço de Aplicativo; que por sua vez contêm os aplicativos. Ao dimensionar um aplicativo, você também dimensiona o plano do serviço de aplicativo e todos os aplicativos no mesmo plano.

Quando você dimensiona um plano do serviço de aplicativo, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para dimensionar operações enquanto a infraestrutura está sendo adicionada. Se você fizer várias operações de escala em sequência, a primeira solicitação de escala de infraestrutura será acionada e as outras serão enfileiradas. Quando a primeira operação de escala for concluída, a outra infraestrutura solicitará todas as operações juntas. E, quando a infraestrutura é adicionada, os planos do serviço de aplicativo são atribuídos conforme apropriado. Criar um novo plano do serviço de aplicativo é, em si, uma operação de dimensionamento porque solicita hardware adicional.

No serviço de aplicativo multilocatário, o dimensionamento é imediato porque um pool de recursos está prontamente disponível para dar suporte a ele. Em um ASE, não há tal buffer e os recursos são alocados com base na necessidade.

Em um ASE, você pode dimensionar um plano do serviço de aplicativo até 100 instâncias. Um ASE pode ter até 201 instâncias de total em todos os planos do serviço de aplicativo nesse ASE.

## <a name="ip-addresses"></a>Endereços IP

O serviço de aplicativo pode alocar um endereço IP dedicado a um aplicativo. Essa funcionalidade estará disponível depois que você configurar o SSL baseado em IP, conforme descrito em [associar um certificado TLS/SSL personalizado existente ao serviço Azure app][ConfigureSSL]. Em um ASE ILB, não é possível adicionar mais endereços IP a serem usados para o SSL baseado em IP.

Com um ASE externo, você pode configurar o SSL baseado em IP para seu aplicativo da mesma maneira que no serviço de aplicativo multilocatário. Há sempre um endereço de reposição no ASE, até 30 endereços IP. Cada vez que você usa um, outro é adicionado para que um endereço esteja sempre disponível. É necessário um atraso de tempo para alocar outro endereço IP. Esse atraso impede a adição de endereços IP em sucessão rápida.

## <a name="front-end-scaling"></a>Dimensionamento de front-end

Quando você escala horizontalmente seus planos do serviço de aplicativo, os trabalhadores são adicionados automaticamente para dar suporte a eles. Cada ASE é criado com dois front-ends. Os front-ends são dimensionados automaticamente a uma taxa de um front-end para cada conjunto de 15 instâncias do plano do serviço de aplicativo. Por exemplo, se você tiver três planos de serviço de aplicativo com cinco instâncias cada, você terá um total de 15 instâncias e três front-ends. Se você dimensionar para um total de 30 instâncias, terá quatro front-ends. Esse padrão continua conforme você dimensiona horizontalmente.

O número de front-ends alocados por padrão é bom para uma carga moderada. Você pode diminuir a taxa para o mínimo de um front-end para cada cinco instâncias. Você também pode alterar o tamanho dos front-ends. Por padrão, eles são um único núcleo. No portal do Azure, você pode alterar seu tamanho para dois ou quatro núcleos, em vez disso.

Há um encargo para alterar a taxa ou os tamanhos de front-end. Para saber mais, veja [Preços do Serviço de Aplicativo do Azure][Pricing]. Se desejar melhorar a capacidade de carga de seu ASE, você obterá mais melhorias, primeiro dimensionando para front-ends de dois núcleos antes de ajustar a taxa de escala. Alterar o tamanho do núcleo de seus front-ends causará uma atualização de seu ASE e deve ser feito fora do horário comercial normal.

Os recursos do front-end são o ponto de extremidade HTTP/HTTPS do ASE. Com a configuração de front-end padrão, o uso de memória por front-end é consistentemente cerca de 60%. O principal motivo para dimensionar seus front-ends é o uso da CPU, que é basicamente controlado pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso ao aplicativo

Em um ASE externo, o sufixo de domínio usado para a criação do aplicativo é *. &lt; asename &gt; . p.azurewebsites.net*. Se seu ASE for chamado de _ase externo_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o alcançará nessas URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Para obter informações sobre como criar um ASE externo, consulte [criar um ambiente do serviço de aplicativo][MakeExternalASE].

Em um ASE ILB, o sufixo de domínio usado para a criação do aplicativo é *. &lt; asename &gt; . appserviceenvironment.net*. Se seu ASE se chamar _ILB-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o alcançará nessas URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Para obter informações sobre como criar um ASE ILB, consulte [criar e usar um ase ILB][MakeILBASE].

A URL do SCM é usada para acessar o console do kudu ou para publicar seu aplicativo usando Implantação da Web. Para obter informações sobre o console do Kudu, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. O console do Kudu fornece uma interface do usuário da Web para depuração, upload de arquivos, edição de arquivos e muito mais.

### <a name="dns-configuration"></a>Configuração de DNS 

Quando você usa um ASE Externo, os aplicativos criados no ASE são registrados com o DNS do Azure. Não há etapas adicionais em um ASE Externo para os aplicativos fiquem disponíveis publicamente. Com um ASE do ILB, você precisa gerenciar seu DNS. Você pode fazer isso em seu servidor DNS ou em Zonas Privadas do DNS do Azure.

Para configurar o DNS em seu servidor DNS com o ASE do ILB:

1. crie uma zona para &lt;nome do ASE&gt;.appserviceenvironment.net
1. crie um registro A nessa zona que aponte * para o endereço IP do ILB
1. crie um registro A nessa zona que aponte @ para o endereço IP do ILB
1. crie uma zona em &lt;nome do ASE&gt;.appserviceenvironment.net chamada scm
1. crie um registro A na zona scm que aponte * para o endereço IP do ILB

Para configurar o DNS em Zonas Privadas do DNS do Azure:

1. crie uma zona privada de DNS do Azure chamada &lt;ASE name&gt;.appserviceenvironment.net
1. crie um registro A nessa zona que aponte * para o endereço IP do ILB
1. crie um registro A nessa zona que aponte @ para o endereço IP do ILB
1. crie um registro A nessa zona que aponte *.scm para o endereço IP do ILB

As configurações de DNS do sufixo de domínio padrão do ASE não restringem seus aplicativos a serem acessados apenas por esses nomes. Você pode definir um nome de domínio personalizado sem nenhuma validação em seus aplicativos em um ASE do ILB. Se você quiser criar uma zona chamada *contoso.net*, poderá fazer isso e apontar para o endereço IP ILB. O nome de domínio personalizado funciona para solicitações de aplicativo, mas não para o site do scm. O site do SCM está disponível somente em *&lt; AppName &gt; . SCM. &lt; asename &gt; . appserviceenvironment.net*. 

A zona chamada *. &lt; asename &gt; . appserviceenvironment.net* é globalmente exclusivo. Antes de maio de 2019, os usuários podiam especificar o sufixo de domínio do ASE do ILB. Se você quisesse usar *. contoso.com* para o sufixo de domínio, você poderia fazer isso e isso incluiria o site do SCM. Havia desafios com esse modelo, incluindo o gerenciamento do certificado SSL padrão, a falta de logon único com o site do scm e a necessidade de usar um certificado curinga. O processo de atualização de certificado padrão do ASE do ILB também era problemático e causava a reinicialização do aplicativo. Para resolver esses problemas, o comportamento do ASE do ILB foi alterado de maneira a passar a usar um sufixo de domínio baseado no nome do ASE e com um sufixo de propriedade da Microsoft. A alteração no comportamento do ASE do ILB afeta apenas o ASEs do ILB criados após maio de 2019. ASEs do ILB preexistente ainda precisam gerenciar o certificado padrão do ASE e sua configuração de DNS.

## <a name="publishing"></a>Publicando

Em um ASE, assim como no serviço de aplicativo multilocatário, você pode publicar por estes métodos:

- Implantação da Web
- FTP
- CI (Integração contínua)
- Operação de arrastar e soltar no console do Kudu
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA

Com um ASE externo, essas opções de publicação funcionam da mesma maneira. Para saber mais, veja [Implantação no Serviço de Aplicativo do Azure][AppDeploy].

Com um ASE ILB, os pontos de extremidade de publicação só estão disponíveis por meio do ILB. O ILB está em um IP privado na sub-rede do ASE, na rede virtual. Se você não tiver acesso à rede para o ILB, não poderá publicar nenhum aplicativo nesse ASE. Conforme observado em [criar e usar um ase ILB][MakeILBASE], você deve configurar o DNS para os aplicativos no sistema. Esse requisito inclui o ponto de extremidade SCM. Se os pontos de extremidade não estiverem definidos corretamente, você não poderá publicar. Seus IDEs também devem ter acesso à rede para o ILB para publicar diretamente nele.

Sem alterações adicionais, os sistemas de CI baseados na Internet, como GitHub e Azure DevOps, não funcionam com um ASE ILB porque o ponto de extremidade de publicação não é acessível pela Internet. Você pode habilitar a publicação em um ASE ILB do Azure DevOps instalando um agente de liberação auto-hospedado na rede virtual que contém o ASE ILB. Como alternativa, você também pode usar um sistema de CI que usa um modelo de pull, como o dropbox.

Os pontos de extremidade de publicação para aplicativos em um ASE ILB usam o domínio com o qual o ASE ILB foi criado. Você pode vê-lo no perfil de publicação do aplicativo e no painel do portal do aplicativo (em **visão geral**  >  **Essentials** e também em **Propriedades**).

## <a name="storage"></a>Armazenamento

Um ASE tem 1 TB de armazenamento para todos os aplicativos no ASE. Um plano do serviço de aplicativo no SKU de preços isolado tem um limite de 250 GB. Em um ASE, 250 GB de armazenamento são adicionados por plano do serviço de aplicativo até o limite de 1 TB. Você pode ter mais planos do serviço de aplicativo do que apenas quatro, mas não há mais armazenamento adicionado além do limite de 1 TB.

## <a name="logging"></a>Registro em log

Você pode integrar seu ASE com Azure Monitor para enviar logs sobre o ASE para o armazenamento do Azure, hubs de eventos do Azure ou Log Analytics. Estes itens estão registrados hoje:

| Situação | Mensagem |
|---------|----------|
| O ASE não está íntegro | O ASE especificado não está íntegro devido a uma configuração de rede virtual inválida. O ASE será suspenso se o estado não íntegro continuar. Verifique se as diretrizes definidas aqui são seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| A sub-rede do ASE está quase sem espaço | O ASE especificado está em uma sub-rede que está quase sem espaço. Há {0} endereços restantes. Depois que esses endereços forem esgotados, o ASE não será capaz de dimensionar.  |
| O ASE está se aproximando do limite de instância total | O ASE especificado está se aproximando do limite de instância total do ASE. Atualmente, ele contém {0} instâncias do plano do serviço de aplicativo de um máximo de 201 instâncias. |
| O ASE não consegue alcançar uma dependência | O ASE especificado não é capaz de alcançar {0} .  Verifique se as diretrizes definidas aqui são seguidas: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE suspenso | O ASE especificado está suspenso. A suspensão do ASE pode ser devido a uma deficiência de conta ou uma configuração de rede virtual inválida. Resolva a causa raiz e retome o ASE para continuar a fornecer tráfego. |
| A atualização do ASE foi iniciada | Uma atualização de plataforma para o ASE especificado foi iniciada. Espera-se atrasos nas operações de dimensionamento. |
| A atualização do ASE foi concluída | Uma atualização de plataforma para o ASE especificado foi concluída. |
| Operações de escala iniciadas | Um plano do serviço de aplicativo ( {0} ) começou a ser dimensionado. Estado desejado: {1} I {2} Workers.
| Operações de escala concluídas | Um plano do serviço de aplicativo ( {0} ) concluiu o dimensionamento. Estado atual: {1} I {2} Workers. |
| Falha nas operações de escala | Um plano do serviço de aplicativo ( {0} ) falhou ao ser dimensionado. Estado atual: {1} I {2} Workers. |

Para habilitar o registro em log em seu ASE:

1. No portal, vá para **configurações de diagnóstico**.
1. Selecione **Adicionar configuração de diagnóstico**.
1. Forneça um nome para a integração de log.
1. Selecione e configure os destinos de log desejados.
1. Selecione **AppServiceEnvironmentPlatformLogs**.

![Configurações do log de diagnóstico do ASE][4]

Se você se integrar com o Log Analytics, poderá ver os logs selecionando **logs** no portal do ase e criando uma consulta no **AppServiceEnvironmentPlatformLogs**. Os logs são emitidos somente quando o ASE tem um evento que irá dispará-lo. Se seu ASE não tiver esse tipo de evento, não haverá nenhum log. Para ver rapidamente um exemplo de logs em seu espaço de trabalho do Log Analytics, execute uma operação de dimensionamento com um dos planos do serviço de aplicativo em seu ASE. Em seguida, você pode executar uma consulta em **AppServiceEnvironmentPlatformLogs** para ver esses logs. 

**Criando um alerta**

Para criar um alerta em seus logs, siga as instruções em [criar, exibir e gerenciar alertas de log usando Azure monitor](../../azure-monitor/alerts/alerts-log.md). Em resumo:

* Abra a página alertas em seu portal do ASE
* Selecionar **nova regra de alerta**
* Selecione seu recurso para ser seu espaço de trabalho do Log Analytics
* Defina sua condição com uma pesquisa de logs personalizada para usar uma consulta como "AppServiceEnvironmentPlatformLogs | onde ResultDescription contém "tem começado a ser dimensionado" ou o que você desejar. Defina o limite conforme apropriado. 
* Adicione ou crie um grupo de ação conforme desejado. O grupo de ações é onde você define a resposta para o alerta, como enviar um email ou uma mensagem SMS
* Nomeie o alerta e salve-o.

## <a name="upgrade-preference"></a>Preferência de atualização

Se você tiver vários ASEs, talvez queira que alguns ASEs sejam atualizados antes de outros. No objeto do **Gerenciador de recursos de HostingEnvironment** do ase, você pode definir um valor para **upgradePreference**. A configuração **upgradePreference** pode ser configurada usando um modelo, ARMClient ou https://resources.azure.com . Os três valores possíveis são:

- **Nenhum**: o Azure atualizará seu ASE em nenhum lote específico. Esse valor é o padrão.
- **Antecipadamente**: seu ase será atualizado na primeira metade das atualizações do serviço de aplicativo.
- **Tarde**: seu ase será atualizado na segunda metade das atualizações do serviço de aplicativo.

Se você estiver usando https://resources.azure.com o, siga estas etapas para definir o valor de **upgradePreferences** :

1. Acesse resources.azure.com e entre com sua conta do Azure.
1. Percorra os recursos para assinaturas nome da \/ \[ assinatura \] \/ resourceGroups nome \/ \[ do grupo de recursos \] \/ provedores \/ Microsoft. Web \/ hostingenvironments \/ \[ ase nome \] .
1. Selecione **leitura/gravação** na parte superior.
1. Selecione **Editar**.
1. Defina **upgradePreference** para qualquer um dos três valores desejados.
1. Selecione **patch**.

![recursos de exibição do Azure com][5]

O recurso **upgradePreferences** faz mais sentido quando você tem vários ases porque seu ases "antecipado" será atualizado antes de seu "final" ases. Quando você tiver vários ASEs, deverá definir seu ASEs de desenvolvimento e teste como "antecipado" e sua ASEs de produção como "tardia".

## <a name="pricing"></a>Preços

O SKU de preços chamado *isolado* é para uso somente com ases. Todos os planos do serviço de aplicativo hospedados no ASE estão no SKU de preços isolado. As taxas isoladas para os planos do serviço de aplicativo podem variar por região.

Além do preço de seus planos do serviço de aplicativo, há uma taxa fixa para o próprio ASE. A taxa fixa não é alterada com o tamanho do seu ASE. Ele paga a infraestrutura do ASE em uma taxa de escala padrão de um front-end adicional para cada 15 instâncias do plano do serviço de aplicativo.

Se a taxa de escala padrão de um front-end para cada 15 instâncias do plano do serviço de aplicativo não for rápida o suficiente, você poderá ajustar a taxa na qual os front-ends são adicionados ou o tamanho dos front-ends. Quando você ajustar a taxa ou o tamanho, pagará pelos núcleos de front-end que não seriam adicionados por padrão.

Por exemplo, se você ajustar a taxa de escala para 10, um front-end será adicionado para cada 10 instâncias nos planos do Serviço de Aplicativo. O valor fixo abrange uma taxa de escala de um front-end para cada 15 instâncias. Com uma taxa escala de 10, você pode pagar uma taxa para o terceiro front-end adicionado para as 10 instâncias de plano de Serviço de Aplicativo. Você não precisará pagar por ele quando você chegar a 15 instâncias porque ele terá sido adicionado automaticamente.

Se você ajustar o tamanho dos front-ends para dois núcleos, mas não ajustar a taxa, você pagará pelos núcleos extras. Um ASE é criado com dois front-ends, portanto, mesmo abaixo do limite de dimensionamento automático, você pagaria por dois núcleos extras se aumentar o tamanho para front-ends de dois núcleos.

Para saber mais, veja [Preços do Serviço de Aplicativo do Azure][Pricing].

## <a name="delete-an-ase"></a>Excluir um ASE

Para excluir um ASE:

1. Selecione **excluir** na parte superior do painel de **ambiente do serviço de aplicativo** .

1. Insira o nome do seu ASE para confirmar que deseja excluí-lo. Ao excluir um ASE, você também exclui todo o conteúdo dentro dele.

    ![Exclusão de ASE][3]

1. Selecione **OK**.

## <a name="ase-cli"></a>CLI DO ASE

Há recursos de linha de comando para administrar em um ASE.  Os comandos AZ CLI estão indicados abaixo.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
