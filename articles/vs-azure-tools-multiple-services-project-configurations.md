---
title: "Configurando seu projeto do Azure usando várias configurações de serviço | Microsoft Docs"
description: "Saiba como configurar um projeto de serviço de nuvem do Azure alterando os arquivos Servicedefinition e ServiceConfiguration."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: deb69101e855bcad56b9212736c52ace72631f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configurando seu projeto do Azure usando várias configurações de serviço
Um projeto de serviço de nuvem do Azure inclui dois arquivos de configuração: ServiceDefinition.csdef e ServiceConfiguration.cscfg. Esses arquivos são empacotados com o aplicativo de serviço de nuvem do Azure e implantados no Azure.

* O arquivo **ServiceDefinition.csdef** contém os metadados exigidos pelo ambiente do Azure para os requisitos do seu aplicativo de serviço de nuvem, inclusive as funções que ele contém. Esse arquivo também contém configurações que se aplicam a todas as instâncias. Essas definições de configurações podem ser lidas em tempo de execução usando a API de tempo de execução de hospedagem de serviços do Azure. Esse arquivo não pode ser atualizado enquanto o serviço está em execução no Azure.
* O arquivo **ServiceConfiguration.cscfg** estabelece valores para as configurações definidas no arquivo de definição de serviço e especifica o número de instâncias a serem executadas para cada função. Esse arquivo pode ser atualizado enquanto o serviço de nuvem está em execução no Azure.

As ferramentas do Azure para Microsoft Visual Studio fornecem páginas de propriedades que você pode usar para definir as configurações armazenadas nesses arquivos. Para acessar as páginas de propriedades, clique duas vezes na referência de função, sob o projeto de serviço de nuvem do Azure no Gerenciador de Soluções, ou clique com o botão direito do mouse na referência da função e escolha **Propriedades**, conforme mostrado na figura a seguir.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Para obter informações sobre os esquemas subjacentes para a definição e os arquivos de configuração do serviço, consulte a [Referência do esquema](https://msdn.microsoft.com/library/azure/dd179398.aspx). Para saber mais sobre a configuração de serviço, veja [Como configurar Serviços de Nuvem](cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configurando propriedades da função
As páginas de propriedades de uma função web e uma função de trabalho são semelhantes, embora existam algumas diferenças, indicadas nas seções a seguir.

Na página **Cache** , você pode configurar os serviços de cache do Azure.

### <a name="configuration-page"></a>Página Configuração
Na página de **Configuração** , você pode definir essas propriedades:

**Instâncias**

Definir a **instância** propriedade de contagem para o número de instâncias o serviço deve ser executado para esta função.

Defina a propriedade **Tamanho da VM** como **Extra Pequena**, **Pequena**, **Média**, **Grande** ou **Extra Grande**.  Para saber mais, veja [Tamanhos dos Serviços de Nuvem](cloud-services/cloud-services-sizes-specs.md).

**Ação de inicialização** (somente função web)

Defina essa propriedade para especificar que o Visual Studio deve iniciar um navegador da Web para os pontos de extremidade HTTP ou os pontos de extremidade HTTPS, ou ambos, quando você iniciar a depuração.

A opção de ponto de extremidade HTTPS está disponível somente se você já definiu um ponto de extremidade HTTPS para sua função. Você pode definir um ponto de extremidade HTTPS na página de propriedades **pontos de extremidade** .

Se você já tiver adicionado um ponto de extremidade HTTPS, a opção de ponto de extremidade HTTPS é habilitada por padrão e o Visual Studio iniciará um navegador para esse ponto de extremidade quando você iniciar a depuração, além de um navegador para o ponto de extremidade HTTP. Isso pressupõe que ambas as opções de inicialização estão habilitadas.

**Diagnostics**

Por padrão, o diagnóstico é habilitado para a função web. A conta de armazenamento e o projeto de serviço de nuvem do Azure são definidos para usar o emulador de armazenamento local. Quando você estiver pronto para implantar no Azure, selecione o botão de construtor (**...**) para atualizar a conta de armazenamento para que ela use o armazenamento do Azure na nuvem. Você pode transferir os dados de diagnóstico para a conta de armazenamento sob demanda ou em intervalos agendados automaticamente. Para saber mais sobre o diagnóstico do Azure, veja [Habilitando o Diagnóstico nos Serviços de Nuvem do Azure e nas Máquinas Virtuais](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Página Configurações
Na página **Configurações** , você pode adicionar parâmetros de configuração para o serviço. Definições de configuração são pares nome-valor. O código em execução na função pode ler os valores das suas configurações em tempo de execução usando classes fornecidas pelo [Biblioteca Gerenciada do Azure](http://go.microsoft.com/fwlink?LinkID=171026). Especificamente, o método [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) retorna o valor de uma definição de configuração nomeada em tempo de execução.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configurando uma cadeia de conexão para uma conta de armazenamento
Uma cadeia de conexão é uma definição de configuração que fornece informações de conexão e autenticação para o emulador de armazenamento ou para uma conta de armazenamento do Azure. Sempre que o código precisar acessar dados de serviços de armazenamento do Azure – ou seja, blob, fila ou dados da tabela – do código em execução em uma função, você precisará definir uma cadeia de conexão para essa conta de armazenamento.

Uma cadeia de conexão que aponta para uma conta de armazenamento do Azure deve usar um formato definido. Para obter informações sobre como criar cadeias de conexão, veja [Configurar cadeias de conexão do Armazenamento do Azure](storage/common/storage-configure-connection-string.md).

Quando você estiver pronto para testar seu serviço em relação aos serviços de armazenamento do Azure, ou quando você estiver pronto para implantar seu serviço de nuvem no Azure, pode alterar o valor de qualquer cadeia de conexão para apontar para sua conta de armazenamento do Azure. Escolha (**...**) e **Inserir credenciais da conta de armazenamento**. Insira as informações de sua conta que incluem o nome da conta e a chave de conta. Na caixa de diálogo **Cadeia de conexão da conta de armazenamento** , você também pode indicar se deseja usar os pontos de extremidade HTTPS padrão (a opção padrão), os pontos de extremidade HTTP padrão ou pontos de extremidade personalizados. Você pode decidir usar pontos de extremidade personalizados se registrou um nome de domínio personalizado para seu serviço, conforme descrito em [Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs](storage/blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Você deve modificar as cadeias de conexão para apontar para uma conta de armazenamento do Azure antes de implantar seu serviço. A falha em fazer isso pode fazer com que sua função não inicie, ou que ela alterne entre os estados inicializando, ocupado e parando.
> 
> 

## <a name="endpoints-page"></a>Página Pontos de Extremidade
Uma função de trabalho pode ter qualquer número de pontos de extremidade HTTP, HTTPS ou TCP. Pontos de extremidade podem ser pontos de extremidade de entrada, disponíveis a clientes externos, ou pontos de extremidade internos, disponíveis a outras funções em execução no serviço.

* Para disponibilizar um ponto de extremidade HTTP para clientes externos e navegadores da Web, altere o tipo de ponto de extremidade para entrada e especifique um nome e um número da porta pública.
* Para disponibilizar um ponto de extremidade HTTPS para clientes externos e navegadores da Web, altere o tipo de ponto de extremidade para **entrada**, e especifique um nome, um número da porta pública e um nome de certificado de gerenciamento.
  
    Observe que, antes de especificar um certificado de gerenciamento, você deve definir o certificado na página de propriedades **Certificados** .
* Para disponibilizar um ponto de extremidade para o acesso interno por outras funções no serviço de nuvem, altere o tipo de ponto de extremidade para interno e especifique um nome e possíveis portas privadas para esse ponto de extremidade.

## <a name="local-storage-page"></a>Página Armazenamento Local
Você pode usar a página de propriedades **Armazenamento Local** para reservar um ou mais recursos de armazenamento local para uma função. Um recurso de armazenamento local é um diretório reservado no sistema de arquivos da máquina virtual do Azure no qual uma instância de uma função está em execução.

## <a name="certificates-page"></a>Página Certificados
Na página **Certificados** , você pode associar certificados à sua função. Os certificados que você adicionar podem ser usados para configurar os pontos de extremidade HTTPS na página de propriedades **Pontos de Extremidade** .

A página de propriedades **Certificados** adiciona informações sobre seus certificados para a configuração do serviço. Observe que os certificados não vêm com seu serviço; você deve carregá-los separadamente no Azure por meio do [portal clássico do Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Para associar um certificado à sua função, forneça um nome para o certificado. Use esse nome para fazer referência ao certificado ao configurar um ponto de extremidade HTTPS na página de propriedades **Pontos de Extremidade** . Em seguida, especifique se o repositório de certificados é **Computador Local** ou **Usuário Atual** e o nome do repositório. Finalmente, insira a impressão digital do certificado. Se o certificado estiver no repositório Usuário Atual\Pessoal (Meu), você pode inserir a impressão digital do certificado, selecionando o certificado em uma lista preenchida. Se ele residir em qualquer outro local, insira o valor de impressão digital manualmente.

Quando você adiciona um certificado do repositório de certificados, todos os certificados intermediários são adicionados automaticamente às definições de configuração para você. Esses certificados intermediários também devem ser carregados no Azure para configurar corretamente o serviço para SSL.

Qualquer certificado de gerenciamento que você associar a seu serviço só se aplicará a esse serviço quando ele estiver em execução na nuvem. Quando o serviço é executado no ambiente de desenvolvimento local, ele usa um certificado padrão que é gerenciado pelo emulador de computação.

## <a name="configuring-the-azure-cloud-service-project"></a>Configurando o projeto de serviço de nuvem do Azure
Para definir configurações que se aplicam a todo um projeto de serviço de nuvem do Azure, você primeiro abre o menu de atalho para esse nó do projeto e depois escolhe Propriedades para abrir as páginas de propriedades. A tabela a seguir mostra essas páginas de propriedades.

| Página de Propriedades | Descrição |
| --- | --- |
| Aplicativo |Nessa página, você pode exibir informações sobre a versão das ferramentas do Azure que este projeto de serviço de nuvem usa e pode atualizar para a versão atual das ferramentas. |
| Eventos de compilação |Nessa página, você pode configurar eventos de pré e pós-compilação. |
| Desenvolvimento |Nessa página, você pode especificar instruções de configuração da compilação e as condições sob as quais eventos de pós-compilação são executados. |
| Web |Nessa página, você pode definir as configurações relacionadas ao servidor Web. |

