---
title: Tutorial da Atualização de dispositivo do Hub IoT do Azure usando o Agente de referência do simulador do Ubuntu (18.04 x64) | Microsoft Docs
description: Introdução à Atualização de dispositivo do Hub IoT do Azure usando o Agente de referência do simulador do Ubuntu (18.04 x64).
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 90e72bd12d9115e5ff95213428ae4ac37979dcf3
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120088"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Tutorial da Atualização de dispositivo do Hub IoT do Azure usando o Agente de referência do simulador do Ubuntu (18.04 x64)

A Atualização de dispositivo do Hub IoT é compatível com duas formas de atualizações: baseadas em imagem e em pacote.

As atualizações de imagem fornecem um nível mais alto de confiança no estado final do dispositivo. Normalmente, é mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, já que não são apresentados os mesmos desafios que os pacotes e suas dependências. Devido à natureza atômica, também é possível adotar facilmente um modelo de failover A/B.

O tutorial apresenta as etapas para concluir uma atualização de ponta a ponta baseada em imagem usando a Atualização de dispositivo do Hub IoT. 

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Baixar e instalar a imagem
> * Adicionar uma marca ao seu dispositivo IoT
> * Importar uma atualização
> * Criar um grupo de dispositivos
> * Implantar uma atualização de imagem
> * Monitorar a implantação da atualização

## <a name="prerequisites"></a>Pré-requisitos
* Se você ainda não tiver feito isso, crie uma [conta e uma instância de atualização de dispositivo](create-device-update-account.md), incluindo a configuração de um hub IoT.

### <a name="download-and-install"></a>Baixar e instalar

* Cmdlets AZ (CLI do Azure) para PowerShell:
  * Abra o PowerShell > Instale a CLI do Azure ("Y" para solicitar a instalação de uma fonte "não confiável")

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Habilitar o WSL em seu dispositivo Windows (Subsistema do Windows para Linux)

1. Abra o PowerShell como administrador em seu computador e execute o seguinte comando (talvez precise reiniciar após cada etapa; reinicie quando for solicitado):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Talvez seja preciso reiniciar após essa etapa*)

2. Acesse a Microsoft Store na Web e instale o [Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Inicie o "Ubuntu 18.04 LTS" e instale-o.

4. Depois da instalação, será solicitado que você defina o nome raiz (nome de usuário) e a senha. Use uma senha de nome raiz que seja fácil de memorizar.

5. No PowerShell, execute o comando a seguir para definir o Ubuntu como a distribuição padrão do Linux:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Liste todas as distribuições do Linux e verifique se o Ubuntu é a padrão.

```powershell
PS> wsl --list
```

7. Você deve ver: **Ubuntu-18.04 (padrão)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Baixar a Atualização de dispositivo do Agente de referência do simulador do Ubuntu (18.04 x64)

A imagem de atualização do Ubuntu pode ser baixada na seção *Ativos* das notas sobre a versão [aqui](https://github.com/Azure/iot-hub-device-update/releases).

Existem duas versões do agente. Caso esteja executando um cenário baseado em imagem, use AducIotAgentSim-microsoft-swupdate; caso esteja executando um cenário baseado em pacote, use AducIotAgentSim-microsoft-apt.

## <a name="install-device-update-agent-simulator"></a>Instalar um simulador de Agente de atualização de dispositivo

1. Inicie o Ubuntu WSL e insira o comando a seguir (observe que há um espaço extra e um ponto no final).

  ```shell
  explorer.exe .
  ```

2. Copie AducIotAgentSim-microsoft-swupdate (ou AducIotAgentSim-microsoft-apt) da pasta local na qual ele foi baixado em /mnt para sua pasta base no WSL.

3. Execute o comando a seguir para tornar os binários executáveis.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  ou

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
A Atualização de dispositivo do software do Hub IoT do Azure está sujeita aos seguintes termos de licença:
   * [Licença da atualização de dispositivo do Hub IoT](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licença de cliente de otimização de entrega](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Leia os termos da licença antes de usar o agente. A instalação e o uso confirmam sua aceitação desses termos. Caso não concorde com os termos de licença, não use a Atualização de dispositivo do agente do Hub IoT.

## <a name="add-device-to-azure-iot-hub"></a>Adicionar dispositivo ao Hub IoT do Azure

Depois que o agente de atualização de dispositivo estiver em execução em um dispositivo IoT, o dispositivo precisará ser adicionado ao Hub IoT do Azure.  De dentro do Hub IoT do Azure, será gerada uma cadeia de conexão para um dispositivo específico.

1. No portal do Azure, inicie a Atualização de dispositivo do Hub IoT.
2. Crie um novo dispositivo.
3. No lado esquerdo da página, navegue até "Explorers" > "Dispositivos IoT" > e selecione "Novo".
4. Dê um nome para o dispositivo em "ID do dispositivo"— verifique se a caixa de seleção "Gerar chaves automaticamente" está marcada.
5. Clique em "Salvar".
6. Agora, você será redirecionado de volta à página "Dispositivos", e o dispositivo que você criou deverá estar na lista. Selecione o dispositivo.
7. Na exibição do dispositivo, selecione o ícone "Copiar" ao lado de "Cadeia de conexão primária".
8. Cole os caracteres copiados em algum lugar para usá-los posteriormente nas etapas abaixo. **Essa cadeia de caracteres copiada é a cadeia de conexão do dispositivo**.

## <a name="add-connection-string-to-simulator"></a>Adicionar cadeia de conexão ao simulador

Inicie o agente de atualização de dispositivo em seus novos dispositivos de software.

1. Inicie o Ubuntu.
2. Execute o agente de atualização de dispositivo e especifique a cadeia de conexão do dispositivo da seção anterior entre apóstrofos:

Substitua `<device connection string>` por sua cadeia de conexão
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

ou

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Role para cima e procure a cadeia de caracteres que indique que o dispositivo está no estado "Ocioso". O estado "Ocioso" significa que o dispositivo está pronto para comandos de serviço:

```markdown
Agent running. [main]
```

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

5. Selecione o ícone de pasta ou caixa de texto em "Selecionar um arquivo de manifesto de importação". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o Manifesto de importação criado acima.  Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecionar um ou mais arquivos de atualização". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione a imagem de atualização do Ubuntu que você baixou anteriormente. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Captura de tela mostrando a seleção do arquivo de atualização." lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou a caixa de texto em "Selecionar um contêiner de armazenamento". Em seguida, selecione a conta de armazenamento apropriada.

7. Caso já tenha criado um contêiner, você pode reutilizá-lo. (Caso contrário, selecione "+ Contêiner" para criar um novo contêiner de armazenamento para atualizações.)  Selecione o contêiner que você deseja usar e clique em "Selecionar".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Captura de tela mostrando a seleção do contêiner." lightbox="media/import-update/container.png":::

8. Selecione "Enviar" para iniciar o processo de importação.

9. O processo de importação é iniciado, e a tela é alterada para a seção "Importar histórico". Selecione "Atualizar" para exibir o progresso até que o processo de importação seja concluído. Dependendo do tamanho da atualização, ela pode ser concluída em alguns minutos, mas também pode levar mais tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Captura de tela mostrando a sequência de importação de atualização." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna Status indicar que a importação foi bem-sucedida, selecione o cabeçalho "Pronto para implantar". Agora a atualização importada deve estar presente na lista.

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

Você acabou de concluir uma atualização de imagem de ponta a ponta com êxito usando a Atualização de dispositivo do Hub IoT e o Agente de referência do simulador do Ubuntu (18.04 x64).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessária, limpe a conta da atualização de dispositivo, a instância, o Hub IoT e o dispositivo IoT. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Solução de problemas](troubleshoot-device-update.md)

