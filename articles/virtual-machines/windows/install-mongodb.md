---
title: Instalar o MongoDB em uma VM Windows no Azure | Microsoft Docs
description: Saiba como instalar o MongoDB em uma VM do Azure que executa o Windows Server 2012 R2 criada com o modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: afd8e6b47fb86985acde062af1fb38ec3af4e902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711382"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Instalar e configurar o MongoDB em uma VM do Windows no Azure
[O MongoDB](https://www.mongodb.org) é um popular banco de dados NoSQL de código-fonte aberto e de alto desempenho. Este artigo orienta você pela instalação e configuração do MongoDB em uma VM (máquina virtual) do Windows Server 2016 no Azure. Você também pode [instalar o MongoDB em uma VM do Linux no Azure](../linux/install-mongodb.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de instalar e configurar o MongoDB, você precisa criar uma VM e, idealmente, adicionar um disco de dados a ela. Consulte os artigos a seguir para criar uma VM e adicionar um disco de dados:

* Criar uma VM do Windows Server usando o [Portal do Azure](quick-create-portal.md) ou o [Azure PowerShell](quick-create-powershell.md).
* Anexar um disco de dados a uma VM do Windows Server usando o [Portal do Azure](attach-managed-disk-portal.md) ou o [Azure PowerShell](attach-disk-ps.md).

Para começar a instalar e configurar o MongoDB, [Faça logon em sua VM do Windows Server](connect-logon.md) usando a Área de Trabalho Remota.

## <a name="install-mongodb"></a>Instalar o MongoDB
> [!IMPORTANT]
> Os recursos de segurança do MongoDB, como autenticação e associação com o endereço IP, não são habilitados por padrão. Os recursos de segurança devem ser ativados antes de implantar o MongoDB em um ambiente de produção. Para obter mais informações, veja [Segurança e Autenticação do MongoDB](https://www.mongodb.org/display/DOCS/Security+and+Authentication).


1. Depois de se conectar à VM usando a Área de Trabalho Remota, abra o Internet Explorer pela barra de tarefas.
2. Selecione **usar as configurações de segurança, privacidade e compatibilidade recomendadas** quando o Internet Explorer abrir pela primeira vez e clique em **OK**.
3. A configuração de segurança reforçada do Internet Explorer é habilitada por padrão. Adicione o site do MongoDB à lista de sites permitidos:
   
   * Selecione o ícone **Ferramentas** no canto superior direito.
   * Em **Opções da Internet**, selecione a guia **Segurança**, selecione o ícone **Sites confiáveis**.
   * Clique no botão **Sites**. Adicione *https://\*.mongodb.com* à lista de sites confiáveis e então feche a caixa de diálogo.
     
     ![Definir as configurações de segurança do Internet Explorer](./media/install-mongodb/configure-internet-explorer-security.png)
4. Navegue para a página [MongoDB – Downloads](https://www.mongodb.com/downloads) (https://www.mongodb.com/downloads).
5. Se necessário, selecione o **Community Server** Edition e, em seguida, selecione a última versão estável atual do *Windows Server 2008 R2 64 bits e posterior*. Para baixar o instalador, clique em **BAIXAR (msi)**.
   
    ![Baixar o instalador do MongoDB](./media/install-mongodb/download-mongodb.png)
   
    Quando o download for concluído, execute o instalador.
6. Leia e aceite o contrato de licença. Quando solicitado, selecione a instalação **Completa**.
7. Se quiser, escolha instalar também a Compass, uma interface gráfica para o MongoDB.
8. Na primeira tela, clique em **Instalar**.

## <a name="configure-the-vm-and-mongodb"></a>Configurar a VM e o MongoDB
1. As variáveis de caminho não são atualizadas pelo instalador do MongoDB. Sem a localização `bin` do MongoDB na variável de caminho, você precisará especificar o caminho completo sempre que usar um executável do MongoDB. Para adicionar a localização à variável de caminho:
   
   * Clique com o botão direito do mouse no menu **Iniciar** e selecione **Sistema**.
   * Clique na guia **Configurações avançadas do sistema** e em **Variáveis de Ambiente**.
   * Em **Variáveis de sistema**, selecione **Caminho** e, em seguida, clique em **Editar**.
     
     ![Configurar variáveis PATH](./media/install-mongodb/configure-path-variables.png)
     
     Adicione o caminho à pasta `bin` do MongoDB. Geralmente, o MongoDB é instalado em *C:\Arquivos de Programas\MongoDB*. Verifique se o caminho de instalação na sua VM. O exemplo a seguir adiciona a localização de instalação padrão do MongoDB à variável `PATH`:
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > Certifique-se de adicionar o ponto e vírgula à esquerda (`;`) para indicar que você está adicionando uma localização à variável `PATH`.

2. Crie diretórios de dados e de log do MongoDB no disco de dados. No menu **Iniciar**, selecione **Prompt de Comando**. Os exemplos a seguir criam os diretórios na unidade F:
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. Inicie uma instância do MongoDB com o comando a seguir, ajustando o caminho adequadamente para os diretórios de dados e de log:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    Pode levar alguns minutos para que o MongoDB aloque os arquivos de diário e comece a detectar conexões. Todas as mensagens de log serão direcionadas ao arquivo *F:\MongoLogs\mongolog.log* quando o servidor `mongod.exe` for iniciado e alocar arquivos de diário.
   
   > [!NOTE]
   > O prompt de comando permanece focado nessa tarefa enquanto sua instância do MongoDB está em execução. Deixe a janela de prompt de comando aberta para continuar a executar o MongoDB. Ou, se preferir, instale o MongoDB como um serviço, conforme detalhado na próxima etapa.

4. Para obter uma experiência mais robusta do MongoDB, instale o `mongod.exe` como um serviço. Criar um serviço significa que você não precisa deixar um prompt de comando em execução cada vez que você deseje usar o MongoDB. Crie o serviço da seguinte maneira, ajustando o caminho para os diretórios de dados e de log adequadamente:
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    Isso cria um serviço chamado MongoDB e com a descrição "Mongo DB". Os parâmetros a seguir também são especificados:
   
   * A opção `--dbpath` especifica o local do diretório de dados.
   * A opção `--logpath` deve ser usada para especificar um arquivo de log, uma vez que o serviço em execução não tem uma janela de comando para exibir a saída.
   * A opção `--logappend` especifica que uma reinicialização do serviço fará com que a saída seja acrescentada ao arquivo de log existente.
   
   Execute o seguinte comando para iniciar o serviço MongoDB:
   
    ```
    net start MongoDB
    ```
   
    Para obter mais informações sobre como criar o serviço MongoDB, veja [Configurar um Serviço Windows para o MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Testar a instância do MongoDB
Com o MongoDB em execução como uma única instância ou instalado como um serviço, agora você pode começar a criar e usar seus bancos de dados. Para iniciar o shell administrativo do MongoDB, abra outra janela de prompt de comando do menu **Iniciar** e digite o seguinte comando:

```
mongo  
```

Você pode listar os bancos de dados com o comando `db`. Inserir alguns dados da seguinte maneira:

```
db.foo.insert( { a : 1 } )
```

Pesquise por dados da seguinte maneira:

```
db.foo.find()
```

A saída deverá ser semelhante ao seguinte exemplo:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Saia do console `mongo` da seguinte maneira:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurar regras de firewall e de Grupo de Segurança de Rede
Agora que o MongoDB está instalado e em execução, abra uma porta no Firewall do Windows para poder se conectar remotamente ao MongoDB. Para criar uma nova regra de entrada que permita o uso da porta TCP 27017, abra um prompt do PowerShell com privilégios administrativos e digite o seguinte comando:

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

Você também pode criar a regra usando a ferramenta de gerenciamento gráfica **Firewall do Windows com Segurança Avançada**. Crie uma nova regra de entrada para permitir o uso da porta TCP 27017.

Se necessário, crie uma regra de Grupo de Segurança de Rede para permitir o acesso ao MongoDB de fora da sub-rede da rede virtual do Azure existente. Você pode criar as regras de Grupo de Segurança de Rede usando o [Portal do Azure](nsg-quickstart-portal.md) ou [Azure PowerShell](nsg-quickstart-powershell.md). Assim como acontece com as regras de Firewall do Windows, permita o uso da porta TCP 27017 pela adaptador de rede virtual da VM do MongoDB.

> [!NOTE]
> A porta TCP 27017 é a porta padrão usada pelo MongoDB. Você pode alterar essa porta usando o parâmetro `--port` ao iniciar `mongod.exe` manualmente ou de um serviço. Se você alterar a porta, deverá certificar-se de atualizar as regras de Firewall do Windows e o grupo de segurança de rede nas etapas anteriores.


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você também aprendeu como instalar e configurar o MongoDB na VM do Windows. Agora você pode acessar o MongoDB na VM do Windows seguindo os tópicos avançados na [documentação do MongoDB](https://docs.mongodb.com/manual/).

