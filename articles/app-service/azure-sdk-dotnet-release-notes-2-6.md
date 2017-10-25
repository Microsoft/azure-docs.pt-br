---
title: "Notas de versão SDK do Azure para .NET 2.6"
description: "Notas de versão SDK do Azure para .NET 2.6"
services: app-service/web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: b45853d5-a2b8-4962-a22d-579cb36ae14c
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 21817b09440fc98a54dc45c9129d104b01fa387d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-26-release-notes"></a>Notas de versão SDK do Azure para .NET 2.6
Este documento contém as notas de versão do SDK do Azure para .NET 2.6. 

Com o SDK do Azure 2.6, você pode desenvolver aplicativos de serviço de nuvem (PaaS) visando o .NET 4.5.2 ou .NET 4.6 desde que você instale manualmente o .NET Framework de destino na Função do Serviço de Nuvem. Consulte [Install .NET on a Cloud Service Role (Instalar o .NET em uma Função do Serviço de Nuvem)](http://go.microsoft.com/fwlink/?LinkID=309796).

## <a name="service-bus-updates"></a>Atualizações do Barramento de Serviço
* Hubs de Eventos: 
  
  * Agora permite o controle de acesso direcionado ao enviar eventos expondo o ponto de extremidade do editor adicional para Hubs de Eventos.
  * Mais estabilidade e aperfeiçoamento adicionados ao recurso de Hubs de Eventos.
  * Adição do suporte do protocolo Amqp sobre WebSocket para sistemas de mensagens e Hubs de Eventos.

## <a name="hdinsight-tools-for-visual-studio-updates"></a>Atualizações das Ferramentas do HDInsight para Visual Studio
* **Aprimoramento do IntelliSense**: sugestão de metadados remotos
  
    Agora as Ferramentas do HDInsight para Visual Studio dão suporte à obtenção de metadados remotos quando você estiver editando o script do Hive. Por exemplo, você pode digitar **SELECT * FROM** e todos os nomes de tabela serão exibidos. Além disso, os nomes das colunas serão mostrados depois de especificar uma tabela.
* **Suporte ao emulador do HDInsight**
  
    Agora as Ferramentas do HDInsight para Visual Studio dão suporte à conexão ao emulador do HDInsight, para que você possa desenvolver seus scripts do Hive localmente sem introduzir nenhum custo e, em seguida, executar esses scripts junto aos clusters do HDInsight. 
  
    Para obter mais informações, consulte [este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).
* **Suporte das Ferramentas do HDInsight para Visual Studio a clusters de Hadoop genéricos** (visualização)
  
    Agora as Ferramentas do HDInsight para Visual Studio dão suporte a clusters de Hadoop genéricos, então você pode usar as Ferramentas do HDInsight para Visual Studio para fazer o seguinte:
  
  * conectar-se ao seu cluster, 
  * criar uma consulta do Hive com o suporte avançado do IntelliSense/preenchimento automático, 
  * exibir todos os trabalhos no seu cluster com uma interface do usuário intuitiva. 
    
    Para obter mais informações, consulte [este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

## <a name="in-role-cache-updates"></a>Atualizações do Cache na Função
* O **Cache na Função** foi atualizado para usar o **SDK do Armazenamento do Microsoft Azure** versão 4.3. Até agora, o **Cache na Função** estava usando o SDK do Armazenamento do Azure versão 1.7.
  
    Os clientes usando o SDK do Azure 2.5 ou abaixo devem atualizar para o SDK do Azure 2.6 e mudar para a nova versão do SDK do Armazenamento do Azure. 
  
    No momento, a versão 2011-08-18 do Armazenamento do Azure está agendada para ser removida em 1º de agosto de 2016. As migrações de Cache na Função do Azure SDK 2.5 ou versão anterior a 2.6 devem ser concluídas antes deste momento. Para saber mais sobre a desativação do armazenamento do Azure versão 2011-08-18, confira [Atualização sobre a remoção de versão do Serviço de Armazenamento do Microsoft Azure: extensão até 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

> [!IMPORTANT]
> Estamos anunciando a desativação de 30 de novembro de 2016, para o Serviço de Cache Gerenciado do Azure e o Cache na Função do Azure. É recomendável que você migre para o Cache Redis do Azure em preparação para essa desativação. Para obter mais informações sobre datas e diretrizes sobre migração, consulte [Qual oferta de Cache do Azure é adequada para mim?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)
> 
> 

## <a name="azure-app-service-tools"></a>Ferramentas do Serviço de Aplicativo do Azure
Os seguintes itens foram atualizados na versão 2.6 do SDK do Azure.

* Publicação do Azure aprimorada para incluir Aplicativos de API (Interface de Programação de Aplicativo) do Azure como um destino de implantação.
* Funcionalidade de provisionamento de Aplicativos de API para habilitar os usuários com a funcionalidade de criação e provisionamento de Aplicativo de API.
* Gerenciador de Servidores alterado para refletir o novo nó de Serviço de Aplicativo, com aplicativos Web, móveis e API agrupados por grupo de recursos.
* Gesto para adicionar o cliente de Aplicativo de API do Azure adicionado na maioria dos projetos C#, o que permitirá a geração automática de Aplicativos de API habilitados para Swagger em execução em uma assinatura do Azure do usuário.
* As ferramentas de Aplicativos de API e os nós de Serviço de Aplicativo no Gerenciador de Servidores estão disponíveis apenas no Visual Studio 2013. 

## <a name="azure-resource-manager-tools-updates"></a>Atualizações das ferramentas do gerenciador de recursos do Azure
As ferramentas do gerenciador de recursos do Azure foram atualizadas para incluir modelos de máquinas virtuais, redes e armazenamento. A experiência de edição do JSON foi atualizada para incluir uma nova exibição de estrutura de tópicos para modelos e a capacidade de editar os modelos usando trechos do JSON. Os modelos implantados do Visual Studio usam um script do PowerShell fornecido com o projeto, portanto, todas as alterações feitas no script serão usadas pelo Visual Studio.

## <a name="diagnostics-improvements-for-cloud-services"></a>Aprimoramentos de diagnóstico para Serviços de Nuvem
O SDK do Azure 2.6 traz de volta o suporte para a coleta de logs de diagnóstico emulador de computação do Azure e a transferência deles para o armazenamento de desenvolvimento. Todos os logs de diagnóstico (incluindo logs de rastreamento de aplicativo, logs de ETW (Rastreamento de Eventos para Windows), contadores de desempenho, logs de infraestrutura e logs de eventos do Windows) gerados quando o aplicativo está em execução no emulador podem ser transferidos para o armazenamento de desenvolvimento para verificar se o log de diagnóstico está funcionando no computador local. 

A conta de armazenamento de diagnóstico agora pode ser especificada no arquivo de configuração do serviço (.cscfg), tornando mais fácil o uso de contas de armazenamento de diagnóstico diferentes para ambientes diferentes. Há algumas diferenças notáveis entre como a cadeia de conexão funcionava no SDK do Azure 2.4 e no SDK do Azure 2.6. Para obter mais informações sobre como usar a cadeia de conexão do Armazenamento de diagnóstico e como isso afeta seus projetos, consulte [Configuring Diagnostics for Azure Cloud Services (Configurando o diagnóstico para Serviços de Nuvem do Azure)](http://go.microsoft.com/fwlink/?LinkID=532784).

## <a name="breaking-changes"></a>Alterações de última hora
### <a name="azure-resource-manager-tools"></a>Ferramentas do gerenciador de recursos do Azure
* O tipo de projeto **Projetos de Implantação de Nuvem** disponível no SDK do Azure 2.5 foi renomeado para **Grupo de Recursos do Azure**.
* **Projetos de Implantação de Nuvem** criado no SDK do Azure 2.5 pode ser usado no 2.6, mas a implantação do modelo no Visual Studio vai falhar. No entanto, a implantação com o script do PowerShell ainda funcionará como anteriormente.  Para obter informações sobre como usar **Projetos de Implantação de Nuvem** na versão 2.6, leia esta [postagem](http://go.microsoft.com/fwlink/?LinkID=534086).

## <a name="known-issues"></a>Problemas conhecidos
* A coleta de logs de diagnóstico no emulador requer um sistema operacional de 64 bits. Os logs de diagnóstico não serão coletados durante a execução em um sistema operacional de 32 bits. Isso não afeta nenhuma outra funcionalidade do emulador. 
* O SDK do Azure 2.6, lançado, em 29/04/2015, tinha dois problemas: 
  
  * Não era possível carregar o aplicativo universal no Visual Studio 2015 quando o SDK do Azure SDK 2.6 era instalado no computador.
  * A depuração de um projeto de serviço de nuvem falhava no Visual Studio 2013 e Visual Studio 2015, e o Visual Studio não responde e falha ao exibir uma caixa de diálogo com a mensagem "Configurando o diagnóstico do emulador".
    
    Uma atualização para o SDK do Azure 2.6 foi lançada em 18/05/2015. A versão atualizada é 2.6.30508.1601; ela contém correções para dois problemas descritos acima. Você pode identificar a compilação do SDK no Painel de Controle -> Programas e Recursos -> Ferramentas do Microsoft Azure para Microsoft Visual Studio 2013 – v 2.6. A coluna Versão exibirá o número da versão.
    
    Se você ainda estiver enfrentando os problemas acima, instale a versão mais recente do SDK do Azure 2.6 para [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

## <a name="see-also"></a>Consulte também
[Informações de suporte e de desativação do SDK do Azure para .NET e APIs](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

