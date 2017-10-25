---
title: Criar o Integration Runtime auto-hospedado no Azure Data Factory | Microsoft Docs
description: Saiba como criar o Integration Runtime auto-hospedado no Azure Data Factory, que permite que os data factories acessem armazenamentos de dados em uma rede privada.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 18f5aea960bca34699d2d265d4801797291a3e3a
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Como criar e configurar o Integration Runtime do auto-hospedado
O IR (Integration Runtime) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer funcionalidades de integração de dados entre diferentes ambientes de rede. Para obter detalhes sobre o IR, consulte [Visão geral do Integration Runtime](concepts-integration-runtime.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory versão 1](v1/data-factory-introduction.md).

Um Integration Runtime auto-hospedado é capaz de executar as atividades de cópia entre armazenamentos de dados de nuvem e um armazenamento de dados na rede privada e expedir atividades de transformação em recursos de computação em uma Rede Virtual do Azure ou local. A instalação do Integration Runtime auto-hospedado precisa de uma máquina virtual ou de um computador local dentro de uma rede privada.  

Este documento apresenta como você pode criar e configurar o IR auto-hospedado.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Etapas de alto nível para instalar o IR auto-hospedado
1.  Crie um Integration Runtime auto-hospedado. Aqui está um exemplo do PowerShell:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  Baixe e instale o Integration Runtime auto-hospedado (no computador local).
3.  Recupere a chave de autenticação e registre o Integration Runtime auto-hospedado com a chave. Aqui está um exemplo do PowerShell:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados
Quando você move os dados entre a nuvem e o local, a atividade usa um Integration Runtime auto-hospedado para transferir os dados da fonte de dados local para a nuvem e vice-versa.

Aqui está o fluxo de dados de alto nível para e o resumo das etapas para a cópia com o IR auto-hospedado:

![Visão geral de alto nível](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. O desenvolvedor de dados cria um Integration Runtime auto-hospedado dentro de um Azure Data Factory usando o cmdlet do PowerShell. Atualmente, o Portal do Azure não dá suporte a esse recurso.
2. O desenvolvedor de dados cria um serviço vinculado para um armazenamento de dados local especificando a instância de Integration Runtime auto-hospedado que ele deve usar para se conectar a armazenamentos de dados. Como parte da configuração do serviço vinculado, o desenvolvedor de dados usa o aplicativo ‘Gerenciador de Credenciais’ (sem suporte no momento) para configurar as credenciais e os tipos de autenticação. A caixa de diálogo do aplicativo Gerenciador de Credenciais se comunica com o armazenamento de dados para testar a conexão e o Integration Runtime auto-hospedado para salvar as credencias.
4.  O nó do Integration Runtime auto-hospedado criptografa a credencial usando a DPAPI (Interface de Programação do Aplicativo de Proteção de Dados) do Windows e a salva localmente. Se vários nós forem definidos para alta disponibilidade, as credenciais serão mais sincronizadas em outros nós. Cada nó a criptografa usando DPAPI e a armazena localmente. A sincronização de credenciais é transparente para o desenvolvedor de dados e é tratada pelo IR auto-hospedado.    
5.  O serviço Data Factory se comunica com o Integration Runtime auto-hospedado para o agendamento e o gerenciamento de trabalhos por meio de um **canal de controle** que usa uma fila do Barramento de Serviço do Azure compartilhado. Quando um trabalho de atividade precisa ser executado, o Data Factory enfileira a solicitação juntamente com quaisquer informações de credenciais (no caso de as credenciais ainda não estarem armazenadas no Integration Runtime auto-hospedado). O Integration Runtime auto-hospedado inicia o trabalho depois da sondagem da fila.
6.  O Integration Runtime auto-hospedado copia dados de um repositório local para um armazenamento na nuvem, ou vice-versa, dependendo de como a atividade de cópia estiver configurada no pipeline de dados. Para esta etapa, o Integration Runtime auto-hospedado se comunica diretamente com os serviços de armazenamento baseado na nuvem, como Armazenamento de Blobs do Azure por um canal seguro (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Considerações para o uso do IR auto-hospedado

- Um único Integration Runtime auto-hospedado pode ser usado para várias fontes de dados locais. No entanto, **uma única instância do Integration Runtime auto-hospedado é vinculada apenas a um Azure Data Factory** e não pode ser compartilhada com outro Data Factory.
- Você pode ter **apenas uma instância do Integration Runtime auto-hospedado** instalada em um único computador. Supondo que você tenha dois data factories que precisam acessar fontes de dados locais, você precisará instalar o Integration Runtime auto-hospedado em dois computadores locais. Em outras palavras, um Integration Runtime auto-hospedado é associado a um data factory específico
- O **Integration Runtime auto-hospedado não precisa estar no mesmo computador que a fonte de dados**. No entanto, ter o Integration Runtime auto-hospedado mais perto da fonte de dados reduz o tempo para o Integration Runtime auto-hospedado se conectar à fonte de dados. É recomendável instalar o Integration Runtime auto-hospedado em um computador que seja diferente daquele que hospeda a fonte de dados local. Quando a fonte de dados e o Integration Runtime auto-hospedado estão em computadores diferentes, o Integration Runtime auto-hospedado não compete por recursos com a fonte de dados.
- Você pode ter **vários tempos de execução de integração auto-hospedados em diferentes computadores conectados à mesma fonte de dados local**. Por exemplo, você pode ter dois tempos de execução de integração auto-hospedados servindo dois data factories, mas a mesma fonte de dados local é registrada com ambos os data factories.
- Se você já tiver um gateway instalado no computador atendendo um cenário do **Power BI**, instale um **Integration Runtime auto-hospedado separado para o Azure Data Factory** em outro computador.
- O Integration Runtime auto-hospedado deve ser usado para dar suporte à integração de dados na Rede Virtual do Azure.
- Trate a fonte de dados como local (isto é, protegida por um firewall) mesmo quando você usar o **ExpressRoute**. Use o Integration Runtime auto-hospedado para estabelecer a conectividade entre o serviço e a fonte de dados.
- Você deverá usar o Integration Runtime auto-hospedado mesmo se o armazenamento de dados estiver na nuvem em uma **máquina virtual de IaaS do Azure**.

## <a name="prerequisites"></a>Pré-requisitos

- As versões de **Sistema Operacional** com suporte são Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. A instalação do Integration Runtime auto-hospedado em um **controlador de domínio não tem suporte**.
- O **.NET Framework 4.6.1 ou superior** é necessário. Se você estiver instalando o Integration Runtime auto-hospedado em um computador com Windows 7, instale o .NET Framework 4.6.1 ou posterior. Confira [Requisitos de sistema do .NET Framework](/dotnet/framework/get-started/system-requirements) para obter detalhes.
- A **configuração** recomendada para o computador do Integration Runtime auto-hospedado é de, no mínimo, 2 GHz, quatro núcleos, 8 GB de RAM e 80 GB de disco.
- Se o computador host hibernar, o Integration Runtime auto-hospedado não responderá às solicitações de dados. Portanto, configure um plano de energia adequado no computador antes de instalar o Integration Runtime auto-hospedado. Se o computador estiver configurado para hibernar, a instalação do Integration Runtime auto-hospedado exibirá uma mensagem.
- Você deve ser um administrador no computador local para instalar e configurar com êxito o Integration Runtime auto-hospedado.
- Como as execuções da atividade de cópia ocorrem em uma frequência específica, o uso de recursos (CPU, memória) no computador também segue o mesmo padrão com tempos ociosos e de pico. A utilização de recursos também depende muito da quantidade de dados sendo movida. Quando vários trabalhos de cópia estiverem em andamento, você verá o uso do recurso aumentar durante horários de pico.

## <a name="installation-best-practices"></a>Melhores práticas de instalação
O Integration Runtime auto-hospedado pode ser instalado baixando um pacote de instalação MSI do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Veja o artigo [Mover dados entre locais e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure o plano de energia no computador host para o Integration Runtime auto-hospedado para que o computador não hiberne. Se o computador host hibernar, o Integration Runtime auto-hospedado ficará offline.
- Faça backup das credenciais associadas ao Integration Runtime auto-hospedado regularmente.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Instalar e registrar o IR auto-hospedado do centro de download

1. Navegue até a [página de download do Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
2. Clique em **Baixar**, selecione a versão apropriada (**32 bits** v. **64 bits**) e clique em **Avançar**.
3. Execute o **MSI** diretamente ou salve-o em seu disco rígido e execute-o.
4. Na página de **Boas-vindas**, selecione um **idioma** e clique em **Avançar**.
5. **Aceite** os Termos de Licença e clique em **Avançar**.
6. Selecione **pasta** para instalar o Integration Runtime auto-hospedado e clique em **Avançar**.
7. Na página **Pronto para instalar**, clique em **Instalar**.
8. Clique em **Concluir** para finalizar a instalação.
9. Obtenha a chave de autenticação usando o Azure PowerShell. Exemplo do PowerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. Na página **Registrar Integration Runtime (Auto-hospedado)** do Gerenciador de Configuração do Microsoft Integration Runtime em seu computador, execute as seguintes etapas:
    1. Cole a **chave de autenticação** na área de texto.
    2. Opcionalmente, clique em **Mostrar chave de autenticação** para ver o texto da chave.
    3. Clique em **Registrar**.


## <a name="high-availability-and-scalability"></a>Alta disponibilidade e escalabilidade
Um Integration Runtime auto-hospedado pode ser associado a vários computadores locais. Esses computadores são chamados de nós. Você pode ter até quatro nós associados a um Integration Runtime auto-hospedado. Os benefícios de ter vários nós (computadores locais com o gateway instalado) para um gateway lógico são:
1. Disponibilidade superior do Integration Runtime auto-hospedado para que ele não seja o único ponto de falha na sua solução de Big Data ou integração de dados de nuvem com o Azure Data Factory, garantindo a continuidade com até quatro nós.
2. Desempenho e taxa de transferência aprimorados durante a movimentação de dados entre os armazenamentos de dados de nuvem e locais. Obtenha mais informações sobre [comparações de desempenho](copy-activity-performance.md).

Você pode associar vários nós simplesmente instalando o software de Integration Runtime auto-hospedado do [Centro de download](https://www.microsoft.com/download/details.aspx?id=39717) e registrando-o por uma das Chaves de Autenticação obtidas do cmdlet New-AzureRmDataFactoryV2IntegrationRuntimeKey conforme descrito no [Tutorial](tutorial-hybrid-copy-powershell.md)

> [!NOTE]
> Você não precisa criar um novo Integration Runtime auto-hospedado para associar cada nó.

## <a name="system-tray-icons-notifications"></a>Ícones/notificações da bandeja do sistema
Se você mover o cursor sobre o ícone de bandeja do sistema/mensagem de notificação, poderá encontrar detalhes sobre o estado do Integration Runtime auto-hospedado.

![Notificações da bandeja do sistema](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Portas e firewall
Há dois firewalls que você precisa levar em consideração: o **firewall corporativo** em execução no roteador central da organização e o **Firewall do Windows** configurado como um daemon no computador local em que o Integration Runtime está instalado.

![Firewall](media\create-self-hosted-integration-runtime\firewall.png)

No nível do **firewall corporativo**, você precisa configurar os seguintes domínios e portas de saída:

Nomes de domínio | Portas | Descrição
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | Usado para comunicação com o back-end do Serviço de Movimentação de Dados
*.core.windows.net | 443 | Usado para cópia em etapas usando Blobs do Azure (se estiver configurado)
*.frontend.clouddatahub.net | 443 | Usado para comunicação com o back-end do Serviço de Movimentação de Dados

No nível do **Firewall do Windows** (nível do computador), essas portas de saída normalmente são habilitadas. Se não forem, você poderá configurar as portas e os domínios adequadamente no computador do Integration Runtime auto-hospedado.

> [!NOTE]
> Com base em sua origem/coletores, talvez você precise incluir domínios adicionais na lista de permissões e as portas de saída em seu Firewall do Windows/corporativo.
>
> Para alguns bancos de dados na nuvem (por exemplo: Banco de Dados SQL do Azure, Azure Data Lake etc.), talvez seja necessário incluir o endereço IP do computador do Integration Runtime auto-hospedado na lista de permissões na configuração do firewall deles.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma origem para um coletor
Verifique se as regras de firewall estão habilitadas corretamente no firewall corporativo, no Firewall do Windows no computador do Integration Runtime auto-hospedado e no próprio armazenamento de dados. Habilitar essas regras permite ao Integration Runtime auto-hospedado se conectar com êxito à origem e ao coletor. Habilite as regras para cada repositório de dados que esteja envolvido na operação de cópia.

Por exemplo, para copiar de **um armazenamento de dados local para um coletor do Banco de Dados SQL do Azure ou um coletor do SQL Data Warehouse do Azure**, siga as etapas abaixo:

- Permitir a comunicação **TCP** de saída na porta **1433** para o firewall do Windows e o firewall corporativo.
- Configurar as definições de firewall do SQL Server do Azure para adicionar o endereço IP do computador do Integration Runtime auto-hospedado à lista de endereços IP permitidos.

> [!NOTE]
> Se o firewall não permitir a porta de saída 1433, o Integration Runtime auto-hospedado não poderá acessar diretamente o Azure SQL. Nesse caso, use [Cópia em Etapas](copy-activity-performance.md) para o Banco de Dados SQL do Azure/DW SQL do Azure. Neste cenário, você exigiria apenas HTTPS (porta 443) para a movimentação de dados.


## <a name="proxy-server-considerations"></a>Considerações do servidor proxy
Se o ambiente de rede corporativo usar um servidor proxy para acessar a Internet, configure o Integration Runtime auto-hospedado para usar as definições de proxy apropriadas. Você pode definir o proxy durante a fase de registro inicial.

![Especificar o proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

O Integration Runtime auto-hospedado usa o servidor proxy para se conectar ao serviço de nuvem. Clique no link Alterar durante a configuração inicial. Você verá a caixa de diálogo de configuração de proxy.

![Configurar proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Há três opções de configuração:

- **Não usar proxy**: o Integration Runtime auto-hospedado não usa explicitamente qualquer proxy para se conectar aos serviços de nuvem.
- **Usar proxy do sistema**: o Integration Runtime auto-hospedado usa a configuração de proxy que é definida em diahost.exe.config e em diawp.exe.config. Se nenhum proxy estiver configurado em diahost.exe.config e em diawp.exe.config, o Integration Runtime auto-hospedado se conectará ao serviço de nuvem diretamente sem passar pelo proxy.
- **Usar proxy personalizado**: configure a definição do proxy HTTP a ser usado pelo Integration Runtime auto-hospedado, em vez de usar as configurações em diahost.exe.config e diawp.exe.config. Endereço e porta são necessários. O Nome de Usuário e a Senha são opcionais, dependendo da configuração de autenticação do seu proxy. Todas as configurações são criptografadas com a DPAPI do Windows no Integration Runtime auto-hospedado e armazenados localmente no computador.

O Serviço de Host do Integration Runtime é reiniciado automaticamente depois que você salva as configurações de proxy atualizadas.

Depois que o Integration Runtime auto-hospedado tiver sido registrado com êxito, se você quiser exibir ou atualizar as configurações de proxy, use o Gerenciador de Configurações do Integration Runtime.

1.  Inicie o **Gerenciador de Configuração do Integration Runtime da Microsoft**.
2.  Alterne para a guia **Configurações** .
3.  Clique no link **Alterar** na seção **Proxy HTTP** para iniciar a caixa de diálogo **Configurar Proxy HTTP**.
4.  Depois de clicar no botão **Avançar**, você verá uma caixa de diálogo de aviso solicitando sua permissão para salvar a configuração de proxy e reiniciar o Serviço de Host do Integration Runtime.

Você pode exibir e atualizar o proxy HTTP usando a ferramenta Gerenciador de Configurações.

![Exibir proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Se você configurar um servidor proxy com autenticação NTLM, o Serviço de Host do Integration Runtime será executado sob a conta de domínio. Se você alterar a senha da conta de domínio posteriormente, lembre-se de atualizar as definições de configuração para o serviço e reiniciá-lo adequadamente. Por conta desse requisito, sugerimos o uso de uma conta de domínio dedicada para acessar o servidor proxy que não exija a atualização da senha com frequência.

### <a name="configure-proxy-server-settings"></a>Definir configurações do servidor proxy

Se você escolher a configuração **Usar proxy do sistema** para o proxy HTTP, o Integration Runtime auto-hospedado usará a configuração de proxy em diahost.exe.config e diawp.exe.config. Se nenhum proxy estiver especificado em diahost.exe.config e em diawp.exe.config, o Integration Runtime auto-hospedado se conectará ao serviço de nuvem diretamente sem passar pelo proxy. O procedimento a seguir fornece instruções para atualizar o arquivo diahost.exe.config.

1. No Explorador de Arquivos, faça uma cópia de segurança de C:\Arquivos de Programas\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config para fazer backup do arquivo original.
2. Inicie o Notepad.exe executando como administrador e abra o arquivo de texto “C:\Arquivos de Programas\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config”. Você pode encontrar a marcação padrão para system.net conforme mostrado no código abaixo:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Em seguida, você pode adicionar detalhes do servidor proxy, conforme mostrado no exemplo a seguir:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Propriedades adicionais são permitidas dentro da marca de proxy para especificar as configurações necessárias como scriptLocation. Confira [Elemento proxy (Configurações de Rede)](https://msdn.microsoft.com/library/sa91de1e.aspx) para encontrar a sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Salve o arquivo de configuração no local original e reinicie o Serviço de Host do Integration Runtime auto-hospedado, que assimila as alterações. Para reiniciar o serviço: use o miniaplicativo de serviços no painel de controle ou no **Gerenciador de Configuração do Integration Runtime** > clique no botão **Parar Serviço** e depois em **Iniciar Serviço**. Se o serviço não iniciar, é provável que uma sintaxe de marca XML incorreta tenha sido adicionada ao arquivo de configuração de aplicativo que foi editado.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Além dos desses pontos, você também precisa verificar se o Microsoft Azure está lista de autorizados da sua empresa. Baixe a lista de endereços IP válidos do Microsoft Azure no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Possíveis sintomas de problemas relacionados ao firewall e ao servidor proxy
Se você encontrar erros similares aos descritos a seguir, eles provavelmente se deverão à configuração incorreta do servidor proxy ou firewall, que impedirá o Integration Runtime auto-hospedado de se conectar ao Data Factory para se autenticar. Confira a seção anterior para garantir que seu firewall e servidor proxy estejam configurados corretamente.

1.  Quando você tenta registrar o Integration Runtime auto-hospedado, recebe o seguinte erro: “Falha ao registrar esse nó do Integration Runtime. Confirme se a chave de autenticação é válida e o serviço de host de serviço de integração está em execução neste computador. "
2.  Ao abrir o Gerenciador de Configurações do Integration Runtime, você vê o status "**Desconectado"** ou "**Conectando**". Ao exibir os logs de eventos do Windows, em "Visualizador de Eventos" > "Logs de Aplicativos e Serviços" > "Microsoft Integration Runtime", você vê mensagens de erro como as do seguinte:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="open-port-8060-for-credential-encryption"></a>Abrir a porta 8060 para criptografia de credencial
O aplicativo **Definindo Credenciais** (sem suporte no momento) usa a porta de entrada 8060 para retransmitir credenciais ao Integration Runtime auto-hospedado quando você configura um serviço vinculado local no Portal do Azure. Durante a instalação do Integration Runtime auto-hospedado, por padrão, a instalação do Integration Runtime auto-hospedado o abre no computador do Integration Runtime auto-hospedado.

Se estiver usando um firewall de terceiros, você poderá abrir manualmente a porta 8050. Se tiver problemas de firewall durante a configuração do Integration Runtime auto-hospedado, você poderá tentar usar o comando a seguir para instalar o Integration Runtime auto-hospedado sem configurar o firewall.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8060 no computador do Integration Runtime auto-hospedado, use mecanismos diferentes do aplicativo **Definindo Credenciais** para configurar as credenciais do armazenamento de dados. Por exemplo, você pode usar o cmdlet do PowerShell New-AzureRmDataFactoryV2LinkedServiceEncryptCredential. Confira a seção Definir credenciais e segurança para saber como as credenciais do armazenamento de dados podem ser definidas.


## <a name="next-steps"></a>Próximas etapas
Consulte o tutorial a seguir para obter instruções passo a passo: [Tutorial: cópia de dados locais para a nuvem](tutorial-hybrid-copy-powershell.md).

