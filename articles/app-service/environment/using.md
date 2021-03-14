---
title: Usar um Ambiente do Serviço de Aplicativo
description: Saiba como usar seu Ambiente do Serviço de Aplicativo para hospedar aplicativos isolados.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: d4cd673b5029d8379a699becd7339a265c787390
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586408"
---
# <a name="using-an-app-service-environment"></a>Usando um Ambiente do Serviço de Aplicativo

O Ambiente do Serviço de Aplicativo (ASE) é uma implantação de locatário único do serviço de Azure App que injeta diretamente em uma VNet (rede virtual) do Azure de sua escolha. É um sistema que é usado somente por um cliente. Os aplicativos implantados no ASE estão sujeitos aos recursos de rede que são aplicados à sub-rede do ASE. Não há recursos adicionais que precisem ser habilitados em seus aplicativos para estarem sujeitos a esses recursos de rede. 

## <a name="create-an-app-in-an-ase"></a>Criar um aplicativo em um ASE

Para criar um aplicativo em um ASE, você usa o mesmo processo que normalmente cria um aplicativo, mas com algumas pequenas diferenças. Quando você cria um novo plano do Serviço de Aplicativos:

- Em vez de escolher uma localização geográfica na qual implantar o aplicativo, escolha um ASE como a localização.
- Todos os planos do serviço de aplicativo criados em um ASE só podem estar em um tipo de preço v2 isolado.

Se você não tiver um ASE, poderá criar um seguindo as instruções em [criar um ambiente do serviço de aplicativo][MakeASE].

Para criar um aplicativo em um ASE:

1. Selecione **criar um recurso**  >  **Web + celular**  >  **aplicativo Web**.

1. Selecione uma assinatura.

1. Insira um nome para um novo grupo de recursos ou selecione **Usar existente** e escolha um na lista suspensa.

1. Insira um nome do aplicativo. Se você já tiver selecionado um plano do serviço de aplicativo em um ASE, o nome de domínio do aplicativo refletirá o nome de domínio do ASE:

    ![criar um aplicativo em um ASE][1]

1. Selecione o tipo de publicação, a pilha e o sistema operacional.

1.  Selecione região. Aqui, você precisa selecionar um Ambiente do Serviço de Aplicativo v3 já existente.  Não é possível fazer um ASEv3 durante a criação do aplicativo 

1. Selecione um plano do serviço de aplicativo existente em seu ASE ou crie um novo. Se estiver criando um novo aplicativo, selecione o tamanho desejado para o plano do serviço de aplicativo. A única SKU que você pode selecionar para seu aplicativo é um SKU de preço v2 isolado.

    ![Tipos de preço v2 isolados][2]

    > [!NOTE]
    > Os aplicativos do Linux e os aplicativos do Windows não podem estar no mesmo plano do serviço de aplicativo, mas podem estar na mesma Ambiente do Serviço de Aplicativo.
    >

1. Selecione **Avançar: monitorando**  se você deseja habilitar o Application insights com seu aplicativo, você pode fazer isso aqui durante o fluxo de criação. 

1.  Selecione **Avançar: marcações** adicione as marcas que você deseja ao aplicativo  

1. Selecione **revisão + criar**, verifique se as informações estão corretas e, em seguida, selecione **criar**.

## <a name="how-scale-works"></a>Como funciona a escala

Cada aplicativo do Serviço de Aplicativo é executado em um plano do Serviço de Aplicativo. Os Ambientes do Serviço de Aplicativo contêm os planos do Serviço de Aplicativo; que por sua vez contêm os aplicativos. Ao dimensionar um aplicativo, você também dimensiona o plano do serviço de aplicativo e todos os aplicativos no mesmo plano.

Quando você dimensiona um plano do serviço de aplicativo, a infraestrutura necessária é adicionada automaticamente. Há um atraso de tempo para dimensionar operações enquanto a infraestrutura está sendo adicionada. Quando você dimensiona um plano do serviço de aplicativo, todas as outras operações de escala solicitadas do mesmo sistema operacional e tamanho aguardarão até que a primeira seja concluída. Após a conclusão da operação de escala de bloqueio, todas as solicitações em fila são processadas ao mesmo tempo. Uma operação de dimensionamento em um tamanho e sistema operacional não bloqueará o dimensionamento das outras combinações de tamanho e so. Por exemplo, se você dimensionou um plano do serviço de aplicativo I2v2 do Windows, todas as outras solicitações para dimensionar o Windows I2v2 nesse ASE serão enfileiradas até que isso seja concluído.   

No serviço de aplicativo multilocatário, o dimensionamento é imediato porque um pool de recursos está prontamente disponível para dar suporte a ele. Em um ASE, não há tal buffer e os recursos são alocados com base na necessidade.

## <a name="app-access"></a>Acesso ao aplicativo

Em um ASE, o sufixo de domínio usado para a criação do aplicativo é *. &lt; asename &gt; . appserviceenvironment.net*. Se seu ASE for chamado _meu-ase_ e você hospedar um aplicativo chamado _contoso_ nesse ASE, você o alcançará nessas URLs:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Para obter informações sobre como criar um ASE, consulte [criar um ambiente do serviço de aplicativo][MakeASE].

A URL do SCM é usada para acessar o console do kudu ou para publicar seu aplicativo usando Implantação da Web. Para obter informações sobre o console do Kudu, consulte [Console do Kudu para o Serviço de Aplicativo do Azure][Kudu]. O console do Kudu fornece uma interface do usuário da Web para depuração, upload de arquivos, edição de arquivos e muito mais.

### <a name="dns-configuration"></a>Configuração de DNS 

O ASE usa pontos de extremidade privados para o tráfego de entrada. Ele não é configurado automaticamente com as zonas privadas do DNS do Azure. Se você quiser usar seu próprio servidor DNS, precisará adicionar os seguintes registros:

1. crie uma zona para &lt;nome do ASE&gt;.appserviceenvironment.net
1. criar um registro A na zona que aponta para o endereço IP de entrada usado pelo ponto de extremidade privado do ASE
1. criar um registro A na zona que aponta para @ para o endereço IP de entrada usado pelo ponto de extremidade privado do ASE
1. crie uma zona em &lt;nome do ASE&gt;.appserviceenvironment.net chamada scm
1. criar um registro A na zona SCM que aponta para o endereço IP usado pelo ponto de extremidade privado do ASE

Para configurar o DNS em Zonas Privadas do DNS do Azure:

1. crie uma zona privada de DNS do Azure chamada <ASE name>.appserviceenvironment.net
1. crie um registro A nessa zona que aponte * para o endereço IP do ILB
1. crie um registro A nessa zona que aponte @ para o endereço IP do ILB
1. crie um registro A nessa zona que aponte *.scm para o endereço IP do ILB

As configurações de DNS para o sufixo de domínio padrão do ASE não restringem seus aplicativos a serem acessíveis apenas por esses nomes. Você pode definir um nome de domínio personalizado sem qualquer validação em seus aplicativos em um ASE. Se você quiser criar uma zona chamada *contoso.net*, poderá fazer isso e apontar para o endereço IP de entrada. O nome de domínio personalizado funciona para solicitações de aplicativo, mas não para o site do scm. O site do SCM está disponível somente em *&lt; AppName &gt; . SCM. &lt; asename &gt; . appserviceenvironment.net*. 

## <a name="publishing"></a>Publicando

Em um ASE, assim como no serviço de aplicativo multilocatário, você pode publicar por estes métodos:

- Implantação da Web
- CI (Integração contínua)
- Operação de arrastar e soltar no console do Kudu
- Um IDE, como Visual Studio, Eclipse ou IntelliJ IDEA

Com um ASE, os pontos de extremidade de publicação só estão disponíveis por meio do endereço de entrada usado pelo Endpoint privado. Se você não tiver acesso à rede para o endereço do ponto de extremidade privado, não poderá publicar nenhum aplicativo nesse ASE.  Seus IDEs também devem ter acesso à rede para o ILB para publicar diretamente nele.

Sem alterações adicionais, os sistemas de CI baseados na Internet, como GitHub e Azure DevOps, não funcionam com um ASE ILB porque o ponto de extremidade de publicação não é acessível pela Internet. Você pode habilitar a publicação em um ASE ILB do Azure DevOps instalando um agente de liberação auto-hospedado na rede virtual que contém o ASE ILB. 

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

Se você se integrar com o Log Analytics, poderá ver os logs selecionando **logs** no portal do ase e criando uma consulta no **AppServiceEnvironmentPlatformLogs**. Os logs são emitidos somente quando o ASE tem um evento que irá dispará-lo. Se o seu ASE não tiver tal evento, não haverá nenhum log. Para ver rapidamente um exemplo de logs em seu espaço de trabalho do Log Analytics, execute uma operação de dimensionamento com um dos planos do serviço de aplicativo em seu ASE. Em seguida, você pode executar uma consulta em **AppServiceEnvironmentPlatformLogs** para ver esses logs. 

**Criando um alerta**

Para criar um alerta em seus logs, siga as instruções em [criar, exibir e gerenciar alertas de log usando Azure monitor](../../azure-monitor/alerts/alerts-log.md). Em resumo:

* Abra a página alertas em seu portal do ASE
* Selecionar **nova regra de alerta**
* Selecione seu recurso para ser seu espaço de trabalho do Log Analytics
* Defina sua condição com uma pesquisa de logs personalizada para usar uma consulta como "AppServiceEnvironmentPlatformLogs | onde ResultDescription contém "tem começado a ser dimensionado" ou o que você desejar. Defina o limite conforme apropriado. 
* Adicione ou crie um grupo de ação conforme desejado. O grupo de ações é onde você define a resposta para o alerta, como enviar um email ou uma mensagem SMS
* Nomeie o alerta e salve-o.

## <a name="internal-encryption"></a>Criptografia interna

O Ambiente do Serviço de Aplicativo funciona como um sistema de caixa preta em que você não pode ver os componentes internos nem a comunicação dentro do sistema. Para habilitar uma taxa de transferência mais alta, a criptografia não é habilitada por padrão entre os componentes internos. O sistema é seguro, pois o tráfego está totalmente inacessível para ser monitorado ou acessado. Se você tiver um requisito de conformidade que exija a criptografia completa do caminho de dados da criptografia de ponta a ponta, você poderá habilitá-lo na interface do usuário de **configuração** do ase.

![Habilitar criptografia interna][5]

Isso vai criptografar o tráfego de rede interno em seu ASE entre os front-ends e as funções de trabalho, criptografar o arquivo de paginação e criptografar os discos das funções de trabalho. Depois que o InternalEncryption clusterSetting estiver habilitado, o desempenho do sistema poderá ser afetado. Ao fazer a alteração para habilitar InternalEncryption, seu ASE estará em um estado instável até que a alteração seja totalmente propagada. A propagação completa da alteração pode levar algumas horas para ser concluída, dependendo de quantas instâncias você tem em seu ASE. É altamente recomendável não habilitar isso em um ASE enquanto ele estiver em uso. Se você precisa habilitá-lo em um ASE usado ativamente, recomendamos desviar o tráfego para um ambiente de backup até o fim da operação.

## <a name="upgrade-preference"></a>Preferência de atualização

Se você tiver vários ASEs, talvez queira que alguns ASEs sejam atualizados antes de outros. No objeto do **Gerenciador de recursos de HostingEnvironment** do ase, você pode definir um valor para **upgradePreference**. A configuração **upgradePreference** pode ser configurada usando um modelo, ARMClient ou https://resources.azure.com . Os três valores possíveis são:

- **Nenhum**: o Azure atualizará seu ASE em nenhum lote específico. Esse valor é o padrão.
- **Antecipadamente**: seu ase será atualizado na primeira metade das atualizações do serviço de aplicativo.
- **Tarde**: seu ase será atualizado na segunda metade das atualizações do serviço de aplicativo.

Para configurar sua preferência de atualização, vá para a interface do usuário de **configuração** do ase. 

O recurso **upgradePreferences** faz mais sentido quando você tem vários ases porque seu ases "antecipado" será atualizado antes de seu "final" ases. Quando você tiver vários ASEs, deverá definir seu ASEs de desenvolvimento e teste como "antecipado" e sua ASEs de produção como "tardia".

## <a name="delete-an-ase"></a>Excluir um ASE

Para excluir um ASE:

1. Selecione **excluir** na parte superior do painel de **ambiente do serviço de aplicativo** .

1. Insira o nome do seu ASE para confirmar que deseja excluí-lo. Ao excluir um ASE, você também exclui todo o conteúdo dentro dele.

    ![Exclusão de ASE][3]

1. Selecione **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
