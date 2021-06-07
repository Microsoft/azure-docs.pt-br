---
title: Tutorial de Atualização de dispositivo do Hub IoT do Azure usando a imagem do Yocto de referência do Raspberry Pi 3 B+ | Microsoft Docs
description: Introdução à Atualização de dispositivo do Hub IoT do Azure usando a imagem do Yocto de referência do Raspberry Pi 3 B+.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 143a7c411bea6a451645c860b7b5d12d2aa8d9f5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121329"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Tutorial de Atualização de dispositivo do Hub IoT do Azure usando a imagem de referência do Raspberry Pi 3 B+

A Atualização de dispositivo do Hub IoT é compatível com duas formas de atualizações: baseadas em imagem e em pacote.

As atualizações de imagem fornecem um nível mais alto de confiança no estado final do dispositivo. Normalmente, é mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, já que não são apresentados os mesmos desafios que os pacotes e suas dependências. Devido à natureza atômica, também é possível adotar facilmente um modelo de failover A/B.

O tutorial apresenta as etapas para concluir uma atualização de ponta a ponta baseada em imagem usando a Atualização de dispositivo do Hub IoT. 

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Baixar a imagem
> * Adicionar uma marca ao seu dispositivo IoT
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implantar uma atualização de imagem
> * Monitorar a implantação da atualização

## <a name="prerequisites"></a>Pré-requisitos
* Se você ainda não tiver feito isso, crie uma [conta e uma instância de atualização de dispositivo](create-device-update-account.md), incluindo a configuração de um Hub IoT.

## <a name="download-image"></a>Baixar a imagem

Existem três imagens disponíveis como parte dos "Ativos" em uma determinada [Versão do GitHub de atualização de dispositivo](https://github.com/Azure/iot-hub-device-update/releases). São fornecidas a imagem base (adu-base-image) e uma imagem de atualização (adu-update-image) para que você possa experimentar as distribuições em diferentes versões sem a necessidade de atualizar o cartão SD no dispositivo. Para isso, você precisará carregar as imagens de atualização para a Atualização de dispositivo do serviço do Hub IoT, como parte da importação.

## <a name="flash-sd-card-with-image"></a>Atualizar o cartão SD com imagem

Usando sua ferramenta de atualização de SO favorita, instale a imagem de base da Atualização de dispositivo (adu-base-image) no cartão SD que será usada no dispositivo Raspberry Pi 3 B+.

### <a name="using-bmaptool-to-flash-sd-card"></a>Como usar o bmaptool para atualizar o cartão SD

1. Instale o utilitário `bmaptool`, caso ainda não o tenha feito.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Localize o caminho do cartão SD no `/dev`. O caminho deve ter a seguinte aparência: `/dev/sd*` ou `/dev/mmcblk*`. Você pode usar o utilitário `dmesg` para ajudar a localizar o caminho correto.

3. Será necessário desmontar todas as partições montadas antes de atualizar.

   ```shell
   sudo umount /dev/<device>
   ```

4. Verifique se você tem permissões de gravação para o dispositivo.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Opcional. Para uma atualização mais rápida, baixe o arquivo bimap juntamente com o arquivo de imagem e coloque-os no mesmo diretório.

6. Atualize o cartão SD.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
A Atualização de dispositivo do software do Hub IoT do Azure está sujeita aos seguintes termos de licença:
   * [Licença da atualização de dispositivo do Hub IoT](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Leia os termos da licença antes de usar o agente. A instalação e o uso confirmam sua aceitação desses termos. Caso não concorde com os termos de licença, não use a Atualização de dispositivo do agente do Hub IoT.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Criar dispositivo no Hub IoT e obter a cadeia de conexão

Agora, o dispositivo precisa ser adicionado ao Hub IoT do Azure.  De dentro do Hub IoT do Azure, será gerada uma cadeia de conexão para o dispositivo.

1. No portal do Azure, inicie o Hub IoT do Azure.
2. Crie um novo dispositivo.
3. No lado esquerdo da página, navegue até "Explorers" > "Dispositivos IoT" > e selecione "Novo".
4. Dê um nome para o dispositivo em "ID do dispositivo"— verifique se a caixa de seleção "Gerar chaves automaticamente" está marcada.
5. Clique em "Salvar".
6. Agora, você será redirecionado de volta à página "Dispositivos", e o dispositivo que você criou deverá estar na lista. Selecione o dispositivo.
7. Na exibição do dispositivo, selecione o ícone "Copiar" ao lado de "Cadeia de conexão primária".
8. Cole os caracteres copiados em algum lugar para usá-los posteriormente nas etapas abaixo.
   **Essa cadeia de caracteres copiada é a cadeia de conexão do dispositivo**.

## <a name="provision-connection-string-on-sd-card"></a>Provisionar a cadeia de conexão no cartão SD

1. Verifique se o Raspberry Pi3 está conectado à rede.
2. No PowerShell, use o comando abaixo para ssh no dispositivo
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Insira o logon como "raiz", e a senha deve ser deixada em branco.
5. Depois de realizar o ssh com êxito no dispositivo, execute os comandos abaixo
 
Substitua `<device connection string>` por sua cadeia de conexão
 ```markdown
    echo "connection_string=<device connection string>" > /adu/adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> /adu/adu-conf.txt
    echo "aduc_model=RefDevice" >> /adu/adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Conectar o dispositivo na Atualização de dispositivo do Hub IoT

1. No lado esquerdo da página, selecione "Dispositivos IoT" em "Explorers".
2. Selecione o link com o nome do dispositivo.
3. Na parte superior da página, selecione "Dispositivo Gêmeo".
4. Na seção "relatado" das propriedades do dispositivo gêmeo, procure a versão do kernel do Linux.
Para um novo dispositivo, que não recebeu uma atualização da Atualização de dispositivo, o valor [DeviceManagement:DeviceInformation:1.swVersion](device-update-plug-and-play.md) representará a versão do firmware em execução no dispositivo.  Depois que uma atualização tiver sido aplicada a um dispositivo, a Atualização de dispositivo usará o valor da propriedade [AzureDeviceUpdateCore:ClientMetadata:4.installedUpdateId](device-update-plug-and-play.md) para representar a versão do firmware em execução no dispositivo.
5. Os arquivos de imagem de base e de atualização contam com um número de versão no nome de arquivo.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Use esse número de versão na etapa de Atualização de importação abaixo.

## <a name="add-a-tag-to-your-device"></a>Adicionar uma marca ao seu dispositivo

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até Hub IoT.

2. Em "Dispositivos IoT" ou "IoT Edge" no painel de navegação esquerdo, encontre seu dispositivo IoT e navegue até o Dispositivo Gêmeo ou Módulo Gêmeo.

3. No Módulo Gêmeo do módulo de agente de Atualização de Dispositivo, exclua qualquer valor de marca de Atualização de Dispositivo existente definindo-o como nulo. Se você estiver usando a Identidade do dispositivo com o agente de Atualização de Dispositivo, faça essas alterações no Dispositivo Gêmeo.

4. Adicione um novo valor de marca da Atualização de dispositivo, conforme mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importar atualização

1. Crie um manifesto de importação seguindo essas [instruções](import-update.md).
2. Selecione a opção Atualizações de dispositivo em Gerenciamento de dispositivo automático na barra de navegação à esquerda.
3. Selecione a guia Atualizações.
4. Selecione "+ Importar nova atualização".
5. Selecione o ícone de pasta ou caixa de texto em "Selecionar um arquivo de manifesto de importação". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o Manifesto de importação criado acima.  Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecionar um ou mais arquivos de atualização". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o arquivo de atualização que você deseja implantar nos dispositivos IoT.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Captura de tela mostrando a seleção do arquivo de atualização." lightbox="media/import-update/select-update-files.png":::

5. Selecione o ícone de pasta ou a caixa de texto em "Selecionar um contêiner de armazenamento". Em seguida, selecione a conta de armazenamento apropriada.

6. Caso já tenha criado um contêiner, você pode reutilizá-lo. (Caso contrário, selecione "+ Contêiner" para criar um novo contêiner de armazenamento para atualizações.)  Selecione o contêiner que você deseja usar e clique em "Selecionar".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Captura de tela mostrando a seleção do contêiner." lightbox="media/import-update/container.png":::

7. Selecione "Enviar" para iniciar o processo de importação.

8. O processo de importação é iniciado, e a tela é alterada para a seção "Importar histórico". Selecione "Atualizar" para exibir o progresso até que o processo de importação seja concluído. Dependendo do tamanho da atualização, ela pode ser concluída em alguns minutos, mas também pode levar mais tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Captura de tela mostrando a sequência de importação de atualização." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Quando a coluna Status indicar que a importação foi bem-sucedida, selecione o cabeçalho "Pronto para implantar". Agora a atualização importada deve estar presente na lista.

[Saiba mais](import-update.md) sobre a importação de atualizações.

## <a name="create-update-group"></a>Criar grupo de atualizações

1. Vá para o Hub IoT que você conectou anteriormente à sua instância de Atualização de dispositivo.

2. Selecione a opção Atualizações de dispositivo em Gerenciamento de dispositivo automático na barra de navegação à esquerda.

3. Selecione a guia Grupos na parte superior da página. 

4. Clique no botão Adicionar para criar um novo grupo.

5. Selecione a marca do Hub IoT que você criou na etapa anterior com a lista. Selecione Criar grupo de atualizações.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Captura de tela mostrando a seleção de marca." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre como adicionar marcas e criar grupos de atualização


## <a name="deploy-update"></a>Implantar atualização

1. Depois que o grupo for criado, será possível ver uma nova atualização disponível para o grupo de dispositivos, juntamente com um link para a atualização em Atualizações Pendentes. Talvez seja necessário atualizar a página. 

2. Clique na atualização disponível.

3. Confirme se o grupo correto foi selecionado como o grupo de destino. Agende uma implantação e selecione Implantar atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione Atualizar" lightbox="media/deploy-update/select-update.png":::

4. Exiba o gráfico de conformidade. Será possível ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em andamento" lightbox="media/deploy-update/update-in-progress.png":::

5. Depois de atualizar seu dispositivo com êxito, será possível conferir o gráfico de conformidade e a atualização dos detalhes da implantação para refletir as mesmas informações. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização realizada com êxito" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorar uma implantação de atualização

1. Selecione a guia Implantações na parte superior da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Guia Implantações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implantação criada para exibir os detalhes da implantação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes de implantação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione Atualizar para exibir os detalhes mais recentes do status. Continue esse processo até que o status seja alterado para Bem-sucedido.

Agora você concluiu com êxito uma atualização de imagem de ponta a ponta usando a Atualização de dispositivo do Hub IoT em um dispositivo Raspberry Pi 3 B+. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessária, limpe a conta da atualização de dispositivo, a instância, o Hub IoT e o dispositivo IoT. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Agente de referência do simulador](device-update-simulator.md)
