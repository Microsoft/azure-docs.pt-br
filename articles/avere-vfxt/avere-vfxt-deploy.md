---
title: Implantar o Avere vFXT para Azure
description: Saiba como usar o assistente de implantação disponível no Azure Marketplace para implantar um cluster com o avere vFXT para Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 110fc658ffc3b72de91bbf1c9562f3efeecaf55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342409"
---
# <a name="deploy-the-vfxt-cluster"></a>Implantar cluster vFXT

Este procedimento explica como usar o assistente de implantação disponível no Azure Marketplace. O assistente implanta automaticamente o cluster usando um modelo do Azure Resource Manager. Depois de inserir os parâmetros no formulário e clicar em **criar**, o Azure concluirá automaticamente estas tarefas:

* Cria o controlador de cluster, que é uma VM básica que contém o software necessário para implantar e gerenciar o cluster.
* Configura o grupo de recursos e a infraestrutura de rede virtual, incluindo a criação de novos elementos.
* Cria as VMs do nó de cluster e as configura como o cluster avere.
* Se solicitado, cria um novo contêiner de BLOBs do Azure e o configura como um arquivo de núcleo de cluster.

Depois de seguir as instruções neste documento, você terá uma rede virtual, uma sub-rede, um controlador de cluster e um cluster vFXT, conforme mostrado no diagrama a seguir. Este diagrama mostra o filer principal do blob do Azure opcional, que inclui um novo contêiner de armazenamento de BLOBs (em uma nova conta de armazenamento, não mostrado) e um ponto de extremidade de serviço para o armazenamento da Microsoft na sub-rede.

![diagrama mostrando três retângulos concêntricos com componentes de cluster avere. O retângulo externo é rotulado como ' grupo de recursos ' e contém um hexágono rotulado como ' armazenamento de BLOBs (opcional) '. O próximo retângulo em é rotulado como ' rede virtual: 10.0.0.0/16 ' e não contém nenhum componente exclusivo. O retângulo mais interno é rotulado ' subnet: 10.0.0.0/24 ' e contém uma VM rotulada ' cluster Controller ', uma pilha de três VMs rotuladas como ' vFXT Nodes (cluster vFXT) ' e um hexágono rotulado como ' Service Endpoint '. Há uma seta para conectar o ponto de extremidade de serviço (que está dentro da sub-rede) e o armazenamento de BLOBs (que está fora da sub-rede e vnet, no grupo de recursos). A seta passa pelos limites de sub-rede e rede virtual.](media/avere-vfxt-deployment.png)

Antes de usar o modelo de criação, verifique se você resolveu estes pré-requisitos:  

* [Nova assinatura](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Permissões do proprietário da assinatura](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Ponto de extremidade do serviço de armazenamento (se necessário)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – necessário para implantações que usam uma rede virtual existente e criar armazenamento de BLOBs

Para obter mais informações sobre as etapas e o planejamento de implantação de cluster, leia [Plan your Avere vFXT system](avere-vfxt-deploy-plan.md) (Planejar seu sistema vFXT do Avere) e [Visão geral da implantação](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Criar o Avere vFXT para Azure

Acesse o modelo de criação no portal do Azure procurando por avere e selecionando "avere vFXT for Azure ARM template".

![Janela do navegador mostrando o portal do Azure com a navegação estrutural "Novo > Marketplace > Tudo". Na página tudo, o campo de pesquisa tem o termo "avere" e o segundo resultado, "avere vFXT para o modelo ARM do Azure" é descrito em vermelho para realçá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes na página de modelo do avere vFXT para o Azure ARM, clique no botão **criar** para começar.

![O Azure marketplace com a primeira página do modelo de implantação exibida](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro etapas – duas páginas de coleta de informações mais validação e confirmação das etapas.

* A página um reúne as configurações para a VM do controlador de cluster.
* A página dois coleta parâmetros para criar o cluster e recursos adicionais, como sub-redes e armazenamento.
* A página três resume suas escolhas e valida a configuração.
* A página quatro explica os termos e condições de software e permite que você inicie o processo de criação do cluster.

## <a name="page-one-parameters---cluster-controller-information"></a>Parâmetros da página um – informações do controlador de cluster

A primeira página do modelo de implantação se concentra no controlador de cluster.

![Primeira página do modelo de implantação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome do controlador de cluster** – defina o nome para a VM do controlador de cluster.

* **Nome de usuário do controlador** -defina o nome de usuário raiz para a VM do controlador de cluster.

* **Tipo de autenticação** – escolha autenticação por senha ou chave pública SSH para se conectar ao controlador. O método de chave pública SSH é recomendado. Leia [Como criar e usar chaves SSH](../virtual-machines/linux/ssh-from-windows.md) se precisar de ajuda.

* **Senha** ou **Chave pública SSH** – dependendo do tipo de autenticação selecionado, você deve fornecer uma chave pública RSA ou uma senha nos próximos campos. Essa credencial é usada com o nome de usuário fornecido anteriormente.

* **Assinatura** – selecione a assinatura para o Avere vFXT.

* **Grupo de recursos** - escolha um grupo de recursos vazio já criado para o cluster do Avere vFXT ou clique em "Criar novo" e insira um novo nome de grupo de recursos.

* **Localização** – selecione a localização do Azure para seu cluster e recursos.

Clique em **OK** quando terminar.

> [!NOTE]
> Se você quiser que o controlador de cluster tenha um endereço IP público, crie uma nova rede virtual para o cluster, em vez de selecionar uma rede existente. Essa configuração está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Parâmetros da página dois – informações do cluster do vFXT

A segunda página do modelo de implantação permite que você defina o tamanho do cluster, o tipo de nó, o tamanho do cache e os parâmetros de armazenamento, entre outras configurações.

![Segunda página do modelo de implantação](media/avere-vfxt-deploy-2.png)

* **Contagem de nós de cluster avere vFXT** -escolha o número de nós no cluster. O mínimo é de três nós e o máximo é de doze.

* **Senha de administração de cluster** – crie a senha de administração de cluster. Essa senha é usada com o nome de usuário ```admin``` para entrar no painel de controle de cluster, no qual você pode monitorar o cluster e definir as configurações de cluster.

* **Nome do cluster do Avere vFXT** – dê ao cluster um nome exclusivo.

* **Tamanho** – esta seção mostra o tipo de VM que será usado para os nós de cluster. Embora haja apenas uma opção recomendada, o link **alterar tamanho** abre uma tabela com detalhes sobre esse tipo de instância e um link para uma calculadora de preço.

* **Tamanho do cache por nó** – o cache do cluster é distribuído entre os nós do cluster, portanto, o tamanho total do cache em seu cluster avere vFXT será esse tamanho multiplicado pelo número de nós.

  Configuração recomendada: Use 4 TB por nó para Standard_E32s_v3 nós.

* **Rede virtual** – defina uma nova rede virtual para hospedar o cluster ou selecione uma rede existente que atenda aos pré-requisitos descritos em [planejar seu sistema avere vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Se você criar uma nova rede virtual, o controlador de cluster terá um endereço IP público para que você possa acessar a nova rede privada. Se você escolher uma rede virtual existente, o controlador de cluster será configurado sem um endereço IP público.
  >
  > Um endereço IP publicamente visível no controlador de cluster fornece acesso mais fácil para o cluster vFXT, mas gera um pequeno risco de segurança.
  >* Um endereço IP público no controlador de cluster permite que você o use como um host de atalho para se conectar ao cluster do Avere vFXT de fora da sub-rede privada.
  >* Se você não tiver um endereço IP público no controlador, precisará de outro host de salto, uma conexão VPN ou o ExpressRoute para acessar o cluster. Por exemplo, use uma rede virtual existente que já tenha uma conexão VPN configurada.
  >* Se você criar um controlador com um endereço IP público, você deverá proteger a VM do controlador com um grupo de segurança de rede. Por padrão, o avere vFXT para implantação do Azure cria um grupo de segurança de rede que restringe o acesso de entrada apenas para a porta 22 para controladores com endereços IP públicos. Você pode proteger ainda mais o sistema bloqueando o acesso a seu intervalo de endereços IP de origem, ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

  Uma nova rede virtual também é configurada com um ponto de extremidade de serviço de armazenamento para o armazenamento de BLOBs do Azure e com o controle de acesso à rede bloqueado para permitir somente IPs da sub-rede do cluster.

* **Sub-rede** -escolha uma sub-rede ou crie uma nova.

* **Criar e usar o armazenamento de BLOBs** -escolha **true** para criar um novo contêiner de blob do Azure e configurá-lo como armazenamento de back-end para o novo cluster avere vFXT. Essa opção também cria uma nova conta de armazenamento no grupo de recursos do cluster e cria um ponto de extremidade do serviço de armazenamento da Microsoft dentro da sub-rede do cluster.
  
  Se você fornecer uma rede virtual existente, ela deverá ter um ponto de extremidade de serviço de armazenamento antes de criar o cluster. (Para obter mais informações, leia [planejar o sistema avere vFXT](avere-vfxt-deploy-plan.md).)

  Defina este campo como **false** se não quiser criar um novo contêiner. Nesse caso, você deve anexar e configurar o armazenamento depois de criar o cluster. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções.

* **(Novo) conta de armazenamento** -se estiver criando um novo contêiner de blob do Azure, insira um nome para a nova conta de armazenamento.

## <a name="validation-and-purchase"></a>Validação e compra

A página três resume a configuração e valida os parâmetros. Após a validação ser realizada com sucesso, verifique o resumo e clique no botão **OK** .

> [!TIP]
> Você pode salvar as configurações de criação do cluster clicando no link **baixar modelo e parâmetros** ao lado do botão **OK** . Essas informações podem ser úteis se você precisar criar um cluster semelhante posteriormente, por exemplo, para criar um cluster de substituição em um cenário de recuperação de desastres. (Leia as [diretrizes de recuperação de desastre](disaster-recovery.md) para saber mais.)

![Terceira página do modelo de implantação – validação](media/avere-vfxt-deploy-3.png)

A página quatro fornece os termos de uso e links para informações de privacidade e preços.

Insira as informações de contato ausentes e clique no botão **criar** para aceitar os termos e criar o avere vFXT para o cluster do Azure.

![Quarta página do modelo de implantação – termos e condições, botão criar](media/avere-vfxt-deploy-4.png)

A implantação do cluster leva de 15 a 20 minutos.

## <a name="gather-template-output"></a>Coletar a saída de modelo

Quando o modelo avere vFXT conclui a criação do cluster, ele gera informações importantes sobre o novo cluster.

> [!TIP]
> Certifique-se de copiar o **endereço IP de gerenciamento** da saída do modelo. Você precisa desse endereço para administrar o cluster.

Para localizar as informações:

1. Vá para o grupo de recursos do seu controlador de cluster.

1. No lado esquerdo, clique em **Implantações** e, em seguida, em **microsoft-avere.vfxt-template**.

   ![Página do portal do grupo de recursos com implantações selecionadas à esquerda e microsoft-avere.vfxt-template sendo exibido em uma tabela embaixo do nome da implantação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **Saídas**. Copie os valores em cada um dos campos.

   ![produz a página mostrando os valores SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP em campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Próximas etapas

Agora que o cluster está em execução e você sabe seu endereço IP de gerenciamento, [Conecte-se à ferramenta de configuração de cluster](avere-vfxt-cluster-gui.md).

Use a interface de configuração para personalizar o cluster, incluindo estas tarefas de instalação:

* [Habilitar o suporte](avere-vfxt-enable-support.md)
* [Adicionar armazenamento](avere-vfxt-add-storage.md) (se necessário)