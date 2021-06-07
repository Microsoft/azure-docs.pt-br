---
title: Tutorial para filtrar e analisar dados para implantação avançada com a computação no Azure Stack Edge Pro | Microsoft Docs
description: Saiba como configurar a função de computação no Azure Stack Edge Pro e usá-la para fluxo de implantação avançada antes de enviar para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
ms.openlocfilehash: 81953f573c47d229fcaccd7c11f62155acd2f119
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063637"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro-for-advanced-deployment-flow"></a>Tutorial: Transformar dados com o Azure Stack Edge Pro para o fluxo de implantação avançada

Este tutorial descreve como configurar uma função de computação para um fluxo de implantação avançada no dispositivo Azure Stack Edge Pro. Depois de configurar a função de computação, o Azure Stack Edge Pro pode transformar os dados antes de enviar para o Azure.

Computação pode ser configurada para o fluxo de implantação simples ou avançada em seu dispositivo.

| Critérios | Implantação simples                                | Implantação avançada                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Destinado a     | Administradores de TI                                | Desenvolvedores                            |
| Type             | Usar o serviço Azure Stack Edge para implantar módulos      | Usar o serviço de Hub IoT para implantar módulos |
| Módulos implantados | Single                                           | Módulos múltiplos ou encadeados           |


Esse procedimento pode levar cerca de 20 a 30 minutos para ser concluído.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um gatilho
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar uma função de computação em seu dispositivo Azure Stack Edge Pro, verifique o seguinte:

- Você ativou o dispositivo Azure Stack Edge Pro conforme descrito em [Conectar, configurar e ativar o Azure Stack Edge Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar a computação

Para configurar a computação no Azure Stack Edge Pro, você criará um recurso do Hub IoT.

1. No portal do Azure do recurso do Azure Stack Edge, acesse **Visão geral**. No painel direito, selecione o bloco **IoT Edge**.

    ![Introdução à computação](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. No bloco **Habilitar o serviço IoT Edge**, selecione **Adicionar**. Essa ação habilita o serviço do IoT Edge que lhe permite implantar os módulos do IoT Edge localmente em seu dispositivo.

    ![Introdução à computação 2](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Em **Criar serviço do IoT Edge**, insira o seguinte:

   
    |Campo  |Valor  |
    |---------|---------|
    |Subscription     |Selecione uma assinatura para o recurso do Hub IoT. Você pode selecionar a mesma assinatura usada pelo recurso Azure Stack Edge.        |
    |Grupo de recursos     |Insira um nome para o grupo de recursos para o recurso do Hub IoT. Você pode selecionar o mesmo grupo de recursos usado pelo recurso do Azure Stack Edge.         |
    |Hub IoT     | Escolha **Novo** ou **Existente**. <br> Por padrão, uma camada Standard (S1) é usada para criar um recurso de IoT. Para usar um recurso de IoT de Camada gratuita, crie um e, em seguida, selecione o recurso existente.      |
    |Nome     |Aceite o padrão ou insira um nome para o recurso do Hub IoT.         |

    ![Introdução à computação 3](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecione **Examinar + criar**. A criação do recurso do Hub IoT leva alguns minutos. Depois da criação do recurso do Hub IoT, a **visão geral** é atualizada para indicar que o serviço IoT Edge está em execução. 

    Quando o serviço de IoT Edge é configurado no dispositivo de borda, ele cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Os dois dispositivos podem ser exibidos no recurso do Hub IoT. Um runtime do IoT Edge também está em execução no dispositivo do IoT Edge. No momento, somente a plataforma Linux está disponível para o dispositivo IoT Edge.

    Para confirmar que a função de computação de borda foi configurada, selecione **Serviço IoT Edge > Propriedades** e veja o dispositivo IoT e o dispositivo IoT Edge. 

    ![Introdução à computação 4](./media/azure-stack-edge-deploy-configure-compute-advanced/configure-compute-4.png)
    

## <a name="add-shares"></a>Adicionar compartilhamentos

Para a implantação avançada neste tutorial, você precisará de dois compartilhamentos: um compartilhamento do Edge e outro compartilhamento local do Edge.

1. Adicione um compartilhamento do Microsoft Edge no dispositivo seguindo as seguintes etapas:

    1. Em seu recurso do Azure Stack Edge, vá para **IoT Edge > Compartilhamentos**.
    2. Na página **Compartilhamentos**, na barra de comandos, selecione **+ Adicionar compartilhamento**.
    3. Na folha **Adicionar compartilhamento**, forneça o nome do compartilhamento e selecione o tipo de compartilhamento.
    4. Para montar o compartilhamento do Microsoft Edge, marque a caixa de seleção **Usar o compartilhamento com a computação de borda**.
    5. Selecione a **Conta de armazenamento**, o **Serviço de armazenamento**, um usuário existente e, em seguida, selecione **Criar**.

        ![Adicionar um compartilhamento do Microsoft Edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    Após o compartilhamento do Edge ser criado, você receberá uma notificação de êxito na criação. A lista de compartilhamentos é atualizada para refletir o novo compartilhamento.

2. Adicione um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge repetindo todas as etapas da etapa anterior e marcando a caixa de seleção **Configurar como o compartilhamento local do Microsoft Edge**. Os dados no compartilhamento local permanecem no dispositivo.

    ![Adicionar um compartilhamento local do Microsoft Edge](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Na folha **Compartilhamentos**, você vê a lista de compartilhamentos atualizada.

    ![Lista atualizada de compartilhamentos](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Para exibir as propriedades do compartilhamento local recém-criado, basta selecioná-lo na lista. Na caixa **Ponto de montagem local para módulos de computação de borda**, copie o valor correspondente a esse compartilhamento.

    Você usará esse ponto de montagem local ao implantar o módulo.

    ![A caixa "Ponto de montagem local para módulos de computação de borda"](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Para exibir as propriedades do compartilhamento do Edge recém-criado, selecione-o na lista. Na caixa **Ponto de montagem local para módulos de computação de borda**, copie o valor correspondente a esse compartilhamento.

    Você usará esse ponto de montagem local ao implantar o módulo.

    ![Adicionar módulo personalizado](./media/azure-stack-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Adicionar um gatilho

1. Vá para o recurso do Azure Stack Edge e para **IoT Edge > Gatilhos**. Selecione **+ Adicionar gatilho**.

    ![Adicionar gatilho](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Na folha **Adicionar gatilho**, adicione os valores de entrada a seguir.

    |Campo  |Valor  |
    |---------|---------|
    |Nome do gatilho     | Um nome exclusivo para o gatilho.         |
    |Tipo de gatilho     | Selecione o gatilho **Arquivo**. Um gatilho de arquivo é acionado sempre que ocorre um evento de arquivo, como uma gravação de arquivo no compartilhamento de entrada. Um gatilho agendado, por sua vez, é acionado de acordo com um agendamento definido por você. Neste exemplo, precisamos de um gatilho de arquivo.    |
    |Compartilhamento de entrada     | Selecione um compartilhamento de entrada. O compartilhamento local do Microsoft Edge é o compartilhamento de entrada, nesse caso. O módulo usado aqui move os arquivos do compartilhamento local do Microsoft Edge para um compartilhamento do Microsoft Edge, em que são carregados para a nuvem.        |

    ![Adicionar gatilho 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Você será notificado depois que o gatilho for criado. A lista de gatilhos é atualizada para exibir o gatilho criado recentemente. Selecione o gatilho que você acabou de criar.

    ![Adicionar gatilho 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Copie e salve a rota de exemplo. Você modificará essa rota de exemplo e a usará mais tarde no Hub IoT.

    `"sampleroute&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Adicionar gatilho 4](./media/azure-stack-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Adicionar um módulo

Não há módulos personalizados neste dispositivo do Edge. Você pode adicionar um módulo personalizado ou pré-criado. Para saber como criar um módulo personalizado, acesse [Desenvolver um módulo em C# para o dispositivo Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md).

Nesta seção, você adiciona um módulo personalizado ao dispositivo do IoT Edge que foi criado em [Desenvolver um módulo em C# para o Azure Stack Edge Pro](azure-stack-edge-create-iot-edge-module.md). Esse módulo personalizado usa arquivos de um compartilhamento local do Microsoft Edge no dispositivo do Microsoft Edge e move-os para um compartilhamento do Microsoft Edge (nuvem) no dispositivo. O compartilhamento em nuvem então efetua o push dos arquivos para a conta de Armazenamento do Azure associada com o compartilhamento em nuvem.

1. Vá para o recurso do Azure Stack Edge e para **IoT Edge > Visão geral**. No bloco **Módulos**, selecione **Ir para Hub IoT do Azure**.

    ![Selecionar a implantação avançada](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. No recurso do Hub IoT, acesse **Dispositivo do IoT Edge** e, em seguida, selecione seu dispositivo do IoT Edge.

    ![Acessar o dispositivo do IoT Edge no Hub IoT](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Em **Detalhes do dispositivo**, selecione **Definir Módulos**.

    ![O link Definir Módulos](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Em **Adicionar Módulos**, faça o seguinte:

    1. Insira o nome, o endereço, o nome de usuário e a senha para as configurações do registro de contêiner do módulo personalizado.
    O nome, o endereço e as credenciais listadas são usados para recuperar módulos com uma URL correspondente. Para implantar este módulo, em **Módulos de implantação**, selecione **Módulo do IoT Edge**. Este módulo do IoT Edge é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao seu dispositivo Azure Stack Edge Pro.

        ![A página Definir Módulos](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Especifique as configurações para o módulo personalizado do IoT Edge. Insira os valores a seguir.
     
        |Campo  |Valor  |
        |---------|---------|
        |Nome     | Um nome exclusivo para o módulo. Esse módulo é um contêiner do Docker que você pode implantar no dispositivo do IoT Edge associado ao Azure Stack Edge Pro.        |
        |URI da imagem     | O URI da imagem para a imagem de contêiner correspondente ao módulo.        |
        |Credenciais necessárias     | Se essa opção for marcada, o nome de usuário e a senha serão usados para recuperar os módulos com uma URL correspondente.        |
    
        Na caixa **Opções de Criação de Contêiner**, insira os pontos de montagem locais dos módulos do Edge que você copiou nas etapas anteriores para o compartilhamento do Edge e o compartilhamento local do Edge.

        > [!IMPORTANT]
        > Os caminhos usados aqui são montados em seu contêiner, portanto, eles devem coincidir com o que a funcionalidade em seu contêiner espera. Se você está seguindo as instruções em [Criar um módulo personalizado](azure-stack-edge-create-iot-edge-module.md#update-the-module-with-custom-code), o código especificado nesse módulo espera os caminhos copiados. Não modifique esses caminhos.
    
        Na caixa **Opções de Criação de Contêiner**, cole o exemplo a seguir:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Forneça também todas as variáveis de ambiente usadas para seu módulo. Variável de ambiente fornecem informações opcionais que ajudam a definir o ambiente no qual o módulo é executado.

        ![A caixa Opções de Criação de Contêiner](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Se necessário, defina as configurações avançadas de runtime do Edge e, em seguida, clique em **Avançar**.

        ![Adicionar módulo personalizado 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5. Em **Especificar Rotas**, defina rotas entre módulos.  
   
   ![A seção Especificar Rotas](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-7.png)

    Você pode substituir *rota* pela cadeia de caracteres a seguir, que você copiou anteriormente. Neste exemplo, insira o nome do compartilhamento local que efetuará push de dados para o compartilhamento de nuvem. Substitua o `modulename` pelo nome do módulo. Selecione **Avançar**.
        
    ```
    "route&quot;: &quot;FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![A seção Especificar Rotas](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-8.png)

6. Em **Examinar implantação**, examine todas as configurações e, em seguida, selecione **Enviar** para enviar o módulo para implantação.

   ![A página Definir Módulos 2](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Essa ação inicia a implantação do módulo. Depois que a implantação for concluída, o **Status de runtime** do módulo será **em execução**.

    ![Adicionar módulo personalizado 3](./media/azure-stack-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verificar a transformação e a transferência de dados

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

Execute as etapas a seguir para verificar a transformação e transferência de dados para o Azure.
 
1. No Explorador de Arquivos, conecte-se aos compartilhamentos do Microsoft Edge e local do Microsoft Edge criados anteriormente.

   ![Verifique a transformação de dados](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1. Adicione dados ao compartilhamento de local.

   ![Verificar a transformação de dados 2](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Os dados são movidos para o compartilhamento em nuvem.

    ![Verificar a transformação de dados 3](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Os dados são então enviados por push do compartilhamento em nuvem para a conta de armazenamento. Para exibir os dados, vá para sua conta de armazenamento e, em seguida, selecione **Gerenciador de Armazenamento**. Você pode exibir os dados carregados em sua conta de armazenamento.

    ![Verificar a transformação de dados 4](./media/azure-stack-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Você concluiu o processo de validação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar a computação
> * Adicionar compartilhamentos
> * Adicionar um gatilho
> * Adicionar um módulo de computação
> * Verificar a transformação e a transferência de dados

Para saber como administrar seu dispositivo Azure Stack Edge Pro, confira:

> [!div class="nextstepaction"]
> [Usar IU da Web local para administrar um Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md)
