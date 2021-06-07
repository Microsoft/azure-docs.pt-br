---
title: Criar um cluster do Service Fabric no portal do Azure
description: Saiba como configurar um cluster seguro do Service Fabric no Azure usando o portal do Azure e o Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563719"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Criar um cluster do Service Fabric no usando o portal do Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Este é um guia passo a passo que orienta você pelas etapas de configuração de um cluster do Service Fabric (Linux ou Windows) no Azure usando o portal do Azure. Este guia apresenta as seguintes etapas:

* Crie um cluster no Azure por meio do portal do Azure.
* Autentique os administradores que usam certificados.

> [!NOTE]
> Para obter opções de segurança mais avançadas, como autenticação de usuário com o Azure Active Directory e configurar certificados para a segurança de aplicativo, [crie o cluster usando o Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Segurança de cluster 
Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos. Para obter mais informações sobre como os certificados são usados no Service Fabric, consulte [Cenários de segurança do cluster do Service Fabric][service-fabric-cluster-security].

Se esta for a primeira vez que você está criando um cluster do Service Fabric ou implantando um cluster para cargas de trabalho de teste, você pode pular para a próxima seção (**criar cluster na portal do Azure**) e fazer com que o sistema gere certificados necessários para seus clusters que executam cargas de trabalho de teste. Se você estiver configurando um cluster para cargas de trabalho de produção, continue lendo.

#### <a name="cluster-and-server-certificate-required"></a>Certificado de cluster e de servidor (necessário)
Esse certificado é necessário para proteger um cluster e impedir o acesso não autorizado a ele. Ele fornece segurança de cluster de duas maneiras:

* **Autenticação de cluster:** Autentica a comunicação de nó para nó para a Federação de cluster. Somente os nós que podem provar sua identidade com esse certificado podem ingressar no cluster.
* **Autenticação do servidor:** Autentica os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o cliente de gerenciamento saiba que ele está se comunicando com o cluster real. Esse certificado também fornece o TLS para a API de gerenciamento de HTTPS e para Service Fabric Explorer por HTTPS.

Para servir a essas finalidades, o certificado deverá atender a estes requisitos:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome da **entidade do certificado deve corresponder ao domínio** usado para acessar o cluster de Service Fabric. Isso é necessário para fornecer o TLS para os pontos de extremidade de gerenciamento HTTPS do cluster e Service Fabric Explorer. Não é possível obter um certificado TLS/SSL de uma autoridade de certificação (CA) para o `.cloudapp.azure.com` domínio. Adquira um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

#### <a name="client-authentication-certificates"></a>Certificados de autenticação de cliente
Os certificados de cliente adicionais autenticam os administradores para tarefas de gerenciamento de cluster. O Service Fabric tem dois níveis de acesso: **administrador** e **usuário somente leitura**. No mínimo, um único certificado para acesso administrativo deve ser usado. Para acesso de nível de usuário adicional, deve ser fornecido um certificado diferente. Para obter mais informações sobre as funções de acesso, consulte [Controle de acesso baseado em função para clientes do Service Fabric][service-fabric-cluster-security-roles].

Você não precisa carrear os certificados de autenticação do cliente no Cofre de Chaves para trabalhar com o Service Fabric. Esses certificados só precisam ser fornecido para os usuários autorizados para gerenciamento de cluster. 

> [!NOTE]
> O Azure Active Directory é a maneira recomendada para autenticar clientes para operações de gerenciamento de cluster. Para usar o Azure Active Directory, é necessário [criar um cluster usando o Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certificados de aplicativo (opcionais)
Qualquer número de certificados adicionais pode ser instalado em um cluster para fins de segurança do aplicativo. Antes de criar o cluster, considere os cenários de segurança de aplicativos que exigem um certificado a ser instalado em nós, como:

* Criptografia e descriptografia de valores de configuração de aplicativo
* Criptografia de dados entre nós durante a replicação 

Os certificados de aplicativo não podem ser configurados durante [a criação de um cluster por meio do portal do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Para configurar certificados de aplicativo durante a instalação do cluster, é necessário [criar um cluster usando o Azure Resource Manager][create-cluster-arm]. Você também pode adicionar certificados do aplicativo ao cluster após ele ter sido criado.

## <a name="create-cluster-in-the-azure-portal"></a>Criar um cluster no portal do Azure

Criar um cluster de produção para atender às necessidades de seu aplicativo envolve algum planejamento, para ajudá-lo com isso, é altamente recomendável que você leia e reconheça o documento [Considerações sobre planejamento do Service Fabric Cluster][service-fabric-cluster-capacity]. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Pesquise o recurso de cluster do Service Fabric

Entre no [portal do Azure][azure-portal].
Clique em **Criar um recurso** para adicionar um novo modelo de recurso. Procure o modelo Cluster do Service Fabric no **Marketplace** em **Tudo**.
Selecione **Cluster do Service Fabric** na lista.

![pesquisa pelo modelo de cluster do Service Fabric no portal do Azure.][SearchforServiceFabricClusterTemplate]

Navegue até o **Cluster do Service Fabric** folha e clique em **Criar**.

A folha **criar Service Fabric cluster** tem as quatro etapas a seguir:

### <a name="1-basics"></a>1. noções básicas
![Captura de tela da criação de um novo grupo de recursos.][CreateRG]

Na folha Básico, você precisa fornecer os detalhes básicos do seu cluster.

1. Insira o nome do seu cluster.
2. Insira um **nome de usuário** e uma **senha** para a Área de Trabalho Remota para as VMs.
3. Selecione a **Assinatura** desejada para a implantação do cluster, especialmente se você tiver várias assinaturas.
4. Crie um novo **grupo de recursos**. É melhor dar a ele o mesmo nome do cluster, pois ajuda a encontrá-los mais tarde, especialmente quando você estiver tentando fazer alterações em sua implantação ou excluir o cluster.
   
   > [!NOTE]
   > Embora você possa optar por usar um grupo de recursos existente, é uma boa prática criar um novo grupo de recursos. Isso facilita a excluir clusters e todos os recursos que ele usa.
   > 
   > 
5. Selecione a **região** na qual você deseja criar o cluster. Se pretender usar um certificado existente já carregado em um cofre de chaves, você deverá usar a mesma região onde está o cofre de chaves. 

### <a name="2-cluster-configuration"></a>2. configuração de cluster
![Criar um tipo de nó][CreateNodeType]

Configure os nós de cluster. Os tipos de nó definem os tamanhos e o número de VMs e suas propriedades. O cluster pode ter mais de um tipo de nó, mas o tipo de nó primário (o primeiro que você define no portal) deve ter pelo menos cinco VMs. Esse é o tipo de nó onde os serviços do sistema do Service Fabric são colocados. Não configure as **Propriedades de Posicionamento**, pois uma propriedade de posicionamento padrão de "NodeTypeName" é adicionada automaticamente.

> [!NOTE]
> Um cenário comum para vários tipos de nó é um aplicativo que contém um serviço front-end e um serviço back-end. Você quer colocar o serviço front-end em VMs menores (tamanhos como D2_V2) com portas abertas para a Internet e colocar o serviço de back-end em VMs maiores (com tamanhos como D3_V2, D6_V2, D15_V2 e assim por diante) com portas não são voltadas para a Internet abertas.
> 

1. Escolha um nome para o tipo de nó (um a 12 caracteres contendo somente letras e números).
2. O **tamanho** mínimo das VMs para o tipo de nó primário é determinado pela camada de **durabilidade** que você escolhe para o cluster. O padrão para a camada de durabilidade é Bronze. Para obter mais informações sobre durabilidade, consulte [como escolher a durabilidade do cluster do Service Fabric][service-fabric-cluster-durability].
3. Selecione o **tamanho da máquina Virtual**. As VMs da série D têm unidades SSD e são altamente recomendadas para aplicativos com monitoração de estado. Não use nenhuma SKU de VM com núcleos parciais ou que tenham menos de 10 GB de capacidade em disco disponível. Consulte [documento de consideração do planejamento de cluster do Service Fabric][service-fabric-cluster-capacity] para ajudar a selecionar o tamanho da VM.
4.  **Único cluster de nós e clusters de três nós** destinam-se somente para teste. Não há suporte para cargas de trabalho de produção em execução.
5. Escolha a **capacidade inicial do conjunto de dimensionamento de máquinas virtuais** para o tipo de nó. Você pode expandir ou reduzir o número de VMs em um tipo de nó posteriormente, mas no tipo de nó primário, o mínimo é cinco para cargas de trabalho de produção. Outros tipos de nó podem ter um mínimo de uma máquina virtual. O número **mínimo** de VMs para as unidades do tipo de nó primário da **confiabilidade** do seu cluster.  
6. Configure **pontos de extremidade personalizados**. Este campo permite que você insira uma lista separada por vírgulas de portas que você deseja expor por meio do Azure Load Balancer para a Internet pública para seus aplicativos. Por exemplo, se você planeja implantar um aplicativo web para o cluster, insira "80" aqui para permitir o tráfego na porta 80 em seu cluster. Para obter mais informações sobre pontos de extremidade, consulte [Comunicando-se com aplicativos][service-fabric-connect-and-communicate-with-services]
7. **Habilitar proxy reverso**.  O [proxy reverso do Service Fabric](service-fabric-reverseproxy.md) microsserviços de Ajuda em execução em um cluster do Service Fabric a descobrir e se comunicar com outros serviços que têm pontos de extremidade http.
8. De volta à **configuração de Cluster**, em **+ Mostrar configurações opcionais**, configure o **diagnóstico** do cluster. Por padrão, os diagnósticos são habilitados no cluster para ajudar na solução de problemas. Se quiser desabilitar o diagnóstico, alterne o **Status** para **Desligado**. **Não** é recomendável desligar o diagnóstico. Se você já tiver o projeto do Application Insights criado, forneça a chave, de maneira que os rastreamentos de aplicativo sejam roteados para ele.
9. **Inclui serviço DNS**.  O [serviço DNS](service-fabric-dnsservice.md) um serviço opcional que permite que você encontre outros serviços usando o protocolo DNS.
10. Selecione o modo de atualização do **Fabric para** o qual você deseja definir o cluster. Selecione **Automático**, se você desejar que o sistema automaticamente obtenha a versão mais recente e tente atualizar o cluster para ela. Defina o modo como **Manual**, se você desejar escolher uma versão com suporte. Para obter mais detalhes sobre o modo de upgrade do Fabric, consulte o documento [Service Upgrade Cluster do Service Fabric.][service-fabric-cluster-upgrade]

> [!NOTE]
> Damos suporte somente para clusters que executam versões com suporte do Service Fabric. Selecionando o modo **Manual** , você está assumindo a responsabilidade de atualizar seu cluster para uma versão com suporte.
> 

### <a name="3-security"></a>3. segurança
![Captura de tela das configurações de segurança no portal do Azure.][BasicSecurityConfigs]

Para facilitar a configuração de um cluster de teste seguro para você, oferecemos a opção **Básica**. Se você já tiver um certificado e já o carregou para sua [Cofre de chaves](../key-vault/index.yml) (e habilitada o Cofre de chaves para implantação), em seguida, usar o **personalizado** opção

#### <a name="basic-option"></a>Opção Básica
Siga as telas para adicionar ou reutilizar um cofre de chaves existente e adicionar um certificado. A adição do certificado é um processo síncrono e, por isso, você terá que aguardar a criação do certificado.

Resista à tentação de sair da tela até o processo anterior ser concluído.

![Captura de tela mostra a página de segurança com básico selecionado com o painel Key Vault e criar o painel Key Vault.][CreateKeyVault]

Agora que o Cofre de chaves é criado, edite as políticas de acesso para o Cofre de chaves. 

![Captura de tela mostra o painel criar Service Fabric cluster com a opção 3 segurança selecionada e uma explicação de que o cofre de chaves não está habilitado.][CreateKeyVault2]

Clique em **Editar políticas de acesso**, em seguida, **Mostrar políticas de acesso avançados** e habilitar o acesso às máquinas virtuais do Azure para implantação. Também é recomendável habilitar a implantação de modelo. Depois de fazer suas seleções, não se esqueça de clicar no botão **Salvar** e fechar o painel **Políticas de acesso**.

![Captura de tela mostra o painel criar Service Fabric cluster com o painel de segurança aberto e o painel políticas de acesso aberto.][CreateKeyVault3]

Insira o nome do certificado e clique em **OK**.

![Captura de tela mostra o painel criar Service Fabric cluster com segurança selecionada como antes, mas sem a explicação de que o cofre de chaves não está habilitado.][CreateKeyVault4]

#### <a name="custom-option"></a>Opção Personalizada
Ignore esta seção, caso você já tenha realizado as etapas na opção **Básica**.

![Captura de tela mostra a caixa de diálogo Definir configurações de segurança do cluster.][SecurityCustomOption]

Você precisa do cofre da chave de Origem, URL do Certificado e informações de impressão digital do Certificado para concluir a página de segurança. Se você não estiver à mão, abra outra janela do navegador e, no portal do Azure, faça o seguinte

1. Navegue até seu serviço de Cofre de chaves.
2. Selecione a guia "Propriedades" e copie a "ID RECURSO" para "Cofre da chave de origem" na outra janela do navegador 

    ![Captura de tela mostra a janela Propriedades para o cofre de chaves.][CertInfo0]

3. Agora selecione a guia “Certificados”.
4. Clique na impressão digital do certificado, que leva você até a página Versões.
5. Clique nas Guids na Versão atual.

    ![Captura de tela mostra a janela do certificado para o cofre de chaves][CertInfo1]

6. Agora você deve estar em uma tela como abaixo. Copie a impressão digital SHA-1 hexadecimal para "Impressão digital do certificado" na outra janela do navegador
7. Copie o identificador' segredo' para a "URL do certificado" em outra janela do navegador.

    ![Captura de tela mostra a caixa de diálogo versão do certificado com uma opção para copiar o identificador do certificado.][CertInfo2]

Marque a caixa **Definir configurações avançadas** para inserir certificados de cliente para **cliente administrativo** e **cliente somente leitura**. Nesses campos, insira a impressão digital do seu certificado de cliente do administrador e a impressão digital do seu certificado de cliente do usuário somente leitura, se aplicável. Quando os administradores tentam se conectar ao cluster, eles só receberão acesso se tiverem um certificado com uma impressão digital que corresponda aos valores da impressão digital inseridos aqui.  

### <a name="4-summary"></a>4. Resumo

Agora você está pronto para implantar o cluster. Antes disso, baixe o certificado, procure dentro da caixa informativa azul grande o link. Não se esqueça de manter o certificado em um local seguro. Você precisa dele para se conectar ao cluster. Como o certificado que você baixou não tem uma senha, é recomendável adicionar uma.

Para concluir a criação do cluster, clique em **Criar**. Você também pode baixar o modelo.

![Captura de tela mostra a página de resumo criar Service Fabric cluster com um link para exibir e baixar um certificado.][Summary]

Você pode ver o progresso da criação nas notificações. (Clique no ícone "Bell" próximo à barra de status na parte superior direita da tela.) Se você clicou **em fixar no quadro inicial** ao criar o cluster, você vê **implantando Service Fabric cluster** fixado no quadro **inicial** . Esse processo levará algum tempo. 

Para executar operações de gerenciamento em seu cluster usando o PowerShell ou a CLI, você precisa se conectar ao cluster e ler mais sobre como [conectar-se ao cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Exibir o status do cluster
![Captura de tela dos detalhes do cluster no painel.][ClusterDashboard]

Depois que o cluster for criado, você poderá inspecioná-lo no portal:

1. Vá para **Procurar** e clique em **Clusters do Service Fabric**.
2. Localize o cluster e clique nele.
3. Agora você pode ver os detalhes do cluster no painel, inclusive o ponto de extremidade público do cluster e um link para o Service Fabric Explorer.

A seção **Monitor do Nó** na folha do painel do cluster indica o número de VMs íntegras e não íntegras. Encontre mais detalhes sobre a integridade do cluster em [Introdução ao monitoramento da integridade do Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Os clusters de Service Fabric exigem um determinado número de nós esteja sempre ativo para manter a disponibilidade e preservar o estado – conhecido como "manter o quórum". Portanto, normalmente não é seguro desligar todos os computadores no cluster, a menos que você tenha primeiro feito um [backup completo do estado][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do Conjunto de Escala de Máquinas Virtuais ou a um nó de cluster
Cada um dos NodeTypes que você especifica no cluster resulta na configuração de um Conjunto de dimensionamento de máquinas virtuais. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Próximas etapas
Neste ponto, você tem um cluster seguro usando certificados para autenticação de gerenciamento. Em seguida, [conecte-se ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar segredos do aplicativo](service-fabric-application-secret-management.md).  Além disso, saiba mais sobre as [Azure Service Fabric support options](service-fabric-support.md) (Opções de suporte do Service Fabric).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
