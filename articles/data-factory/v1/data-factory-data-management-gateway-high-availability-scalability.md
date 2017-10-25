---
title: Alta disponibilidade com o Gateway de Gerenciamento de Dados no Azure Data Factory | Microsoft Docs
description: "Este artigo explica como você pode escalar horizontalmente um Gateway de Gerenciamento de Dados adicionando mais nós e escalar verticalmente com o aumento do número de trabalhos simultâneos que podem ser executados em um nó."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1aac856d154724e3dcd282e2d34c27571cd1cb02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Gateway de Gerenciamento de Dados – alta disponibilidade e escalabilidade (versão prévia)
Este artigo lhe ajudará a configurar a solução de alta disponibilidade e escalabilidade com o Gateway de Gerenciamento de Dados.    

> [!NOTE]
> Este artigo pressupõe que você já esteja familiarizado com conceitos básicos do Gateway de Gerenciamento de Dados. Se você não estiver, consulte [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md).

>**Esse recurso em versão prévia é oficialmente compatível com o Gateway de Gerenciamento de Dados versão 2.12.xxxx.x e superior**. Verifique se você está usando a versão 2.12.xxxx.x ou superior. Baixe a versão mais recente do Gateway de Gerenciamento de Dados [aqui](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Visão geral
Você pode associar gateways de gerenciamento de dados instalados em vários computadores locais a um único gateway lógico por meio do portal. Esses computadores são chamados de **nós**. Você pode ter até **quatro nós** associados a um gateway lógico. Os benefícios de ter vários nós (computadores locais com o gateway instalado) para um gateway lógico são:  

- Melhorar o desempenho de movimentação de dados entre armazenamentos de dados local e na nuvem.  
- Se um de nós ficar inativo por algum motivo, outros nós ainda estarão disponíveis para mover os dados. 
- Se um de nós precisar ficar offline para manutenção, outros nós ainda estarão disponíveis para mover os dados.

Você também pode configurar o número de **trabalhos de movimentação de dados simultâneos** que podem ser executados em um nó para escalar verticalmente a capacidade de mover dados entre armazenamentos de dados locais e na nuvem. 

Usando o Portal do Azure, você pode monitorar o status desses nós, o que ajuda você a decidir se deseja adicionar ou remover um nó do gateway lógico. 

## <a name="architecture"></a>Arquitetura 
O diagrama a seguir fornece a visão geral da arquitetura do recurso de escalabilidade e disponibilidade do Gateway de Gerenciamento de Dados: 

![Gateway de Gerenciamento de Dados – alta disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Um **gateway lógico** é o gateway que você adiciona a um data factory no Portal do Azure. Anteriormente, você podia associar apenas um computador do Windows local com instalado com o Gateway de Gerenciamento de Dados instalado com um gateway de lógico. Esse computador de gateway local é chamado de nó. Agora, você pode associar até **quatro nós físicos** com um gateway lógico. Um gateway lógico com vários nós é chamado de **gateway com vários nós**.  

Todos esses nós estão **ativos**. Todos eles podem processar trabalhos de movimentação de dados para mover dados entre armazenamentos de dados locais e na nuvem. Um dos nós atua tanto como dispatcher quanto como de trabalho. Outros nós no grupo são nós de trabalho. Um nó **dispatcher** efetua pull de tarefas/trabalhos de movimentação de dados do serviço de nuvem e expede-os para nós de trabalho (incluindo ele próprio). Um nó **de trabalho** executa trabalhos de movimentação de dados para mover dados entre armazenamentos de dados locais e na nuvem. Todos os nós são de trabalho. Apenas um nó pode ser expedição e de trabalho.    

Normalmente, você pode começar com um nó e **escalar horizontalmente** para adicionar mais nós, conforme os nós existentes são sobrecarregados com a carga de movimentação de dados. Você também pode **escalar verticalmente** a funcionalidade de movimentação de dados de um nó de gateway, aumentando o número de trabalhos simultâneos que podem ser executados no nó. Essa funcionalidade também está disponível com um gateway de nó único (mesmo quando o recurso de escalabilidade e disponibilidade não está habilitado). 

Um gateway com vários nós mantém as credenciais do armazenamento de dados sincronizadas entre todos os nós. Se há um problema de conectividade de nó para nó, as credenciais podem estar fora de sincronia. Quando você define as credenciais para um armazenamento de dados local que usa um gateway, ele salva as credenciais no nó dispatcher/de trabalho. O nó dispatcher é sincronizado com outros nós de trabalho. Este processo é conhecido como **sincronização de credenciais**. O canal de comunicação entre os nós pode ser **criptografado** por um certificado SSL/TLS público. 

## <a name="set-up-a-multi-node-gateway"></a>Configurar um gateway com vários nós
Esta seção pressupõe que você percorreu os dois artigos a seguir ou está familiarizado com conceitos nesses artigos: 

- [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) – fornece uma visão geral detalhada do gateway.
- [Mover dados entre locais e na nuvem armazenamentos de dados](data-factory-move-data-between-onprem-and-cloud.md) – contém um passo a passo com instruções passo a passo para usar um gateway com um único nó.  

> [!NOTE]
> Antes de você instalar um Gateway de Gerenciamento de Dados em um computador local do Windows, consulte os pré-requisitos listados no [artigo principal](data-factory-data-management-gateway.md#prerequisites).

1. No [passo a passo](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), ao criar um gateway lógico, habilite o recurso de **Alta Disponibilidade e Escalabilidade**. 

    ![Gateway de Gerenciamento de Dados – habilitar alta disponibilidade e escalabilidade](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na página **Configurar**, use um dos links **Instalação Expressa** ou **Instalação Manual** para instalar um gateway no primeiro nó (um computador local do Windows).

    ![Gateway de Gerenciamento de Dados – instalação manual ou expressa](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se você usar a opção de instalação expressa, a comunicação de nó para nó é feita sem criptografia. O nome do nó é o mesmo que o nome do computador. Use a instalação manual se a comunicação entre nós precisar ser criptografada ou para especificar um nome de nó de sua escolha. Nomes de nó não podem ser editados posteriormente.
3. Se você escolher **instalação expressa**
    1. Você verá a seguinte mensagem depois que o gateway for instalado com êxito:

        ![Gateway de Gerenciamento de Dados – êxito na instalação expressa](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Inicie o Gerenciador de Configurações de Gerenciamento de Dados para o gateway seguindo [estas instruções](data-factory-data-management-gateway.md#configuration-manager). Você vê o nome do gateway, o nome do nó, o status, etc.

        ![Gateway de Gerenciamento de Dados – instalação do gateway bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se você escolher **configuração manual**:
    1. Baixe o pacote de instalação do Centro de Download da Microsoft, execute-o para instalar o gateway em seu computador.
    2. Use a **chave de autenticação** da página **Configurar** para registrar o gateway.
    
        ![Gateway de Gerenciamento de Dados – instalação do gateway bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na página **Novo nó de gateway**, você pode fornecer um **nome** personalizado para o nó do gateway. Por padrão, o nome do nó é o mesmo que o nome do computador.    

        ![Gateway de Gerenciamento de Dados – especificar nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na próxima página, você pode escolher se deseja ou não **habilitar a criptografia para comunicação de nó para nó**. Clique em **ignorar** para desabilitar a criptografia (padrão).

        ![Gateway de Gerenciamento de Dados – habilitar criptografia](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Só há suporte para alteração do modo de criptografia quando você tem um único nó de gateway no gateway lógico. Para alterar o modo de criptografia quando um gateway tem vários nós, siga as etapas a seguir: exclua todos os nós exceto um, altere o modo de criptografia e, em seguida, adicione os nós novamente.
        > 
        > Consulte a seção [Requisitos de certificado TLS/SSL](#tlsssl-certificate-requirements) para obter uma lista de requisitos para usar um certificado TLS/SSL. 
    5. Depois que o gateway for instalado com êxito, clique em Iniciar o Gerenciador de Configurações:
    
        ![Instalação manual – iniciar gerenciador de configurações](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Consulte o Gerenciador de Configurações do Gateway de Gerenciamento de Dados no nó (computador do Windows local), que mostra o status de conectividade, o **nome do gateway** e **o nome do nó**.  

        ![Gateway de Gerenciamento de Dados – instalação do gateway bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se você está provisionando o gateway em uma VM do Azure, você poderá usar [este modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Esse script cria um gateway lógico, configura as VMs com software de Gateway de Gerenciamento de Dados instalado e registra-os com o gateway lógico. 
6. No Portal do Azure, inicie a página **Gateway**: 
    1. Na home page do data factory no portal, clique em **Serviços Vinculados**.
    
        ![Página inicial da data factory](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Selecione o **gateway** para ver a página **Gateway**:
    
        ![Página inicial da data factory](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Você vê a página **Gateway**:   

        ![Gateway com exibição de nó único](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Clique em **Adicionar Nó** na barra de ferramentas para adicionar um nó para o gateway lógico. Se você estiver planejando usar a instalação expressa, siga esta etapa do computador local que será adicionado como um nó para o gateway. 

    ![Gateway de Gerenciamento de Dados – menu adicionar nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. As etapas são semelhantes àquelas para configuração do primeiro nó. A interface do usuário do Gerenciador de Configurações permite que você defina o nome do nó, se você escolher a opção de instalação manual: 

    ![Gerenciador de Configurações – instalar segundo gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Depois que o gateway está instalado com êxito no nó, a ferramenta Gerenciador de Configurações exibe a tela a seguir:  

    ![Gerenciador de Configurações – instalação do segundo gateway bem-sucedida](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se você abrir a página **Gateway** no portal, você verá dois nós de gateway agora: 

    ![Gateway com dois nós no portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Para excluir um nó do gateway, clique em **Excluir Nó** na barra de ferramentas, selecione o nó que você deseja excluir e, em seguida, clique em **Excluir** na barra de ferramentas. Essa ação exclui o nó selecionado do grupo. Observe que essa ação não desinstala o software de Gateway de Gerenciamento de Dados do nó (computador do Windows local). Use **Adicionar ou remover programas** no Painel de Controle local para desinstalar o gateway. Quando você desinstala um gateway por meio do nó, ele é automaticamente excluído no portal.   

## <a name="upgrade-an-existing-gateway"></a>Atualizar um gateway existente
Você pode atualizar um gateway existente para usar o recurso de alta disponibilidade e escalabilidade. Esse recurso funciona somente com nós que têm o gateway de gerenciamento de dados da versão >= 2.12.xxxx. Você pode ver a versão do Gateway de Gerenciamento de Dados instalado no computador na guia **Ajuda** do Gerenciador de Configurações do Gateway de Gerenciamento de Dados. 

1. Atualize o gateway no computador local para a versão mais recente, baixando e executando um pacote de instalação MSI do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Consulte a seção [Instalação](data-factory-data-management-gateway.md#installation) para obter detalhes.  
2. Navegue até o Portal do Azure. Inicie a **página do Data Factory** o seu data factory. Clique no bloco Serviços vinculados para iniciar a **página Serviços vinculados**. Selecione o gateway para iniciar a **página Gateway**. Clique na **Versão Prévia do Recurso** e habilite-a, conforme mostrado na imagem a seguir: 

    ![Gateway de Gerenciamento de Dados – habilitar versão prévia do recurso](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Quando a versão prévia do recurso estiver habilitada no portal, feche todas as páginas. Reabra a **página Gateway** para ver a nova IU (interface do usuário) da versão prévia.
 
    ![Gateway de Gerenciamento de Dados – versão prévia do recurso habilitada com êxito](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Gateway de Gerenciamento de Dados – interface do usuário da versão prévia](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante a atualização, o nome do primeiro nó é o nome do computador. 
3. Agora, adicione um nó. Na página **Gateway**, clique em **Adicionar Nó**.  

    ![Gateway de Gerenciamento de Dados – menu adicionar nó](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Siga as instruções da seção anterior para configurar o nó. 

### <a name="installation-best-practices"></a>Melhores práticas de instalação

- Configure o plano de energia no computador host para o gateway para que o computador não hiberne. Se o computador host hibernar, o gateway não responderá às solicitações de dados.
- Faça backup do certificado associado ao gateway.
- Verifique se todos os nós são de configuração semelhante (recomendada) para um desempenho ideal. 
- Adicione pelo menos dois nós para assegurar alta disponibilidade.  

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL
Aqui estão os requisitos para o certificado TLS/SSL que é usado para proteger as comunicações entre os nós de gateway:

- O certificado deve ser um certificado X509 v3 publicamente confiável.
- Todos os nós de gateway devem confiar nesse certificado. 
- É recomendável que você use certificados emitidos por uma AC (autoridade de certificação) pública (de terceiros).
- Dá suporte a qualquer tamanho de chave com suporte pelo Windows Server 2012 R2 para certificados SSL.
- Não dá suporte a certificados que usam chaves CNG.
- Há suporte para certificados curinga. 


## <a name="monitor-a-multi-node-gateway"></a>Monitorar um gateway com vários nós
### <a name="multi-node-gateway-monitoring"></a>Monitoramento de gateway com vários nós
No Portal do Azure, você pode exibir o instantâneo quase em tempo real de utilização de recursos (CPU, memória, rede(entrada/saída), etc.) em cada nó, juntamente com os status dos nós de gateway. 

![Gateway de Gerenciamento de Dados – monitoramento de vários nós](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Você pode habilitar as **Configurações Avançadas** na página **Gateway** para ver as métricas avançadas como **Rede**(entrada/saída), **Função e Status de Credencial**, que é útil na depuração de problemas do gateway e **Trabalhos Simultâneos** (Executando/Limite), que podem ser modificados/alterados adequadamente durante o ajuste de desempenho. A tabela a seguir fornece descrições das colunas na lista **Nós de Gateway**:  

Propriedade de monitoramento | Descrição
:------------------ | :---------- 
Nome | Nome do gateway lógico e nós associada ao gateway.  
Status | Status do gateway lógico e dos nós de gateway. Exemplo: Online/Offline/Limitado/etc. Para obter informações sobre esses status, consulte a seção [Status do gateway](#gateway-status). 
Versão | Mostra a versão do gateway lógico e cada nó do gateway. A versão do gateway lógico é determinada com base na versão da maioria dos nós no grupo. Se não há nós com versões diferentes na configuração do gateway lógico, somente os nós com o mesmo número de versão que o gateway lógico funcionam corretamente. Os outros estão no modo limitado e precisam ser atualizados manualmente (somente caso a atualização automática falhe). 
Memória disponível | Memória disponível em um nó do gateway. Esse valor é um instantâneo quase em tempo real. 
Utilização da CPU | Utilização da CPU de um nó de gateway. Esse valor é um instantâneo quase em tempo real. 
Rede (Entrada/Saída) | Utilização de rede de um nó de gateway. Esse valor é um instantâneo quase em tempo real. 
Trabalhos Simultâneos (Executando/Limite) | Número de trabalhos ou tarefas em execução em cada nó. Esse valor é um instantâneo quase em tempo real. Limite significa a máxima de trabalhos simultâneos para cada nó. Esse valor é definido com base no tamanho do computador. Você pode aumentar o limite para escalar verticalmente a execução de trabalhos simultâneos em cenários avançados, em que memória/CPU/rede são subutilizados, mas as atividades estão atingindo o tempo limite. Essa funcionalidade também está disponível com um gateway de nó único (mesmo quando o recurso de escalabilidade e disponibilidade não está habilitado). Para obter mais informações, consulte a seção [considerações de dimensionamento](#scale-considerations). 
Função | Há dois tipos de funções – Dispatcher e de trabalho. Todos os nós são de trabalho, o que significa que eles podem ser usados para executar trabalhos. Há apenas um nó dispatcher, que é usado para efetuar pull de tarefas/trabalhos dos serviços de nuvem e distribuí-los para nós de trabalho diferentes (incluindo ele próprio). 

![Gateway de Gerenciamento de Dados – monitoramento avançado de vários nós](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Status do gateway

A tabela a seguir fornece os possíveis status de um **nó de gateway**: 

Status  | Comentários/Cenários
:------- | :------------------
Online | Nó conectado ao serviço Data Factory.
Off-line | O nó está offline.
Atualizando | O nó está sendo atualizado automaticamente.
Limitado | Devido a problema de conectividade. Pode ser devido a problema 8050 de porta HTTP, problema de conectividade do barramento de serviço ou problema de sincronização de credenciais. 
Inativo | O nó está em uma configuração diferente da configuração de outros nós de maioria.<br/><br/> Um nó pode ficar inativo quando ele não pode se conectar a outros nós. 


A tabela a seguir fornece os possíveis status de um **gateway lógico**. O status do gateway depende do status dos nós do gateway. 

Status | Comentários
:----- | :-------
Precisa de Registro | Nenhum nó está registrado ainda para este gateway lógico
Online | Nós de Gateway estão online
Off-line | Nenhum nó no status online.
Limitado | Nem todos os nós neste gateway estão em estado íntegro. Esse status é um aviso de que um nó pode estar inativo! <br/><br/>Isso pode ser devido a problema de sincronização de credenciais no nó dispatcher/de trabalho. 

### <a name="pipeline-activities-monitoring"></a>Monitoramento de atividades/de pipeline
O Portal do Azure fornece uma experiência de monitoramento de pipeline com detalhes de nível de nó granulares. Por exemplo, ele mostra quais atividades foram executadas em qual nó. Essas informações podem ser úteis para entender os problemas de desempenho em um nó específico, por exemplo, devido à limitação de rede. 

![Gateway de Gerenciamento de Dados – monitoramento de vários nós para pipelines](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Gateway de Gerenciamento de Dados – detalhes do pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerações de escala

### <a name="scale-out"></a>Expansão
Quando a **memória disponível está baixa** e o **uso de CPU é alto**, adicionar um novo nó ajuda a escalar horizontalmente a carga entre computadores. Se as atividades estão falhando devido a tempo limite atingido ou ao nó do gateway ficar offline, ajuda adicionar um nó ao gateway.
 
### <a name="scale-up"></a>Escalar verticalmente
Quando a memória e a CPU disponíveis não são bem utilizadas mas a capacidade ociosa é 0, você deve escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó. Também convém escalar verticalmente quando atividades estão atingindo o tempo limite porque o gateway está sobrecarregado. Conforme mostrado na imagem a seguir, você pode aumentar a capacidade máxima de um nó. Sugerimos duplicá-la para começar.  

![Gateway de Gerenciamento de Dados – considerações de escala](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Problemas conhecidas/últimas alterações

- No momento, você pode ter até quatro nós de gateway físico para um único gateway lógico. Se você precisar de mais de quatro nós por motivos de desempenho, envie um email para [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com).
- Você não pode registrar novamente um nó de gateway com a chave de autenticação de outro gateway lógico para o qual mudar do gateway lógico atual. Para registrar novamente, desinstale o gateway do nó, reinstale o gateway e registre-o com a chave de autenticação para o gateway lógico. 
- Se o proxy HTTP é necessário para todos os seus nós de gateway, configure o proxy em diahost.exe.config e diawp.exe.config e use o gerenciador do servidor para verificar se todos os nós têm os mesmos diahost.exe.config e diawip.exe.config. Consulte a seção [definir configurações de proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) para obter detalhes. 
- Para alterar o modo de criptografia para comunicação de nó para nó no Gerenciador de Configurações do Gateway, exclua todos os nós no portal, exceto um. Em seguida, adicione nós novamente depois de alterar o modo de criptografia.
- Use um certificado SSL oficial se você optar por criptografar o canal de comunicação de nó para nó. Um certificado autoassinado pode causar problemas de conectividade, já que o mesmo certificado pode não ser confiável na lista da autoridade de certificação em outros computadores. 
- Você não pode registrar um nó de gateway para um gateway lógico quando a versão do nó é inferior à versão do gateway lógico. Excluir todos os nós do gateway lógico do portal de forma que você possa registrar um nó de versão inferior (fazer downgrade dele). Se você excluir todos os nós de um gateway lógico, instale manualmente e registre novos nós nesse gateway lógico. Não há suporte para a instalação expressa nesse caso.
- Você não pode usar a instalação expressa para instalar nós em um gateway lógico existente, que ainda está usando credenciais de nuvem. Você pode verificar onde as credenciais são armazenadas do Gerenciador de Configurações do Gateway na guia Configurações.
- Você não pode usar a instalação expressa para instalar nós em um gateway lógico existente, que ainda tem criptografia de nó para nó habilitada. Já que a configuração do modo de criptografia consiste em adicionar manualmente os certificados, a instalação expressa não é mais uma opção. 
- Para obter uma cópia do arquivo do ambiente local, você não deve mais usar \\localhost ou C:\files, já que o localhost ou a unidade local podem não estar acessíveis por meio de todos os nós. Em vez disso, use \\ServerName\files para especificar o local dos arquivos.


## <a name="rolling-back-from-the-preview"></a>Revertendo da versão prévia 
Para reverter da versão prévia, exclua todos os nós, com exceção de um. Não importa quais nós você excluir, mas verifique se você tem pelo menos um nó no gateway lógico. Você pode excluir um nó desinstalando o gateway no computador ou usando o Portal do Azure. No Portal do Azure, na página **Data Factory**, clique em Serviços vinculados para iniciar a página **Serviços vinculados**. Selecione o gateway para iniciar a página **Gateway**. Na página Gateway, você pode ver os nós associados ao gateway. A página permite excluir um nó do gateway.
 
Depois de excluí-lo, clique na **versão prévia dos recursos** na mesma página do Portal do Azure e desabilite a versão prévia do recurso. Você redefiniu o gateway para um gateway de um nó de DG (disponibilidade geral).


## <a name="next-steps"></a>Próximas etapas
Examine os seguintes artigos:
- [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) – fornece uma visão geral detalhada do gateway.
- [Mover dados entre locais e na nuvem armazenamentos de dados](data-factory-move-data-between-onprem-and-cloud.md) – contém um passo a passo com instruções passo a passo para usar um gateway com um único nó. 
