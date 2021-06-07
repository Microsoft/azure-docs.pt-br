---
title: Atualização de dispositivo para o tutorial do Hub IoT do Azure usando o agente de pacote Ubuntu Server 18.04 x64 | Microsoft Docs
description: Introdução à atualização de dispositivo para o Hub IoT do Azure usando o agente de pacote Ubuntu Server 18.04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 6464ad632251053ac481fbd1f6a3e1197aa470df
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121295"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Atualização de dispositivo para o tutorial do Hub IoT do Azure usando um agente de pacote no Ubuntu Server 18.04 x64

A atualização de dispositivo para o Hub IoT é compatível com duas formas de atualizações – de acordo com a imagem e o pacote.

As atualizações baseadas em pacote são atualizações de destino que alteram apenas um componente ou aplicativo específico no dispositivo. Essas atualizações baseadas em pacote levam a um menor consumo de largura de banda e ajuda a reduzir o tempo para baixar e instalar a atualização. As atualizações de pacote geralmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evita a sobrecarga de criação de imagens.

Este tutorial de ponta a ponta guia você na atualização do Azure IoT Edge no Ubuntu Server 18.04 x64 usando o agente de pacote de Atualização de Dispositivo. Embora o tutorial demonstre como atualizar o IoT Edge, usando etapas semelhantes, será possível atualizar outros pacotes, como o mecanismo de contêiner que ele usa.

Caso planeje usar outra configuração de plataforma de sistema operacional, as ferramentas e os conceitos deste tutorial ainda poderão ser aplicados. Conclua esta introdução a um processo de atualização de ponta a ponta e escolha a forma preferida de atualização e plataforma do sistema operacional, para aprofundar-se nos detalhes.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Baixar e instalar um agente de Atualização de Dispositivo e as respectivas dependências
> * Adicionar uma marca ao seu dispositivo
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implantar uma atualização de pacote
> * Monitorar a implantação de atualização

## <a name="prerequisites"></a>Pré-requisitos

* Se você ainda não tiver feito isso, crie uma [conta e uma instância de atualização de dispositivo](create-device-update-account.md), incluindo a configuração de um hub IoT.
* A [cadeia de conexão de um dispositivo do IoT Edge](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Preparar uma dispositivo
### <a name="using-the-automated-deploy-to-azure-button"></a>Como usar o botão de Implantação Automatizada no Azure

Por conveniência, este tutorial usará um [modelo do Azure Resource Manager](../virtual-machines/linux/using-cloud-init.md) baseado em [cloud-init](../azure-resource-manager/templates/overview.md) para ajudar você a configurar de modo rápido máquinas virtuais do Ubuntu 18.04 LTS. Ele vai instalar o runtime do Azure IoT Edge e o agente de pacote de Atualização de Dispositivo, depois configurar o dispositivo de modo automático com informações de provisionamento usando a cadeia de conexão do dispositivo para um dispositivo do IoT Edge (pré-requisito) que será fornecido. O modelo do Azure Resource Manager também evita a necessidade de iniciar uma sessão SSH para concluir a instalação.

1. Para começar, clique no botão abaixo:

   [![Botão Implantar no Azure para iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Na recém-criada janela, preencha os campos de formulário disponíveis:

    > [!div class="mx-imgBorder"]
    > [![Captura de tela mostrando o modelo iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Assinatura**: uma assinatura ativa do Azure na qual a máquina virtual será implantada.

    **Grupo de recursos**: um Grupo de Recursos existente ou recém-criado para conter a máquina virtual e os recursos associados.

    **Prefixo do rótulo DNS**: um valor necessário de sua preferência que será usado para prefixar o nome do host da máquina virtual.

    **Nome de usuário do administrador**: um nome de usuário que obterá privilégios de raiz na implantação.

    **Cadeia de conexão do dispositivo**: uma [cadeia de conexão do dispositivo](../iot-edge/how-to-register-device.md) para um dispositivo criado no [Hub IoT](../iot-hub/about-iot-hub.md) pretendido.

    **Tamanho da VM**: o [tamanho](../cloud-services/cloud-services-sizes-specs.md) da máquina virtual que será implantada

    **Versão do sistema operacional Ubuntu**: a versão do sistema operacional Ubuntu que será instalada na máquina virtual de base. Deixe o valor padrão inalterado, pois ele será definido como Ubuntu 18.04-LTS.

    **Localização**: a [região geográfica](https://azure.microsoft.com/global-infrastructure/locations/) na qual a máquina virtual será implantada. Esse valor assumirá a localização do Grupo de Recursos selecionado como padrão.

    **Tipo de autenticação**: escolha **sshPublicKey** ou **senha**, de acordo com sua preferência.

    **Senha ou chave do administrador**: o valor da Chave Pública SSH ou o valor da senha, dependendo do Tipo de Autenticação escolhida.

    Após preencher os campos, marque a caixa de seleção na parte inferior da página para aceitar os termos. Depois clique em **Comprar** para iniciar a implantação.

1. Verifique se a implantação foi concluída com êxito. Aguarde alguns minutos após a conclusão da implantação para que a pós-instalação e a configuração concluam a instalação do IoT Edge e do agente de atualização de Pacote do Dispositivo.

   Um recurso de máquina virtual deve ter sido implantado no grupo de recursos selecionado.  Anote o nome do computador que deve estar no formato `vm-0000000000000`. Além disso, anote o **Nome DNS** associado, que deve estar no formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    O **Nome DNS** pode ser obtido na seção **Visão geral** da máquina virtual implantada recentemente no portal do Azure.

    > [!div class="mx-imgBorder"]
    > [![Uma captura de tela mostrando o nome DNS da VM do IoT Edge](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Caso queira usar o SSH nessa VM após a instalação, use o **Nome DNS** associado a este comando: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Preparar um dispositivo de modo manual (opcional)
Semelhante às etapas automatizadas pelo [script cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt), veja a seguir as etapas manuais para instalar e configurar o dispositivo. Essas etapas podem ser usadas para preparar um dispositivo físico.

1. Siga as instruções para [Instalar o runtime do Azure IoT Edge](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > O agente de pacote de Atualização de Dispositivo não depende do IoT Edge. No entanto, ele dependerá do daemon do Serviço de Identidade da IoT que será instalado com o IoT Edge (1.2.0 e posterior) para obter uma identidade e se conectar ao Hub IoT.
   >
   > Embora não seja abordado neste tutorial, o [daemon do Serviço de Identidade da IoT poderá ser instalado de modo autônomo em dispositivos IoT baseados em Linux](https://azure.github.io/iot-identity-service/packaging.html). A sequência da instalação é importante. O agente de pacote de Atualização de Dispositivo deverá ser instalado _após_ o Serviço de Identidade da IoT. Caso contrário, o agente de pacote não será registrado como um componente autorizado a estabelecer uma conexão com o Hub IoT.

1. Depois, instale pacotes .deb do agente de Atualização de Dispositivo.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

A atualização de dispositivo para pacotes de software do Hub IoT do Azure está sujeita aos seguintes termos de licença:
  * [Licença de atualização de dispositivo para o Hub IoT](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE)

Leia os termos da licença antes de usar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

## <a name="add-a-tag-to-your-device"></a>Adicionar uma marca ao seu dispositivo

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até Hub IoT.

2. Em "IoT Edge", no painel de navegação esquerdo, localize seu dispositivo do IoT Edge e acesse o Dispositivo Gêmeo ou Módulo Gêmeo.

3. No Módulo Gêmeo do módulo de agente de Atualização de Dispositivo, exclua qualquer valor de marca de Atualização de Dispositivo existente definindo-o como nulo. Se você estiver usando a Identidade do dispositivo com o agente de Atualização de Dispositivo, faça essas alterações no Dispositivo Gêmeo.

4. Adicione um novo valor de marca da Atualização de dispositivo, conforme mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importar atualização

1. Vá para [Versões da Atualização de dispositivo](https://github.com/Azure/iot-hub-device-update/releases) no GitHub e clique na lista suspensa "Ativos".

3. Baixe o `Edge.package.update.samples.zip` clicando nele.

5. Extraia o conteúdo da pasta para descobrir um exemplo de atualização e os manifestos de importação correspondentes dele. 

2. No portal do Azure, selecione a opção Atualizações do Dispositivo em Gerenciamento de Dispositivo Automático na barra de navegação à esquerda no Hub IoT.

3. Selecione a guia Atualizações.

4. Selecione "+ Importar nova atualização".

5. Selecione o ícone de pasta ou caixa de texto em "Selecionar um arquivo de manifesto de importação". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o manifesto de importação `sample-1.0.1-aziot-edge-importManifest.json` na pasta baixada anteriormente. Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecionar um ou mais arquivos de atualização". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o arquivo de atualização do manifesto APT `sample-1.0.1-aziot-edge-apt-manifest.json` baixado anteriormente.
Esta atualização atualizará os pacotes `aziot-identity-service` e `aziot-edge` para a versão 1.2.0~rc4-1 em seu dispositivo.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Captura de tela mostrando a seleção do arquivo de atualização." lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou a caixa de texto em "Selecionar um contêiner de armazenamento". Em seguida, selecione a conta de armazenamento apropriada.

7. Caso já tenha criado um contêiner, você pode reutilizá-lo. (Caso contrário, selecione "+ Contêiner" para criar um novo contêiner de armazenamento para atualizações.)  Selecione o contêiner que você deseja usar e clique em "Selecionar".

   :::image type="content" source="media/import-update/container.png" alt-text="Captura de tela mostrando a seleção do contêiner." lightbox="media/import-update/container.png":::

8. Selecione "Enviar" para iniciar o processo de importação.

9. O processo de importação é iniciado, e a tela é alterada para a seção "Importar histórico". Selecione "Atualizar" para exibir o progresso até que o processo de importação seja concluído. Dependendo do tamanho da atualização, o processo de importação pode ser concluído em alguns minutos, mas também pode levar mais tempo.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Captura de tela mostrando a sequência de importação de atualização." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna Status indicar que a importação foi bem-sucedida, selecione o cabeçalho "Pronto para implantar". Agora a atualização importada deve estar presente na lista.

[Saiba mais](import-update.md) sobre a importação de atualizações.

## <a name="create-update-group"></a>Criar grupo de atualizações

1. Vá para o Hub IoT que você conectou anteriormente à sua instância de Atualização de dispositivo.

1. Selecione a opção Atualizações de dispositivo em Gerenciamento de dispositivo automático na barra de navegação à esquerda.

1. Selecione a guia Grupos na parte superior da página.

1. Clique no botão Adicionar para criar um novo grupo.

1. Selecione a marca do Hub IoT que você criou na etapa anterior com a lista. Selecione Criar grupo de atualizações.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Captura de tela mostrando a seleção de marca." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre como adicionar marcas e criar grupos de atualização

## <a name="deploy-update"></a>Implantar atualização

1. Após a criação do grupo, será possível conferir uma nova atualização disponível para o grupo de dispositivos, juntamente com um link para executar uma atualização na coluna _Atualizações disponíveis_. Talvez seja necessário atualizar a página.

1. Clique no link para acessar uma atualização disponível.

1. Confirme se o grupo adequado foi selecionado como o grupo de destino e agende sua implantação

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione Atualizar" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Por padrão, a data/hora de início será 24 horas do horário atual. Lembre-se de selecionar uma data/hora diferente caso deseje que a implantação comece mais cedo.

1. Clique em Implantar atualização.

1. Exiba o gráfico de conformidade. Será possível ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em andamento" lightbox="media/deploy-update/update-in-progress.png":::

1. Depois de atualizar seu dispositivo com êxito, será possível conferir o gráfico de conformidade e a atualização dos detalhes da implantação para refletir as mesmas informações. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização realizada com êxito" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorar uma implantação de atualização

1. Selecione a guia Implantações na parte superior da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Guia Implantações" lightbox="media/deploy-update/deployments-tab.png":::

1. Selecione a implantação criada para exibir os detalhes da implantação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes de implantação" lightbox="media/deploy-update/deployment-details.png":::

1. Selecione Atualizar para exibir os detalhes mais recentes do status. Continue esse processo até que o status seja alterado para Realizado com êxito.

Agora você concluiu com êxito uma atualização de pacote de ponta a ponta usando a atualização do dispositivo para o Hub IoT em um dispositivo Ubuntu Server 18.04 x64. 

## <a name="clean-up-resources"></a>Limpar recursos

Limpe a conta de atualização do dispositivo, a instância, o Hub IoT e o dispositivo do IoT Edge (caso tenha criado a VM por meio do botão Implantar no Azure) quando não precisar mais dos recursos. Você pode fazer isso acessando cada recurso individual e selecionando "Excluir". Você precisa limpar uma instância de atualização de dispositivo antes de limpar a conta de atualização de dispositivo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atualização de imagem no tutorial Raspberry Pi 3 B+](device-update-raspberry-pi.md)
