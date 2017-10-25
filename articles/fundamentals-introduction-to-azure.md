---
title: "Introdução ao Microsoft Azure | Microsoft Docs"
description: "Novo no Microsoft Azure? Obtenha uma visão geral básica dos serviços que ele oferece com exemplos de como são úteis."
services: " "
documentationcenter: .net
author: rboucher
manager: carolz
editor: 
ms.assetid: 6f47f711-2208-4c21-8c1d-826a54c05c29
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2015
ms.author: robb
ms.openlocfilehash: 2d2cc04887d90b9f005bfe5310a9ce2fb2f7e217
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-microsoft-azure"></a>Apresentando o Microsoft Azure
O Microsoft Azure é a plataforma de aplicativos da Microsoft para a nuvem pública.  A finalidade deste artigo é mostrar os princípios básicos do Azure, mesmo que você não saiba nada sobre computação em nuvem.

**Como ler este artigo**

O Azure está aumentando o tempo todo, portanto, é fácil ficar sobrecarregado.  Comece com os serviços básicos, que são listados primeiro neste artigo e, em seguida, vá para serviços adicionais. Isso não significa que você não pode utilizar os serviços adicionais por conta própria, mas os serviços básicos compõem o núcleo de um aplicativo sendo executado pelo Azure.

**Fornecer feedback**

Seu feedback é importante. Este artigo deve oferecer a você uma visão geral eficaz do Azure. Se isso não acontecer, mencione para nós na seção de comentários da parte inferior da página. Ofereça alguns detalhes sobre o que você esperava ver e como melhorar este artigo.  

## <a name="the-components-of-azure"></a>Os componentes do Azure
O Azure agrupa serviços em categorias no Portal de Gerenciamento e em vários recursos visuais, como [O que é o infográfico do Azure](https://azure.microsoft.com/documentation/infographics/azure/) . O Portal de Gerenciamento é o que você utiliza para gerenciar a maior parte (mas não todos) os serviços no Azure.

Este artigo utilizará uma **organização diferente** para tratar de serviços baseados em uma função similar e para chamar sub-serviços que são parte de serviços maiores.  

![Componentes do Azure](./media/fundamentals-introduction-to-azure/AzureComponentsIntroNew780.png)   
 *Figura: o Azure fornece serviços de aplicativos acessíveis pela Internet, sendo executados em datacenters do Azure.*

## <a name="management-portal"></a>Portal de Gerenciamento
O Azure conta com uma interface web chamada [Portal de Gerenciamento](http://manage.windowsazure.com) , que permite que os administradores acessem e administrem a maioria dos recursos do Azure (mas não todos).  A Microsoft normalmente libera o novo portal de interface de usuário em versão beta, antes de descontinuar o antigo. A versão nova é chamada de ["Portal de Visualização do Azure"](https://portal.azure.com/).

Geralmente, há uma grande sobreposição quando ambos os portais estão ativos. Embora os serviços essenciais apareçam nos dois portais, nem todas as funcionalidades estão presentes em ambos. Os serviços mais recentes podem aparecer primeiro no portal mais novo, enquanto os serviços mais antigos podem estar presentes apenas no portal mais antigo.  Ou seja, se você não encontrar algo no portal mais antigo, verifique o portal mais novo e vice-versa.

## <a name="compute"></a>Computação
Uma das tarefas mais básicas da plataforma na nuvem é executar aplicativos. Cada um dos modelos de computação do Azure tem sua própria função a ser desempenhada.

Essas tecnologias podem ser usadas separadamente ou combinadas, conforme a necessidade, para criar a base certa para o seu aplicativo. A abordagem escolhida depende de quais problemas você está tentando resolver.

### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure
![Máquinas Virtuais do Azure ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_VirtualMachinesIntroNew_12345.png)   
*Figura: máquinas virtuais do Azure oferecem controle total de instâncias de máquina virtual na nuvem.*

A capacidade de criar uma máquina virtual sob demanda, seja de uma imagem padrão ou de uma fornecida por você, pode ser bastante útil. Essa abordagem, geralmente conhecida como Infraestrutura como Serviço (IaaS), é fornecida pelas Máquinas Virtuais do Azure. A figura 2 mostra uma combinação de como uma VM (Máquina Virtual) é executada e criada por meio de um VHD.  

Para criar uma VM, você especifica qual VHD usar e o tamanho da VM.  Você então paga por cada momento em que a VM estiver em execução. Você paga por minuto e somente enquanto estiver em funcionamento, apesar de existir uma cobrança mínima de armazenamento para manter o VHD disponível. O Azure oferece uma galeria de VHDs em estoque (chamados "imagens") que contêm um sistema operacional inicializável do qual se pode começar. Estas incluem opções da Microsoft e de parceiros, como o Windows Server e Linux, SQL Server, Oracle e muitos outros. Você fica livre para criar VHDs e imagens e então carregá-los, por conta própria. Você pode até mesmo carregar VHDs que contém somente dados e acessá-los a partir de suas VMs em execução.

Seja qual for a origem do VHD, você pode armazenar persistentemente todas as alterações feitas enquanto uma VM estiver em execução. Na próxima vez que uma VM for criada desse VHD, tudo é obtido de onde você parou. Os VHDs que dão suporte às Máquinas Virtuais são armazenados nos blobs de armazenamento do Azure, sobre os quais falaremos depois.  Isso significa que você obtém redundância para garantir que suas VMs não desaparecerão devido a falhas de hardware e de disco. Também é possível copiar o VHD alterado fora do Azure e executá-lo localmente.

Seu aplicativo é executado dentro de uma ou mais Máquinas Virtuais, dependendo de como você o criou anteriormente ou de como decidir criá-lo, do zero, a partir de agora.

Essa abordagem geral para computação em nuvem pode ser usada para solucionar muitos problemas diferentes.

**Cenários para máquina virtual**

1. **Dev/Test** - Você pode usá-las para criar uma plataforma barata de desenvolvimento e teste que pode ser desligada quando tiver terminado de usá-la. Também é possível criar e executar aplicativos que usem qualquer linguagem e biblioteca de sua preferência. Esses aplicativos podem usar qualquer uma das opções de gerenciamento de dados fornecidas pelo Azure e você pode optar por usar o SQL Server ou outro DBMS em uma ou mais máquinas virtuais.
2. **Mova os aplicativos para o Azure (arrastar e deslocar)** - "Arrastar e deslocar" refere-se a mover seu aplicativo, como você utilizaria uma empilhadeira para mover um objeto grande.  Você “arrasta" o VHD a partir de seu datacenter local e “desloca-o" para o Azure, executando-o de lá.  Você normalmente precisará fazer algum trabalho para remover dependências de outros sistemas. Se houver muitas você pode escolher, em vez dessa opção, a opção 3.  
3. **Estenda seu Datacenter** - Use as VMs do Azure como uma extensão do seu datacenter local, executando o SharePoint ou outros aplicativos. Para oferecer suporte a essa opção, é possível criar domínios do Windows na nuvem executando o Active Directory nas VMs do Azure. Você pode utilizar a rede virtual do Azure (mencionada posteriormente) para unir sua rede local à rede do Azure.

### <a name="web-apps"></a>Aplicativos Web
![Aplicativos Web do Azure ROBBCSIART_TEST](./media/fundamentals-introduction-to-azure/mscsiart_AzureWebsitesIntroNew_12345.png)   
 *Figura: os Aplicativos Web do Azure executam um aplicativo de site na nuvem sem precisar gerenciar o servidor Web subjacente.*

Uma das tarefas mais comuns que as pessoas fazem em nuvem é executar sites e aplicativos web. As Máquinas Virtuais do Azure permitem isso, porém, ainda deixam você com a responsabilidade de administrar uma ou mais VMs e os sistemas operacionais subjacentes. As funções web dos serviços de nuvem podem fazer isso, mas implantá-las e mantê-las ainda exige trabalho administrativo.  E se você apenas quisesse um site no qual alguma outra pessoa cuidasse do trabalho administrativo para você?

Isso é exatamente o que fornecem os aplicativos Web. Esse modelo de computação oferece um ambiente de web gerenciado usando o Portal de Gerenciamento do Azure, bem como as APIs. Você pode mover um aplicativo de site existente para os Aplicativos Web não alterados ou pode criar um novo diretamente na nuvem. Assim que um site estiver em execução, será possível adicionar ou remover instâncias dinamicamente, dependendo dos Aplicativos Web do Azure para balancear a carga de solicitações entre elas. Os Aplicativos do Azure oferecem uma opção compartilhada, na qual o site é executado em uma máquina virtual com outros sites, além de uma opção padrão que permite que um site seja executado em sua própria VM. A opção padrão também permite que você aumente o tamanho (computação energia) das ocorrências, se necessário.

Para desenvolvimento, os Aplicativos Web dão suporte para .NET, PHP, Node.js, Java e Python, juntamente com o Banco de Dados SQL e o MySQL (da ClearDB, uma parceira da Microsoft) para armazenamento relacional. Eles também fornecem suporte interno para vários aplicativos conhecidos, incluindo WordPress, Joomla e Drupal. O objetivo é fornecer uma plataforma amplamente útil, de baixo custo e escalonável para criação de sites e aplicativos web na nuvem pública.

**Cenários de Aplicativos Web**

Os Aplicativos Web são destinados a ser úteis para empresas, desenvolvedores e agências de design da Web. Para empresas, é uma solução fácil de gerenciar, flexível, altamente segura e altamente disponível para a execução de sites da web de presença. Quando você precisa configurar um site, é melhor começar com os Aplicativos Web do Azure e continuar nos Serviços de Nuvem quando precisar de um recurso que não está disponível. Veja o final da seção “Computação" para mais links que possam ajudá-lo a escolher entre as opções.

### <a name="cloud-services"></a>Serviços de Nuvem
![Serviço de Nuvem do Azure](./media/fundamentals-introduction-to-azure/CloudServicesIntroNew.png)   
*Figura: os serviços de nuvem do Azure fornecem um local para executar código personalizado altamente dimensionável em um ambiente PaaS (Plataforma como Serviço)*

Suponha que você queira criar um aplicativo em nuvem que possa oferecer suporte a vários usuários simultaneamente, não exija muito esforço administrativo e nunca falhe. Você pode ser um fornecedor de software estabelecido, por exemplo, que está decidido a adotar o SaaS (Software como Serviço) criando uma versão de um dos seus aplicativos na nuvem. Ou você pode ser um iniciante criando um aplicativo de consumidor com grandes expectativas de que ele cresça rapidamente. Se estiver desenvolvendo com o Azure, que modelo de execução você deve usar?

Os Aplicativos Web do Azure permitem criar esse tipo de aplicativo Web, mas há algumas restrições. Por exemplo, se você não tiver acesso administrativo, significa que não poderá instalar softwares arbitrários. As Máquinas Virtuais do Azure proporcionam muita flexibilidade, incluindo acesso administrativo, e você certamente pode usá-las para criar um aplicativo bastante escalonável, mas ainda terá que lidar com muitos aspectos de confiabilidade e administração. O que você deseja é uma opção que proporcione o controle necessário, mas que também manipule a maior parte do trabalho exigido pela confiabilidade e administração.

Isso é exatamente o que é fornecido pelos Serviços de Nuvem do Azure. Essa tecnologia foi desenvolvida explicitamente para oferecer suporte a aplicativos escalonáveis, confiáveis e que exigem baixa administração. Esse é um exemplo do que normalmente chamamos de PaaS (Plataforma como Serviço). Para usá-la, você pode criar um aplicativo usando a tecnologia de sua preferência, como C#, Java, PHP, Python, Node.js, etc. Seu código é executado em máquinas virtuais (conhecidas como instâncias) que executam uma versão do Windows Server.

Mas essas VMs são diferentes daquelas que você cria com as Máquinas Virtuais do Azure. Em primeiro lugar, o Azure em si as gerencia, realizando tarefas como instalar patches do sistema operacional e implantar automaticamente novas imagens com patches. Isso indica que seu aplicativo não deve manter o estado nas instâncias da função Web ou de trabalho; ele deve ser mantido em uma das opções de gerenciamento de dados do Azure descritas na próxima seção. O Azure também monitora essas VMs, reiniciando todas que falharem. Você pode definir os serviços de nuvem para criarem automaticamente mais ou menos instâncias em resposta à demanda. Isso permite que você gerencie uma maior utilização e, então, faça o dimensionamento de volta, de modo que não haja tanto custo quando há menos uso.

Existem duas funções a serem escolhidas quando você cria uma instância, ambas baseadas no Windows Server. A principal diferença entre as duas é que a instância de uma função Web é executada no IIS, enquanto a instância de uma função de trabalho, não. No entanto, ambas são gerenciadas da mesma forma, e é comum que um aplicativo use ambas. Por exemplo, uma instância da função Web pode aceitar solicitações de usuários e passá-las a uma instância da função de trabalho para processamento. Para dimensionar o aplicativo vertical ou horizontalmente, é possível solicitar que o Azure crie mais instâncias da função ou desligue as instâncias existentes. E assim como as Máquinas Virtuais do Azure, você será cobrado apenas pelo tempo durante o qual cada instância da função de trabalho ou Web é executada.

**Cenários para serviços de nuvem**

Os Serviços de Nuvem são ideais para dar suporte à escala horizontal maciça quando você precisa de mais controle sobre a plataforma que o fornecido pelos Aplicativos Web do Azure, mas não precisa de controle sobre o sistema operacional subjacente.

#### <a name="choosing-a-compute-model"></a>Escolhendo um Modelo de Computação
A página [Comparação de Aplicativos Web, Serviços de Nuvem e Máquinas Virtuais do Azure](app-service/choose-web-site-cloud-service-vm.md) fornece informações mais detalhadas sobre como escolher um modelo de computação.

## <a name="data-management"></a>Gerenciamento de Dados
Os aplicativos precisam de dados, e diferentes tipos de aplicativos precisam de diferentes tipos de dados. Por esse motivo, o Azure oferece várias maneiras diferentes de armazenar e gerenciar dados. O Azure oferece muitas opções de armazenamento, mas todas são projetadas para serem armazenamento muito durável.  Com todas essas opções, há sempre três cópias de seus dados mantidas sincronizadas por um datacenter do Azure -- ou seis, se você permitir que o Azure utilize redundância geográfica para dar suporte a outro datacenter a pelo menos 482,8 Km de distância.     

### <a name="in-virtual-machines"></a>Nas Máquinas Virtuais
A capacidade de executar o SQL Server ou outro DBMS, em uma VM criada com as Máquinas Virtuais do Azure, já foi mencionada. Perceba que essa opção não se limita aos sistemas relacionais; você também tem a liberdade de executar as tecnologias NoSQL, como MongoDB e Cassandra. Executar seu próprio sistema de banco de dados é uma tarefa direta (ele replica o que costumávamos em nossos datacenters), mas também exige controle da administração desse DBMS.  Utilizando outras opções, o Azure gerencia mais da administração para você.

Outra vez, o estado da Máquina Virtual e quaisquer discos de dados adicionais que você crie ou carregue recebem o suporte do armazenamento blob (sobre o qual falaremos mais tarde).  

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
![Banco de dados SQL de armazenamento do Azure](./media/fundamentals-introduction-to-azure/StorageAzureSQLDatabaseIntroNew.png)   

*Figura: o Banco de Dados SQL do Azure oferece um serviço de banco de dados relacional gerenciado na nuvem.*

Para armazenamento relacional, o Azure oferece o recurso de Banco de Dados SQL. Não deixe o nome lhe enganar. Isso é diferente de um banco de dados SQL comum, fornecido pelo SQL Server e sendo executado sobre o Windows Server.  

Antigamente chamado de SQL Azure, o Banco de Dados SQL do Azure fornece todos os principais recursos de um sistema de gerenciamento de banco de dados relacional, incluindo transações atômicas, acesso simultâneo aos dados por vários usuários com integridade de dados, consultas ANSI SQL e um modelo de programação conhecido. Assim como o SQL Server, o Banco de Dados SQL pode ser acessado usando o Entity Framework, ADO.NET, JDBC, entre outras tecnologias conhecidas de acesso a dados. Ele também oferece suporte à maioria das linguagens T-SQL, juntamente com as ferramentas do SQL Server, como o SQL Server Management Studio. Para qualquer pessoa familiarizada com o SQL Server (ou outro banco de dados relacional), usar o Banco de Dados SQL é fácil.

Porém, o Banco de Dados SQL não é apenas um DBMS na nuvem: ele é um serviço PaaS. Você ainda controla os dados e quem os acessa, mas o Banco de Dados SQL cuida do enfadonho trabalho administrativo, como o gerenciamento da infraestrutura de hardware e a atualização automática do banco de dados e do software do sistema operacional. O Banco de Dados SQL também fornece uma alta disponibilidade, backups automáticos, capacidade de restauração a um ponto no tempo e também pode fazer a replicação de cópias através de regiões geográficas.  

**Cenários para banco de dados SQL**

Se você cria um aplicativo do Azure (utilizando qualquer um dos modelos de computação) que exige armazenamento relacional, o banco de dados SQL pode ser uma boa opção. Embora os aplicativos em execução fora da nuvem também possam usar esse serviço, há muitos outros cenários. Por exemplo, os dados armazenados no Banco de Dados SQL podem ser acessados em diferentes sistemas cliente, incluindo desktops, laptops, tablets e telefones. E como essa tecnologia fornece alta disponibilidade interna por meio da replicação, usar o Banco de Dados SQL pode ajudar a minimizar o tempo de inatividade.

### <a name="tables"></a>Tabelas
![Tabelas de armazenamento do Azure](./media/fundamentals-introduction-to-azure/StorageTablesIntroNew.png)  

*Figura: as tabelas do Azure oferecem um modo simples de NoSQL para armazenar dados.*

Esse recurso às vezes é chamado por nomes diferentes, já que é parte de um recurso mais amplo chamado “Armazenamento do Azure". Se você consultar “tabelas", “Tabelas do Azure" ou “tabelas de armazenamento", verá que os três equivalem à mesma coisa.  

Não se confunda com o nome: essa tecnologia não oferece armazenamento relacional. Na verdade, é um exemplo de uma abordagem NoSQL chamada de armazenamento de chave/valor. As Tabelas do Azure permitem que um aplicativo armazene propriedades de vários tipos, como cadeia de caracteres, inteiros e datas. Assim, um aplicativo pode recuperar um grupo de propriedades fornecendo uma chave exclusiva para esse grupo. Embora operações complexas como junções não tenham suporte, as tabelas oferecem acesso rápido aos dados tipados. Elas também são bastante escalonáveis, com uma única tabela é possível manter volume extremamente grande de terabytes de dados. E correspondendo à sua simplicidade, fica menos caro usar tabelas do que o armazenamento relacional do Banco de Dados SQL.

**Cenários para tabelas**

Suponha que você queira criar um aplicativo do Azure que precise de acesso rápido aos dados digitados, talvez muitos deles, mas não precise executar consultas SQL complexas nesses dados. Por exemplo, imagine que você está criando um aplicativo de consumidor que precise armazenar informações de perfil do cliente de cada usuário. Seu aplicativo será muito popular, de modo que é preciso permitir muitos dados, mas você não fará muita coisa com esses dados a não ser armazená-los e depois recuperá-los de formas simples. Esse é exatamente o tipo de cenário no qual as Tabelas do Azure fazem sentido.

### <a name="blobs"></a>Blobs
![Blobs de armazenamento do Azure](./media/fundamentals-introduction-to-azure/StorageBlobsIntroNew.png)    
*Figura: os Blobs do Azure fornecem dados binários não estruturados.*  

Os Blobs do Azure (ou “Armazenamento de Blob" e apenas “Blobs de Armazenamento" são a mesma coisa) são projetados para armazenar dados binários não estruturados. Assim como as Tabelas, os Blobs fornecem armazenamento barato; além disso, um único blob pode ter tamanho de até 1 TB (um terabyte). Os aplicativos do Azure também podem usar unidades do Azure, que permitem aos blobs fornecer armazenamento persistente para um sistema de arquivos do Windows montado em uma instância do Azure. O aplicativo enxerga os arquivos comuns do Windows, mas o conteúdo é armazenado em um blob.

O Armazenamento de Blob é utilizado por muitos outros recursos do Azure (incluindo Máquinas Virtuais), de modo que pode, com certeza, dar conta também das cargas de trabalho usadas por você.

**Cenários para blobs**

Um aplicativo que armazena vídeos, arquivos massivos ou outras informações binárias pode usar blobs para armazenamento simples e barato. Os blobs são utilizados frequentemente em conjunção com outros serviços como a CDN (Rede de Distribuição de Conteúdo), sobre a qual falaremos posteriormente.  

### <a name="import--export"></a>Importar/Exportar
![Serviço Importar/Exportar do Azure](./media/fundamentals-introduction-to-azure/ImportExportIntroNew.png)  

*Figura: importação/exportação do Azure fornece a capacidade de enviar um disco rígido físico para o Azure, ou por meio dele, para importação ou exportação de dados em massa de forma mais rápida e econômica.*  

Em alguns casos, você deseja mover um grande volume dados para o Azure. Isso levaria muito tempo, talvez dias, além de usar muita largura de banda. Nesses casos, você pode usar a Importação/Exportação do Azure, que permite enviar discos rígidos SATA de 3,5” criptografados pelo Bitlocker diretamente para os datacenters do Azure, nos quais a Microsoft vai transferir os dados para armazenamento de blobs para você.  Após o carregamento ter sido concluído, a Microsoft envia as unidades de volta para você.  Você também pode solicitar que grandes volumes de dados do Armazenamento de Blob sejam exportados para discos rígidos e enviados de volta a você, por email.

**Cenários para importação/exportação**

* **Migração de Grandes Volumes de Dados** - toda vez que você tiver grandes volumes de dados (Terabytes) que deseje carregar para o Azure, o serviço de Importação/Exportação é, frequentemente, muito mais rápido e, talvez, mais barato que transferir esses dados pela Internet. Uma vez que os dados estejam em blobs, você pode processá-los em outros formatos como armazenamento em Tabela ou um Banco de Dados SQL.
* **Recuperação de dados arquivados** – você pode utilizar a função Importação/Exportação para fazer com que a Microsoft transfira grandes volumes de dados armazenados no Armazenamento de Blobs do Azure para um dispositivo de armazenamento que você envia e depois recebe esse mesmo dispositivo de volta em uma localização que você desejar. Já que isso levará algum tempo, essa não é uma boa opção para recuperação de desastres. Ela é uma opção melhor para dados arquivados, para os quais você não precisa de acesso rápido.

### <a name="file-service"></a>Serviço de arquivos
![Serviço de Arquivos do Azure](./media/fundamentals-introduction-to-azure/FileServiceIntroNew.png)    
*Figura: os serviços de arquivo do Azure fornecem caminhos SMB \\\\server\share a aplicativos executados na nuvem.*

No local, é comum ter grandes volumes de armazenamento de arquivos acessíveis pelo protocolo SMB utilizando um formato \\\\Server\share. O Azure agora conta com um serviço que permite que você utilize esse protocolo em nuvem. Os aplicativos sendo executados no Azure podem utilizar esse serviço para compartilhar arquivos entre VMs utilizando APIs com um sistema de arquivos familiar, como ReadFile e WriteFile. Além disso, os arquivos também podem ser acessados simultaneamente via uma interface REST, que permite que você acesse os compartilhamentos diretamente do local quando você também configurar uma rede virtual. Os Arquivos do Azure são criados sobre o serviço Blob, de modo que herdam a mesma disponibilidade, durabilidade, escalabilidade e redundância geográfica existentes no Armazenamento do Azure.

**Cenários para arquivos do Azure**

* **Migração de aplicativos existentes para nuvem** - é mais fácil fazer a migração para a nuvem de aplicativos do próprio local, que utilizam compartilhamentos para partilhar dados entre partes do aplicativo. Cada VM conecta-se ao compartilhamento de arquivo e então pode ler e gravar arquivos, exatamente como faria no caso de um compartilhamento de arquivo local.
* **Configurações de Aplicativo Compartilhado** - um padrão comum para aplicativos distribuídos é fazer com que os arquivos de configuração fiquem em um local centralizado, de onde possam ser acessados por diversas máquinas virtuais diferentes. Esses arquivos de configuração podem ser armazenados em um compartilhamento de Arquivo Azure e lido por todas as instâncias de aplicativo. As configurações também podem ser gerenciadas por meio da interface REST, que permite acesso global aos arquivos de configuração.
* **Compartilhamento de Diagnóstico** - você pode compartilhar e salvar arquivos de diagnóstico como logs, métricas e despejo de memória. Ter esses arquivos disponíveis tanto pela interface REST quanto pelo SMB (Bloco de Mensagens de Servidor) permite que os aplicativos utilizem uma variedade de ferramentas de análise para processar e analisar os dados de diagnóstico.
* **Desenv/Teste/Depuração** - quando desenvolvedores ou administradores estão trabalhando em máquinas virtuais na nuvem, frequentemente eles precisam de um conjunto de ferramentas ou utilitários. Instalar e distribuir esses utilitários em cada máquina virtual leva muito tempo. Com o Arquivos do Azure, um desenvolvedor ou administrador pode armazenar suas ferramentas favoritas em um compartilhamento de arquivos e conectar-se a elas a partir de qualquer máquina virtual.

## <a name="networking"></a>Rede
O Azure é executado hoje em muitos datacenters espalhados pelo mundo. Ao executar um aplicativo ou armazenar dados, você pode optar por usar um ou mais desses datacenters. Também é possível se conectar a esses datacenters de várias maneiras, utilizando os serviços abaixo.

### <a name="virtual-network"></a>Rede Virtual
![VirtualNetwork](./media/fundamentals-introduction-to-azure/VirtualNetworkIntroNew.png)   

*Figura: redes virtuais fornecem uma rede privada na nuvem, para que serviços diferentes possam se comunicar entre si ou com recursos locais, caso você configure uma conexão VPN entre locais.*  

Uma maneira útil de usar uma nuvem pública é tratá-la como uma extensão do seu próprio datacenter.

Como é possível criar VMs sob demanda e depois removê-las (e parar de pagar) quando elas não forem mais necessárias, você pode ter o poder da computação somente quando desejá-la. Já que Máquinas Virtuais do Azure permitem que você crie VMs executando SharePoint, Active Directory e outros softwares locais familiares, essa abordagem pode funcionar com os aplicativos que você já tem.

Mas para que isso seja realmente útil, convém que os usuários tratem esses aplicativos como se eles estivessem em execução no seu próprio datacenter. Isso é exatamente o que a Rede Virtual do Azure permite. Usando um dispositivo de gateway VPN, um administrador pode configurar uma rede privada virtual (VPN) entre sua rede local e suas VMs que são implantadas em uma rede virtual no Azure. Como você atribui seus próprios endereços IPv4 às VMs na nuvem, elas parecem estar na sua própria rede. Os usuários na sua organização podem acessar os aplicativos que essas VMs contêm como se estivessem sendo executados localmente.

Para obter mais informações sobre planejamento e criação de uma rede virtual que funciona para você, consulte [Rede Virtual](virtual-network/virtual-networks-overview.md).

### <a name="express-route"></a>Rota Expressa
![ExpressRoute](./media/fundamentals-introduction-to-azure/ExpressRouteIntroNew.png)   

*Figura: o ExpressRoute usa uma Rede Virtual do Azure, mas roteia as conexões por linhas dedicadas mais rápidas, em vez da Internet pública.*  

Se você precisa de mais largura de banda ou segurança do que a oferecida por uma conexão de Rede Virtual do Azure, considere o ExpressRoute. Em alguns casos, o ExpressRoute também pode economizar o seu dinheiro. Você ainda precisará de uma rede virtual no Azure, mas a conexão entre o Azure e seu site utiliza uma conexão dedicada que não passa pela Internet pública. Para utilizar esse serviço, você precisará de um contrato com um provedor de serviços de rede, ou então um provedor de troca.

Configurar uma conexão ExpressRoute exige mais tempo e planejamento, portanto, talvez seja melhor você começar utilizando um VPN site a site, para então migrar para uma conexão ExpressRoute.

Para obter mais informações sobre o ExpressRoute, consulte [Visão Geral Técnica do ExpressRoute](expressroute/expressroute-introduction.md).

### <a name="traffic-manager"></a>Gerenciador de Tráfego
![TrafficManager](./media/fundamentals-introduction-to-azure/TrafficManagerIntroNew.png)   

*Figura: o Gerenciador de Tráfego do Azure permite rotear o tráfego global para o seu serviço, com base em regras inteligentes.*

Se seu aplicativo do Azure estiver sendo executado em vários datacenters, você poderá usar o Gerenciador de Tráfego do Azure para rotear solicitações de usuários de maneira inteligente entre as instâncias do aplicativo. Você também pode rotear o tráfego para serviços que não estejam sendo executados no Azure, desde que possam ser acessados pela Internet.  

Um aplicativo do Azure com usuários em apenas uma única parte do mundo pode ser executado em apenas um datacenter do Azure. Entretanto, um aplicativo com usuários espalhados pelo mundo tem mais probabilidade de ser executado em vários datacenters, talvez até em todos eles. Nesta segunda situação, você enfrenta um problema: como você direciona usuários diretamente de forma inteligente às instâncias do aplicativo? Na maior parte do tempo, provavelmente você desejará que cada usuário acesse o datacenter mais próximo, pois é bem provável que este apresente o melhor tempo de resposta. Mas e se essa instância do aplicativo estiver sobrecarregada ou indisponível? Nesse caso, seria adequado direcionar as respectivas solicitações automaticamente para outro datacenter. O Gerenciador de Tráfego do Azure faz exatamente isso.

O proprietário de um aplicativo define regras que especificam como as solicitações dos usuários devem ser direcionadas para os datacenters e conta com o Gerenciador de Tráfego para aplicar essas regras. Normalmente, os usuários podem ser direcionados para o datacenter do Azure mais próximo, por exemplo, mas são enviados para outro quando o tempo de resposta do datacenter padrão excede o tempo de resposta de outros datacenters. Para aplicativos com muitos usuários distribuídos globalmente, é útil ter um serviço interno para lidar com problemas como esse.

O Gerenciador de Tráfego usa o DNS (Serviço de Nomes de Diretório) para direcionar os usuários aos pontos de extremidade do serviço, mas o tráfego não passa pelo gerenciador após o estabelecimento da conexão. Isso impede que o Gerenciador de Tráfego atue como um gargalo que tornará suas comunicações de serviço lentas.

## <a name="developer-services"></a>Serviços para Desenvolvedores
O Azure oferece uma série de ferramentas para auxiliar os desenvolvedores e profissionais de TI a manter os aplicativos na nuvem.  

### <a name="azure-sdk"></a>SDK do Azure
Em 2008, a primeira versão de pré-lançamento do Azure oferecia suporte apenas para desenvolvimento .NET. Hoje, no entanto, você pode criar aplicativos do Azure em praticamente qualquer linguagem. Atualmente, a Microsoft fornece SDKs específicos a cada linguagem para .NET, Java, PHP, Node. js, Ruby e Python. Também há um SDK geral do Azure que fornece suporte básico para qualquer linguagem, como C++.  

Estes SDKs vão ajudá-lo a desenvolver, implantar e gerenciar aplicativos do Azure. Eles estão disponíveis em [www.microsoftazure.com](https://azure.microsoft.com/downloads/) ou no GitHub e podem ser usados com o Visual Studio e o Eclipse. O Azure também oferece ferramentas de linha de comando que os desenvolvedores podem usar qualquer editor ou ambiente de desenvolvimento, incluindo ferramentas para implantar aplicativos no Azure em sistemas Linux e Macintosh.

Além de ajudar a desenvolver aplicativos Azure, estes SDKs também oferecem bibliotecas de cliente que ajudam você a criar software que use os serviços do Azure. Por exemplo, você pode criar um aplicativo que leia e grave blobs do Azure ou criar uma ferramenta que implante aplicativos do Azure por meio da interface de gerenciamento do Azure.

### <a name="visual-studio-team-services"></a>Visual Studio Team Services
O Visual Studio Team Services é um nome de marketing que abrange uma série de serviços que ajudam a desenvolver aplicativos no Azure.

Para evitar confusão, ele não fornece uma versão hospedada ou baseada em web do Visual Studio. Você ainda precisa de sua cópia do Visual Studio sendo executada localmente. Mas ela oferece muitas outras ferramentas, que podem ser muito úteis.

Ela inclui um sistema de controle do código-fonte hospedado chamado Team Foundation Service, que oferece um controle de versão e acompanhamento de itens de trabalho.  Você pode até mesmo utilizar o Git para controle de versão, se preferir. Além disso, você pode variar o sistema de controle do código-fonte que utiliza para cada projeto. Você pode criar um número ilimitado de projetos de equipe privados, de qualquer lugar do mundo.  

O Visual Studio Team Services fornece um serviço de teste de carga. Você pode executar testes de carga criados no Visual Studio nas VMs presentes na nuvem. Você especifica o número total de usuários com os quais você deseja realizar o teste de carga e o Visual Studio Team Services determinará automaticamente quantos agentes são necessários, criará as máquinas virtuais necessárias e executará os testes de carga. Se você é um assinante do MSDN, você recebe milhares de minutos de usuário para teste de carga a cada mês.

O Visual Studio Team Services também dá suporte ao desenvolvimento rápido com recursos como compilações de integração contínua, quadros Kanban e salas de equipe virtual.

**Cenários do Visual Studio Team Services**

O Visual Studio Team Services é uma ótima opção para empresas que precisam colaborar em todo o mundo e ainda não têm a infraestrutura necessária para fazer isso. Você pode concluir a configuração em minutos, escolher um sistema de controle do código-fonte e iniciar a gravação do código e a compilação no mesmo dia.  A nova equipe oferece um local para coordenação e colaboração, enquanto as ferramentas adicionais oferecem a análise necessária para testar e ajustar rapidamente o seu aplicativo.

Porém, as organizações que já têm um sistema local podem testar novos projetos no Visual Studio Team Services para ver se desse modo o sistema é mais eficiente.   

### <a name="application-insights"></a>Application Insights
![Application Insights](./media/fundamentals-introduction-to-azure/ApplicationInsights.png)  

*Figura: o Application Insights monitora o desempenho e o uso de seu aplicativo ativo da Web ou de dispositivo.*

Ao publicar seu aplicativo, seja ele executado em dispositivos móveis, em desktops ou em navegadores da Web, o Application Insights informa o desempenho dele e o que os usuários estão fazendo com ele. Isso manterá uma contagem de falhas e de respostas lentas, alertará se os números cruzarem limites inaceitáveis e ajudará você a diagnosticar quaisquer problemas.

Ao desenvolver um novo recurso, planeje a medição do êxito dele com os usuários. Analisando padrões de uso, você entende o que funciona melhor para seus clientes e aprimora seu aplicativo em todos os ciclos de desenvolvimento.

Embora esteja hospedado no Azure, o Application Insights funciona para uma grande e crescente variedade de aplicativos, no Azure e fora dele. Ambos os aplicativos Web J2EE e ASP.NET são abordados, bem como aplicativos para iOS, para Android, para OSX e para o Windows. A telemetria é enviada de um SDK criado com o aplicativo, a ser analisada e exibida no serviço do Application Insights no Azure.

Se você quiser uma análise mais especializada, exporte o fluxo de telemetria para um banco de dados ou para o Power BI ou para todas as outras ferramentas.

**Cenários do Application Insights**

Você está desenvolvendo um aplicativo. Pode ser um aplicativo Web ou um aplicativo de dispositivo ou um aplicativo de dispositivo com um back-end da Web.

* Ajuste o desempenho do seu aplicativo depois que ele for publicado ou enquanto ele estiver no teste de carga.  O Application Insights agrega a telemetria de todas as instâncias instaladas e apresenta gráficos de tempos de resposta, contagens de exceção e de solicitação, tempos de resposta de dependência e outros indicadores de desempenho. Isso ajudará você a ajustar o desempenho do aplicativo. Você pode inserir código para relatar dados mais específicos, se forem necessários.
* Detecte e diagnostique problemas em seu aplicativo ativo. Você poderá receber alertas por email se os indicadores de desempenho cruzarem os limites aceitáveis. Você pode investigar sessões de usuário específicas, por exemplo, para ver a solicitação que causou uma exceção.
* Acompanhe o uso para avaliar o sucesso de cada novo recurso. Quando você projetar uma nova história de usuário, planeje medir o quanto ele será usado e se os usuários alcançarão suas metas estabelecidas. O Application Insights fornece dados de uso básicos, como modos de exibição de página da Web, e você pode inserir um código para acompanhar a experiência do usuário em mais detalhes.

### <a name="automation"></a>Automação
Ninguém gosta de desperdiçar tempo realizando os mesmos processos manuais repetidamente. A Automação do Azure oferece um modo para você criar, monitorar, gerenciar e implantar recursos em seu ambiente Azure.  

A automação utiliza "runbooks", que por sua vez utilizam fluxos de trabalho do Windows PowerShell (em vez de utilizar apenas o PowerShell comum) de modo oculto. Os runbooks são destinados à execução sem interação com o usuário. Os fluxos de trabalho do PowerShell permitem que o estado de um script seja salvo em pontos de verificação durante todo o percurso. Então, se uma falha ocorrer, você não precisa começar um script do zero. Você pode recomeçar desde o último ponto de verificação. Isso poupa você de muito trabalho, ao tentar fazer com que o script dê conta de toda falha possível.

**Cenários para automação**

A Automação do Azure é uma boa escolha para automatizar as tarefas manuais, com execução por longos períodos, passíveis de erro e frequentemente repetidas no Azure.

### <a name="api-management"></a>Gerenciamento da API
Criar e publicar Interfaces Programadoras de Aplicativo (APIs) na Internet é um modo comum de fornecer serviços a aplicativos. Se esses serviços puderem ser revendidos (por exemplo, informações sobre o clima), uma organização pode permitir que outras partes - terceiros - tenham acesso a esses mesmos serviços mediante uma taxa. À medida que dimensionar para dar suporte a mais parceiros, normalmente, você precisará otimizar e controlar o acesso.  Alguns parceiros podem até precisar dos dados em um formato diferente.

O Gerenciamento da API do Azure torna fácil para as organizações publicar APIs para parceiros, funcionários e desenvolvedores terceirizados, de modo seguro e dimensionado. Ele oferece um ponto de extremidade de API diferente e age como um proxy para chamar o ponto de extremidade real enquanto fornece serviços como caching, transformação, limitação, controle de acesso e agregação de análise.

**Cenários para Gerenciamento de API**

Digamos que sua empresa tenha um conjunto de dispositivos que todos precisam chamar de volta um serviço central para obter dados -- por exemplo, uma empresa de transporte que tem dispositivos em cada caminhão presente nas estradas.  Com certeza, a empresa desejará configurar um sistema para acompanhar seus próprios caminhões de modo que possa, confiavelmente, predizer e atualizar os horários de entrega. Ela poderá saber quantos caminhões tem e planejar tudo adequadamente.  Cada caminhão precisará de um dispositivo que liga de volta para um local central, informando seus dados de localização e velocidade, podendo informar também outros.

Um cliente da empresa de transporte provavelmente também se beneficiaria de receber esses dados de localização.  O cliente poderia utilizá-los para saber até que distância os produtos precisam ser levados, onde esses produtos ficam presos e quanto se gasta pelo tráfego por determinadas rotas (se combinado com o valor-base pago pelo transporte). Se a empresa de transporte já agrega esses dados, muitos clientes podem pagar por eles.  Mas se esse é o caso, a empresa de transporte precisa fornecer um meio para fornecer os dados aos clientes. Após oferecerem acesso aos clientes, eles podem não ter controle sobre a frequência com que os dados são consultados. Eles precisarão estabelecer regras sobre quem pode acessar quais dados. Todas essas regras precisariam ser criadas em sua API externa. É aqui que o Gerenciamento da API pode ajudar.  

## <a name="identity-and-access"></a>Identidade e Acesso
Trabalhar com identidade faz parte da maioria dos aplicativos. Saber quem um usuário é permite que um aplicativo decida como deve interagir com ele. O Azure fornece serviços para ajudar a acompanhar a identidade, assim como integrá-la a locais de armazenamento de identidade que você já pode estar usando.

### <a name="active-directory"></a>Active Directory
Assim como a maioria dos serviços de diretório, o Active Directory do Azure armazena informações sobre usuários e as organizações às quais eles pertencem. Com ele, os usuários podem fazer logon e recebem tokens que podem apresentar aos aplicativos para provar sua identidade. Ele também permite a sincronização de informações do usuário, sendo executado em locais de sua rede local. Embora os mecanismos e os formatos de dados usados pelo Active Directory do Azure não sejam idênticos aos usados no Active Directory do Windows Server, as funções que ele executa são bastante semelhantes.

É importante entender que o Active Directory do Azure foi desenvolvido basicamente para uso pelos aplicativos em nuvem. Ele pode ser usado pelos aplicativos executados no Azure, por exemplo, em outras plataformas na nuvem. Ele também é usado pelos próprios aplicativos em nuvem da Microsoft, como os do Office 365. No entanto, se desejar estender o datacenter para a nuvem usando as Máquinas Virtuais do Azure e a Rede Virtual do Azure, o Active Directory do Azure não é a opção certa. Em vez disso, será conveniente executar o Active Directory do Windows Server nas Máquinas Virtuais.

Para permitir que os aplicativos acessem as informações que ele contém, o Active Directory do Azure fornece uma API RESTful chamada Gráfico do Active Directory do Azure. Essa API permite que os aplicativos que são executados em qualquer plataforma acessem objetos de diretório e os relacionamentos entre eles.  Por exemplo, um aplicativo autorizado pode usar essa API para saber mais sobre um usuário, os grupos aos quais ele pertence, entre outras informações. Os aplicativos também podem enxergar os relacionamentos entre os usuários (o gráfico social), o que os permite trabalhar de modo mais inteligente com as conexões entre as pessoas.

Outro recurso desse serviço, o Active Directory Access Control do Azure, torna mais fácil para um aplicativo aceitar informações de identidade do Facebook, Google, Windows Live ID e outros provedores de identidade conhecidos. Em vez de exigir que o aplicativo entenda os diversos formatos de dados e protocolos usados por cada um desses provedores, o Access Control converte todos eles em um único formato comum. Ele também permite que um aplicativo aceite logons de um ou mais domínios Active Directory. Por exemplo, um fornecedor que oferece um aplicativo SaaS pode usar o Active Directory Access Control do Azure para fornecer aos usuários em cada um de seus clientes um logon único ao aplicativo.

Os serviços de diretório são um suporte importante de uma computação local. Não é de se surpreender que eles também sejam importantes na nuvem.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
![Autenticação Multifator do Azure](./media/fundamentals-introduction-to-azure/MFAIntroNew.png)   

*Figura: o Multi-Factor Authentication fornece a funcionalidade para seu aplicativo verificar mais de uma forma de identificação*

A segurança é sempre importante. A Multi-Factor Authentication (MFA) ajuda a garantir que somente os próprios usuários acessem suas contas. A MFA (também conhecida como autenticação em dois fatores ou "2FA") exige que os usuários forneçam dois desses três métodos de verificação de identidade, para logons e transações realizados por clientes.

* Algo que você sabe (normalmente, uma senha)
* Algo que você tem (um dispositivo confiável que não pode ser facilmente clonado, como um telefone)
* Algo seu (biometria)

Desse modo, quando um usuário realiza seu logon, você pode solicitar que ele também verifique sua identidade com um aplicativo móvel, uma ligação telefônica ou uma mensagem de texto em combinação com sua senha. Por padrão, o Active Directory do Azure dá suporte ao uso de senhas como seu único método de autenticação para logon de usuário. Você pode utilizar a Autenticação Multifator juntamente com o Active Directory do Azure ou com diretórios e aplicativos personalizados, pelo uso do SDK de Autenticação Multifator. Você também pode utilizá-lo em conjunto com aplicativos locais, por meio do uso do servidor Multi-Factor Authentication.

**Cenários para MFA**

A proteção para logon em contas confidenciais, como logons bancários e acesso a código-fonte, nos quais uma entrada não autorizada poderia ter um elevado custo financeiro ou de propriedade intelectual.   

## <a name="mobile"></a>Móvel
Se você estiver criando um aplicativo para um dispositivo móvel, o Azure pode ajudar a armazenar dados na nuvem, autenticar usuários e enviar notificações por push sem precisar que você precise escrever uma grande quantidade de código personalizado.

Embora certamente seja possível criar back-end de um aplicativo móvel usando Máquinas Virtuais, Serviços de Nuvem ou Aplicativos Web, você pode gastar muito menos tempo gravando os componentes subjacentes do serviço ao usar os serviços do Azure.

### <a name="mobile-apps"></a>Aplicativos Móveis
![Aplicativos Móveis](./media/fundamentals-introduction-to-azure/MobileServicesIntroNew.png)

*Figura: os Aplicativos Móveis oferecem funcionalidades normalmente exigidas por aplicativos que fazem interface com dispositivos móveis.*

Os Aplicativos Móveis do Azure fornecem muitas funções úteis que podem poupar tempo ao criar um back-end para um aplicativo Móvel. Isso permite que você faça um provisionamento simples e gerenciamento dos dados armazenados em um Banco de Dados SQL. Com o código do servidor, você pode facilmente usar opções de armazenamento de dados como armazenamento de blob ou MongoDB. Os Aplicativos Móveis dão suporte a notificações, apesar de, em determinados casos, ser possível usar também os Hubs de Notificação conforme descrito a seguir.  O serviço também conta com uma API REST para o qual o seu aplicativo móvel pode ligar, para realizar o trabalho necessário. Os Aplicativos Móveis também oferecem a capacidade de autenticar usuários por meio da Microsoft e do Active Directory, bem como por meio de outros provedores de identidade, como Facebook, Twitter e Google.   

Você pode utilizar outros Serviços do Azure, como Barramento de Serviço e funções de trabalho, além de conectar-se a sistemas locais. Você pode até mesmo utilizar Add-Ons terceirizados da Azure Store (como SendGrid para email) para oferecer funcionalidades adicionais.

As bibliotecas de cliente nativo para Android, iOS, HTML/JavaScript, Windows Phone e Windows Store facilitam desenvolver conteúdo para aplicativos de todas as principais plataformas móveis. Uma API REST permite que você utilize dados de Serviços Móveis e funcionalidade de autenticação com aplicativos em diferentes plataformas. Um único serviço móvel pode dar suporte a múltiplos aplicativos clientes, de modo que você possa oferecer uma experiência de usuário consistente em todos os dispositivos.

Como o Azure já dá suporte a transmissão de dados em massa, você pode gerenciar o tráfego conforme seu aplicativo se torna mais popular.  Monitoramento e registro têm suporte para auxiliar na resolução de problemas e no gerenciamento do desempenho.

### <a name="notification-hubs"></a>Hubs de Notificação
![NotificationHubs](./media/fundamentals-introduction-to-azure/NotificationHubsIntroNew.png)  

*Figura: os Hubs de Notificação oferecem funcionalidades normalmente exigidas por aplicativos que fazem interface com dispositivos móveis.*

Embora você possa gravar um código para realizar notificações nos Aplicativos Móveis do Azure, os Hubs de Notificação são otimizados para difundir milhões de notificações por push em questão de minutos.  Você não precisa se preocupar com detalhes como operadora móvel ou fabricante de aparelhos. Você pode visar indivíduos ou milhões de usuários com uma única chamada à API.

Os Hubs de Notificação são projetados para funcionar com qualquer back-end. Você pode utilizar os Aplicativos Móveis do Azure, um back-end personalizado na nuvem executado em qualquer provedor ou em um back-end local.

**Cenários para hubs de notificação** Se você estava programando um jogo para dispositivo móvel em que os jogadores se alternam em turnos, pode ser necessário notificar o jogador 2 que o jogador 1 terminou seu turno. Se isso é tudo o que precisa fazer, basta usar os Aplicativos Móveis. Mas se você tem 100.000 usuários utilizando seu jogo e quer enviar uma oferta gratuita sensível ao tempo para todos, os hubs de notificação são a melhor escolha.

Você pode enviar notícias recentes, eventos esportivos e notificações de anúncio de produtos para milhões de usuários com baixa latência. As empresas podem notificar seus funcionários sobre novas comunicações sensíveis ao tempo, como clientes potenciais de vendas, de modo que os funcionários não precisarão verificar constantemente seu email nem outras aplicações para permanecer informados. Você também pode enviar senhas de uso único exigidas para Multi-Factor Authentication.

## <a name="back-up"></a>Backup
Toda empresa precisa fazer backup e restauração de dados. Você pode utilizar o Azure para fazer backup e restaurar os dados de seu aplicativo, seja ele em nuvem ou no próprio local. O Azure oferece opções diferentes para ajudar, dependendo do tipo de backup.

### <a name="site-recovery"></a>Recuperação de Site
O Azure Site Recovery (anteriormente Gerenciador de Recuperação Hyper-V) pode ajudar você a proteger aplicativos importantes, coordenando a replicação e recuperação entre locais. O Site Recovery fornece a capacidade de proteger aplicativos com base em Hyper-V, VMware ou SAN em seu próprio site secundário, no site de um hoster ou no Azure e elimina a despesa e a complexidade da criação e gerenciamento da sua própria localização secundário. O Azure criptografa os dados e comunicações e você tem a opção de habilitar a criptografia também para os dados em repouso.

Ele monitora continuamente a condição de seus serviços e ajuda a automatizar a recuperação ordenada dos serviços no caso de interrupção no funcionamento do site no datacenter primário. Máquinas virtuais podem ser trazidas de modo organizado para ajudar a restaurar o serviço rapidamente, mesmo para cargas de trabalho complexas e multicamadas.

O Site Recovery utiliza tecnologias existentes como Réplica do Hyper-V, System Center e SQL Server Always On. Verifique [Visão geral do Azure Site Recovery](site-recovery/site-recovery-overview.md) para obter mais detalhes.

### <a name="azure-backup"></a>Serviço de Backup do Azure
![Serviço de Backup do Azure](./media/fundamentals-introduction-to-azure/AzureBackupIntroNew.png)  

*Figura: o Backup do Azure realiza o backup dos dados de Windows Servers locais para a nuvem.*  

O Backup do Azure realiza o backup dos dados em servidores locais que executam o Windows Server na nuvem. Você pode gerenciar seus backups diretamente a partir das ferramentas de backup no Windows Server 2012, Windows Server 2012 Essentials ou System Center 2012 - Data Protection Manager. Alternativamente, você pode utilizar um agente de backup especializado.

Os dados ficam mais seguros porque os backups são criptografados antes da transmissão e armazenados criptografados no Azure, protegidos por um certificado carregado por você. O serviço utiliza os mesmos dados redundantes e altamente disponíveis encontrados no Armazenamento do Azure.  Você pode realizar backup de arquivos e pastas regularmente ou imediatamente, realizando esses backups de modo completo ou incremental. Após realizar o backup dos dados para a nuvem, os usuários autorizados podem fazer facilmente a recuperação dos backups para qualquer servidor. Ele também oferece políticas configuráveis de retenção, compressão, transferência de dados e limitação da transferência de dados, de modo que você possa gerenciar o custo de armazenar e transferir dados.

**Cenários para backup do Azure**

Se você já utiliza o Windows Server ou System Center, o Serviço de Backup do Azure é uma solução natural para suporte do sistema de arquivos de seus servidores, máquinas virtuais e bancos de dados de SQL Server.  Essa solução funciona com arquivos criptografados, esparsos e comprimidos. Há algumas limitações, de modo que você deve [verificar os pré-requisitos do Serviço de Backup do Azure](http://technet.microsoft.com/library/dn296608.aspx) primeiro.

## <a name="messaging-and-integration"></a>Mensagens e integração
Não importa o que esteja fazendo, o código frequentemente precisa interagir com outro código.  Em algumas situações, tudo o que é necessário é uma mensagem básica enfileirada. Em outros casos, são necessárias interações mais complexas. O Azure tem algumas maneiras diferentes de resolver esses problemas. A Figura 5 ilustra as opções.

### <a name="queues"></a>Filas
![Retransmissão do Barramento de Serviço do Azure](./media/fundamentals-introduction-to-azure/QueuesIntroNew.png)

*Figura: as filas permitem o acoplamento solto entre peças de um aplicativo e facilitam a colocação em escala.*  

O enfileiramento é uma ideia simples: um aplicativo coloca uma mensagem na fila e essa mensagem, por fim, é lida por outro aplicativo. Se seu aplicativo precisar apenas desse serviço direto, as Filas do Azure podem ser a melhor opção.

Devido a como o Azure cresceu ao longo do tempo, as Filas de Armazenamento do Azure e Filas do Barramento de Serviço fornecem serviços de enfileiramento similares. Os motivos pelos quais você desejaria utilizar um no lugar do outro são explicadas num folheto bastante técnico [Filas do Azure e filas do Barramento de Serviço – comparadas e contrastadas](http://msdn.microsoft.com/library/azure/hh767287.aspx).  Na maioria dos cenários, qualquer uma dessas opções funcionará.

**Cenários para fila**

Um uso comum das filas hoje em dia é para permitir que uma instância de função Web comunique-se com uma instância de função de trabalho dentro do mesmo aplicativo dos Serviços de Nuvem.

Por exemplo, suponha que você crie um aplicativo do Azure para compartilhamento de vídeo. O aplicativo é composto por código PHP em execução em uma função web que permite aos usuários carregar e assistir a vídeos, juntamente com uma função de trabalho implementada em C#, que converte o vídeo carregado em vários formatos.

Quando uma instância da função Web obtém um novo vídeo de um usuário, ela pode armazenar o vídeo em um blob e, em seguida, enviar uma mensagem a uma função de trabalho por uma fila, informando-a onde encontrar esse novo vídeo. Uma instância da função de trabalho (não importa qual) lerá a mensagem na fila e realizará as conversões de vídeo necessárias em segundo plano.

A estruturação de um aplicativo dessa maneira permite o processamento assíncrono, além de facilitar o dimensionamento do aplicativo, pois o número de instâncias da função Web e de instâncias da função de trabalho pode ser diversificado de modo independente. Você também pode utilizar o tamanho da fila como um gatilho para dimensionar o número de funções de trabalho, para mais ou menos. Se está alto demais, você acrescenta mais funções. Quando fica mais baixo, você pode reduzir o número de funções em execução para economizar dinheiro.  

Você pode usar esse mesmo padrão entre vários componentes diferentes do seu aplicativo, mesmo que eles não utilizem funções de trabalho e da Web.  Isso permite o aumento e a redução dos componentes em ambos os lados da fila, conforme as exigências de demanda e tempo de processamento.

### <a name="service-bus"></a>Barramento de Serviço
Estejam eles em execução na nuvem, no data center, em um dispositivo móvel ou em qualquer outro lugar, os aplicativos precisam interagir. O objetivo do Barramento de Serviço do Azure é permitir que os aplicativos executados quase em todos os lugares troquem dados.

Além das filas (um a um) descritas anteriormente, o Barramento de Serviço também oferece outros métodos de comunicação.

#### <a name="service-bus-relay"></a>Retransmissão do Service Bus
![Retransmissão do Barramento de Serviço do Azure](./media/fundamentals-introduction-to-azure/ServiceBusRelayIntroNew.png)

*Figura: a Retransmissão do Barramento de Serviço permite a comunicação entre os aplicativos em diferentes lados de um firewall.*

O Barramento de Serviço permite a comunicação direta por meio de seu serviço de retransmissão, fornecendo um meio seguro de interagir por meio de firewalls. As retransmissões do Barramento de Serviço permitem que os aplicativos se comuniquem trocando mensagens por meio de um ponto de extremidade hospedado na nuvem, em vez de localmente.

**Cenários para retransmissão do Barramento de Serviço**

Os aplicativos que se comunicam por meio do Barramento de Serviço podem ser aplicativos ou softwares do Azure em execução em alguma outra plataforma na nuvem. Entretanto, eles também podem ser aplicativos que são executados fora da nuvem. Por exemplo, pense em uma companhia aérea que implementa serviços de reserva em computadores dentro de seu próprio datacenter. A companhia precisa expor esses serviços a muitos clientes, incluindo check-in em quiosques de aeroportos, terminais de agente de reserva e talvez, até mesmo, telefones de clientes. Ela pode usar o Barramento de Serviço para fazer isso, criando interações acopladas imprecisamente entre os vários aplicativos.

#### <a name="service-bus-topics-and-subscriptions"></a>Tópicos e assinaturas do Barramento de Serviço
![Tópicos do Barramento de Serviço do Azure](./media/fundamentals-introduction-to-azure/ServiceBusTopicsSubsIntroNew.png)   
 *Figura: os tópicos do Barramento de Serviço permitem que múltiplos aplicativos publiquem mensagens e que outros aplicativos façam uma assinatura para receber mensagens que atendam a um critério específico.*

O Barramento de Serviço oferece um mecanismo de publicação e assinatura chamado Tópicos e Assinaturas. Com publicação e assinatura, um aplicativo pode enviar mensagens para um tópico enquanto outros aplicativos podem criar assinaturas para esse tópico. Isso permite comunicação de um para muitos entre um conjunto de aplicativos, permitindo que a mesma mensagem seja lida por vários destinatários.

**Cenários dos Tópicos e assinaturas do Barramento de Serviço**

Em qualquer momento no qual você realiza a configuração e no qual existem muitas mensagens, todas importantes, mas no qual diversos sistemas downstream só precisam escutar subconjuntos diferentes dessas comunicações, o Tópico e Assinaturas do Barramento de Serviço são boas opções.

### <a name="biztalk-services"></a>Serviços do BizTalk
![Serviços do BizTalk](./media/fundamentals-introduction-to-azure/BizTalkServicesIntroNew.png)   
 *Figura: serviços BizTalk fornecem a capacidade de transformar formatos de mensagens XML na nuvem.*

Algumas vezes, você precisa conectar sistemas que comunicam-se utilizando diferentes formatos de mensagens. É comum que empresas tenham esquemas de bancos de dados e formatos de envio de mensagens XML diferentes, mesmo quando há um padrão comum disponível. Em vez de programar uma grande quantidade de código personalizado, você pode utilizar o BizTalk Server localmente para integrar diversos sistemas.  O Serviços BizTalk do Azure fornece o mesmo tipo de serviço, mas na nuvem. Você pode pagar apenas pelo que utiliza, sem se preocupar com dimensionamento, como faria em serviços locais.

**Cenários dos serviços BizTalk**

As interações entre empresas (B2B) normalmente requerem esse tipo de conversão.  Por exemplo, uma empresa que fabrica aviões precisa solicitar peças de seus diversos fornecedores. Essa empresa terá muitos fornecedores de peças.  Esses pedidos deverão ser automatizados, para que sejam enviados diretamente dos sistemas dos fabricantes de aviões para os sistemas dos fornecedores.  Nenhuma das empresas deseja modificar seus sistemas e formatos de mensagem principais, sendo muito improvável que esses formatos sejam os mesmos. Os Serviços BizTalk podem transportar mensagens e traduzi-las entre os novos formatos, em ambos os sentidos. O fabricante de aviões pode fazer o trabalho da tradução ou os diversos fornecedores podem fazê-lo, dependendo de quem deseja ter mais controle e da quantidade de tradução necessária.     

## <a name="compute-assistance"></a>Assistência de Computação
O Azure oferece assistência para serviços que não precisam ser realizados todo o tempo.  

### <a name="scheduler"></a>Agendador
![Agendador do Azure](./media/fundamentals-introduction-to-azure/SchedulerIntroNew.png)   
*Figura: o agendador do Azure oferece um meio de agendar trabalhos em um horário específico, por uma duração específica.*

Alguns aplicativos só precisam ser executados em um determinado momento. No Azure, você pode poupar dinheiro com esse tipo de aplicativo em vez de apenas deixar um aplicativo ser executado 24x7, esperando que os dados sejam processados. O Agendador do Azure permite que você faça o agendamento de quando um aplicativo deve ser executado, com base no intervalo de tempo de um calendário. Ele é confiável e verificará se um processo está em execução, mesmo se houver falhas de rede, computador e datacenter. Você utiliza o Agendador de API REST para gerenciar essas ações.

Quando um alarme agendado é disparado, o Agendador envia mensagens HTTP ou HTTPS para um ponto de extremidade específico, ou pode colocar uma mensagem em uma fila de armazenamento.  Assim, você precisa garantir que seu aplicativo tenha um ponto de extremidade acessível, ou então que esse aplicativo faça o monitoramento de uma fila de armazenamento. Então, uma vez que ele receber a mensagem, poderá realizar qualquer ação para a qual estiver programado.

**Cenários para agendador**

* Ações de aplicativo recorrentes: como exemplo, um serviço pode obter dados periodicamente do twitter e reunir esses dados em um feed regular.
* Manutenção diária: processamento de log ou remoção, realização de backups e outras tarefas agendamento intermitentes.
* Tarefas noturnas.
* Tarefas de aplicativos web como redução diária de logs, realização de backups e outras tarefas de manutenção. Um administrador pode escolher realizar o backup de seu banco de dados à 1h da manhã pelos próximos nove meses, por exemplo.

A API do Agendador permite criar, atualizar, excluir, exibir e gerenciar coleções de trabalhos e trabalhos agendados, programaticamente.

## <a name="performance"></a>Desempenho
O desempenho é sempre importante para um aplicativo. Os aplicativos tendem a acessar os mesmos dados repetidamente. Uma maneira de aprimorar o desempenho é manter uma cópia desses dados mais perto do aplicativo, minimizando o tempo necessário para recuperá-los. O Azure fornece diferentes serviços para realizar essa tarefa.

### <a name="azure-caching"></a>Cache do Azure
![Cache do Azure](./media/fundamentals-introduction-to-azure/AzureCacheIntroNew.png)   
 **Figura: um aplicativo do Azure pode armazenar em cache dados na memória e até mesmo dividi-los em muitas funções de trabalho**

Acessar dados armazenados em algum dos serviços de gerenciamento de dados do Azure (Banco de dados SQL, Tabelas ou Blobs) é muito rápido. Porém, acessar dados armazenados na memória é ainda mais rápido. Por esse motivo, manter uma cópia dos dados acessados frequentemente na memória pode aumentar o desempenho do aplicativo. É possível usar o Caching na memória do Azure para fazer isso.

Um aplicativo dos Serviços de Nuvem pode armazenar dados nesse cache e recuperá-los diretamente sem precisar acessar o armazenamento persistente. O cache pode ser mantido dentro das VMs de seu aplicativo, ou então ser fornecido por VMs dedicadas exclusivamente a caching. Em ambos os casos, o cache pode ser distribuído, com os dados nele contidos espalhados por várias VMs em um datacenter do Azure.

O Azure tem um número de tecnologias de cache diferentes que mudaram ao longo do tempo. Na ordem em que elas foram introduzidas, há um cache compartilhado, em função, gerenciado e Redis. O cache compartilhado é uma tecnologia mais antiga e você não deve criar novas implementações com ele. O Cache Gerenciado tem os as mesmas características do Cache na Função, mas como serviço gerenciado fora do Portal de Gerenciamento do Azure. O Cache Redis está em modo de visualização. A implementação do Redis tem um número maior de recursos e é recomendado ao programar um novo código de caching.

**Cenários para cache do Azure**

Um aplicativo que lê repetidamente um catálogo de produtos pode se beneficiar do uso desse tipo de caching, por exemplo, uma vez que os dados necessários serão disponibilizados mais rapidamente. A tecnologia também oferece suporte ao bloqueio, permitindo que ele seja usado com dados de leitura/gravação, bem como somente leitura. E os aplicativos ASP.NET podem usar o serviço para armazenar dados da sessão com apenas uma alteração na configuração.

### <a name="content-delivery-network"></a>Rede de Distribuição de Conteúdo
![CDN do Azure](./media/fundamentals-introduction-to-azure/CDNIntroNew.png)   
 **Figura: cópias de um blob podem ser armazenadas em cache em sites ao redor do mundo.**

Suponha que você precise armazenar dados de blob que serão acessados por usuários no mundo todo. Talvez seja um vídeo do último jogo da Copa do Mundo, por exemplo, ou atualizações de driver, ou um e-book popular. Armazenar uma cópia dos dados em vários datacenters do Azure ajudará, mas se houver muitos usuários, provavelmente não será suficiente. Para um desempenho ainda melhor, você pode usar a CDN do Azure.

A CDN tem dezenas de sites pelo mundo, cada um capaz de armazenar cópias dos blobs do Azure. Na primeira vez que um usuário em alguma parte do mundo acessar um blob específico, as informações que ele contém serão copiadas de um datacenter do Azure em um armazenamento CDN local na região geográfica. Depois disso, os acessos dessa parte do mundo usarão a cópia blob armazenada em cache na CDN - eles não precisarão fazer todo o caminho até o datacenter mais próximo do Azure. O resultado é um acesso mais rápido aos dados frequentemente acessados por usuários, em qualquer lugar do mundo.

**Cenários para CDN**

É comum utilizar CDN com Serviços de Mídia para transmitir vídeo para todo o mundo. Vídeo geralmente significa arquivos grandes, que exigem muita largura de banda.  Falamos sobre os Serviços de Mídia em outra parte desta página.

## <a name="big-data-and-big-compute"></a>Big Data e Computação de Grande Porte
### <a name="hdinsight-hadoop"></a>HDInsight (Hadoop)
![HDInsight](./media/fundamentals-introduction-to-azure/HDInsightIntroNew.png)   
 **Figura: o HDInsight ajuda no processamento em massa de grandes volumes de dados**

Por muitos anos, a análise de dados em massa foi feita em dados relacionais armazenados em um data warehouse criada com um DBMS relacional. Esse tipo de análise empresarial continua sendo importante e ainda será por um longo tempo. Mas e se os dados que você deseja analisar são tão grandes que os bancos de dados relacionais não podem lidar com eles? E suponha que os dados não sejam relacionais? Eles podem ser logs de servidor em um datacenter, por exemplo, ou dados de eventos históricos de sensores, ou algo parecido. Em casos assim, você tem o que é conhecido como problema de big data. É necessária outra abordagem.

A tecnologia dominante de hoje para analisar big data é o Hadoop. Um projeto de software livre da Apache, essa tecnologia armazena dados usando o HDFS (Hadoop Distributed File System) e permite que os desenvolvedores criem tarefas MapReduce para analisar esses dados. O HDFS distribui os dados por vários servidores e, em seguida, executa partes da tarefa MapReduce em cada um, deixando que o big data sejam processado paralelamente.

HDInsight é o nome do serviço do baseado em Apache Hadoop do Azure. O HDInsight permite que o HDFS armazene dados no cluster e distribua-os em várias VMs. Ele também dissemina a lógica de um trabalho de MapReduce entre as VMs. Assim como no Hadoop local, os dados são processados localmente (a lógica e os dados nos quais ela trabalha estão na mesma VM) e em paralelo para melhor desempenho. O HDInsight também pode armazenar dados no Azure Storage Vault (ASV), que usa os blobs.  O ASV permite que você economize dinheiro, pois você pode excluir o cluster HDInsight quando não estiver em uso ainda mantendo seus dados na nuvem.

O HDinsight oferece suporte a outros componentes do ecossistema do Hadoop, incluindo Hive e Pig. A Microsoft também criou componentes que tornam mais fácil trabalhar com dados produzidos pelo HDInsight usando as ferramentas de BI tradicionais, como o adaptador HiveODBC e o Explorador de Dados, que funcionam com o Excel.

### <a name="high-performance-computing-big-compute"></a>HPC (Computação de Alto Desempenho) - Computação de Grande Porte
Uma das maneiras mais atraentes de usar uma plataforma na nuvem é para HPC (computação de alto desempenho) e outros aplicativos de "Computação de Grande Porte". Exemplos incluem aplicativos de engenharia especializada, criados para usar MPI (Message Passing Interface), que é padrão da indústria, além dos chamados aplicativos embaraçosamente paralelos, como os modelos de risco financeiro.

A essência da Computação de Grande Porte é executar código em várias máquinas ao mesmo tempo. No Azure, isso significa executar muitas máquinas virtuais simultaneamente, todas trabalhando paralelamente para resolver algum problema. Fazer isso exige algum modo de trabalhar com recursos e agendar aplicativos, ou seja, distribuir o trabalho deles por estas instâncias. O Microsoft HPC Pack gratuito e outras soluções de cluster de computação podem funcionar bem no Azure, aproveitando serviços de infraestrutura e de computação do Azure para acrescentar capacidade sob demanda a um cluster de computação local, ou então executar aplicativos de Computação de Grande Porte totalmente na nuvem.

O Azure fornece uma gama de tamanhos de instância de VM com diferentes configurações de núcleos de CPU, memória, capacidade de disco e outras características, para atender aos requisitos de diferentes aplicativos. As instâncias A8 e A9, recentemente introduzidas, funcionam bem para muitas cargas de trabalho intensas de computação e também para aplicativos de MPI paralelos em particular, porque têm alta velocidade, CPUs de múltiplos núcleos e grandes quantidades de memória. Em certas configurações, as instâncias aproveitam uma rede de aplicativos de baixa latência e alta produtividade na nuvem, que inclui tecnologia de Acesso Remoto Direto à Memória (RDMA) para máxima eficiência nos aplicativos de MPI paralelos.

O Azure também oferece a desenvolvedores de aplicativos de Computação de Grande Porte e a parceiros um conjunto completo de recursos de computação, serviços, opções de arquitetura e ferramentas de desenvolvimento. O Azure dá suporte a fluxos de trabalho personalizados de Computação de Grande Porte, envolvendo fluxos de trabalho de dados especializados e padrões de agendamento de tarefas e trabalhos que podem ser dimensionados para milhares de núcleos de computação.

## <a name="media"></a>Mídia
![Serviços de Mídia do Azure](./media/fundamentals-introduction-to-azure/MediaServicesIntroNew.png)   
 **Figura: os Serviços de Mídia é uma plataforma para aplicativos que oferecem vídeo e outras mídias para clientes em todo o mundo.**

A reprodução de vídeos constitui uma grande parte do tráfego de Internet hoje em dia, e essa porcentagem será ainda maior no futuro. Apesar disso, fornecer vídeo na web não é algo simples. Há muitas variáveis, como o algoritmo de codificação e a resolução de vídeo da tela do usuário. Vídeos também tendem a ter picos de demanda, como um pico de noite de sábado quando muitas pessoas decidem assistir um filme on-line.

Dada sua popularidade, é seguro apostar que muitos aplicativos novos que usam vídeo serão criados. Mas todos eles precisarão solucionar alguns dos mesmos problemas, e fazer com que cada um resolva esses problemas por conta própria não faz nenhum sentido. Uma abordagem melhor é criar uma plataforma que ofereça soluções comuns para muitos aplicativos. E criar essa plataforma em nuvem tem algumas vantagens claras. Pode ser amplamente disponível em um formato pago pelo uso e também pode lidar com a variabilidade na demanda com a qual os aplicativos de vídeo sempre se deparam.

Os Serviços de Mídia do Azure resolvem esse problema. Eles fornecem um conjunto de componentes de nuvem que tornam mais fácil a vida das pessoas que criam e executam aplicativos usando vídeo e outras mídias.

Como mostra a figura, os Serviços de Mídia oferecem um conjunto de componentes para aplicativos que funcionam com vídeo e outras mídias. Por exemplo, ele inclui um componente de inclusão de vídeo para carregar o vídeo nos Serviços de Mídia (onde ele é armazenado nos Blobs do Azure), um componente de codificação que oferece suporte a vários formatos de áudio e vídeo, um componente de proteção de conteúdo que oferece gerenciamento de direitos digitais, um componente para inserir anúncios em um fluxo de vídeo, componentes de streaming e muito mais. Os parceiros da Microsoft também podem fornecer os componentes para a plataforma para que a Microsoft distribua esses componentes e cobre em seu nome.

Os aplicativos que usam essa plataforma podem ser executados no Azure ou em outro lugar. Por exemplo, um aplicativo de desktop para uma casa de produção de vídeo pode permitir que seus usuários carreguem vídeo nos Serviços de Mídia para, em seguida, processá-los de várias maneiras. Como alternativa, um serviço de gerenciamento de conteúdo com base em nuvem em execução no Azure pode depender dos Serviços de Mídia para processar e distribuir vídeo. Onde quer que ele seja executado e seja o que for que ele faz, cada aplicativo escolhe os componentes que precisa usar, acessando-os por meio de interfaces RESTful.

Para distribuir o que produz, um aplicativo pode usar o CDN do Azure, outro CDN ou apenas enviar bits diretamente aos usuários. No entanto, vídeos criados usando os Serviços de Mídia podem ser consumidos por vários sistemas de clientes, incluindo Windows, Macintosh, HTML 5, iOS, Android, Windows Phone, Flash e Silverlight. O objetivo é tornar mais fácil a criação de aplicativos de mídia modernos.

**Referências**

Para obter um panorama mais visual do funcionamento dos Serviços de Mídia, faça o download do [pôster dos Serviços de Mídia do Azure][Azure Media Services Poster].

## <a name="commerce"></a>Comércio
O surgimento de Software como Serviço está transformando a maneira como criamos aplicativos. Ele também está transformando a forma como vendemos aplicativos. Como um aplicativo SaaS reside na nuvem, faz sentido que seus clientes em potencial devam procurar soluções on-line. E essa alteração se aplica a dados assim como aplicativos. Por que as pessoas não devem procurar a nuvem em busca de conjuntos de dados disponíveis no mercado? A Microsoft aborda essas duas preocupações com o [Azure Marketplace](https://azure.microsoft.com/marketplace/).

![Comércio do Azure](./media/fundamentals-introduction-to-azure/CommerceIntroNew.png)   
 **Figura: o Azure Marketplace e a Azure Store permitem encontrar e comprar aplicativos Azure e conjuntos de dados comerciais e utilizá-los como parte de seus aplicativos do Azure.**

A diferença entre os dois é que o Marketplace está fora do Portal de Gerenciamento do Azure, mas a Store pode ser acessada de dentro do portal. Clientes potenciais podem pesquisar para encontrar aplicativos do Azure que atendam às suas necessidades. Os clientes também podem pesquisar conjuntos de dados comerciais, incluindo dados demográficos, dados financeiros, dados geográficos e muito mais. Quando eles encontram algo que gostem, podem acessar do fornecedor, diretamente pelos locais da web Marketplace ou Store de ou, em alguns casos, do Portal de Gerenciamento. Os aplicativos também podem usar a API de pesquisa Bing por meio do Marketplace, dando-lhes acesso aos resultados de pesquisas na web.

**Cenários para comércio**

O SendGrid é um aplicativo na Azure Store que permite que você envie emails. Ele oferece funções adicionais, como entrega e estatísticas confiáveis.  Você pode comprar esse aplicativo e serviços relacionados, em vez de tentar construir uma infraestrutura desse tipo por conta própria.  

## <a name="getting-started"></a>Introdução
Agora que você tem a visão geral, a próxima etapa é escrever seu primeiro aplicativo do Azure. Escolha a linguagem, [obtenha o SDK adequado](/downloads/)e vá em frente. Computação em nuvem é o novo padrão: comece agora.

[Azure Media Services Poster]: http://azure.microsoft.com/documentation/infographics/media-services/
