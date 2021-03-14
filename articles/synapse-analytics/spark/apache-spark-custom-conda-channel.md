---
title: Criar um canal Conda personalizado para gerenciamento de pacotes
description: Saiba como criar um canal Conda personalizado para gerenciamento de pacotes
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107299"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Criar um canal Conda personalizado para gerenciamento de pacotes 
Ao instalar pacotes do Python, o Gerenciador de pacotes do Conda usa canais para procurar pacotes. Talvez seja necessário criar um canal Conda personalizado por vários motivos. Por exemplo, você pode achar que:

- seu espaço de trabalho é um vazamento de dados protegido e as conexões de saída são bloqueadas.  
- Você tem pacotes que não deseja carregar em repositórios públicos.
- você deseja configurar o repositório alternativo para os usuários em seu espaço de trabalho.

Neste artigo, forneceremos um guia passo a passo para ajudá-lo a criar seu canal Conda personalizado dentro de sua conta de Azure Data Lake Storage.

## <a name="set-up-your-local-machine"></a>Configurar seu computador local

1. Instale o Conda no computador local. Você pode consultar o [tempo de execução do Azure Synapse Spark](./apache-spark-version-support.md) para identificar a versão do Conda que é usada no mesmo tempo de execução.
   
2. Para criar um canal personalizado, instale Conda-Build.
```
conda install conda-build
```
3. Organize todos os pacotes no para a plataforma que você deseja fornecer. Neste exemplo, instalaremos o arquivo Anaconda no computador local.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Montar a conta de armazenamento em seu computador
Em seguida, montaremos a conta de Azure Data Lake Storage Gen2 em seu computador local. Esse processo também pode ser feito com uma conta do WASB; no entanto, veremos um exemplo para a conta ADLSg2 
 
Para obter mais informações sobre como montar a conta de armazenamento em seu computador local, você pode visitar [esta página](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Você pode instalar o blobfuse do repositório de software do Linux para produtos da Microsoft.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Crie seu mountpoint ( ```mkdir /path/to/mount``` ) e monte um contêiner de blob com blobfuse. Neste exemplo, vamos usar o valor **PrivateChannel** para a variável **MyContainer** .
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Criar o canal
No próximo conjunto de etapas, criaremos um canal Conda personalizado. 

1. Em seu computador local, crie um diretório para organizar todos os pacotes para seu canal personalizado.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organize todos os ```tar.bz2``` pacotes do https://repo.anaconda.com/pkgs/main/linux-64/ no subdiretório. Lembre-se também de incluir todos os pacotes tar. bz2 dependentes. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Para obter mais informações, você também pode [visitar o guia do usuário do Conda](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) para criar canais personalizados. 

## <a name="storage-account-permissions"></a>Permissões da conta de armazenamento
Agora, será necessário validar as permissões na conta de armazenamento. Para definir essas permissões, navegue até o caminho em que o canal personalizado será criado. Em seguida, crie um token SAS para ```privatechannel``` que tenha permissões de leitura, lista e execução. 

O nome do canal será agora a URL SAS do blob gerada nesse processo.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Criar um arquivo de configuração de ambiente Conda de exemplo
Por fim, verifique o processo de instalação criando um arquivo Conda de exemplo ```environment.yml``` . Se você tiver em um espaço de trabalho habilitado para DEP, deverá especificar o ``nodefaults`` canal em seu arquivo de ambiente.

Aqui está um exemplo de arquivo de configuração Conda:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Depois de criar o arquivo Conda de exemplo, você pode criar um ambiente Conda virtual. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Agora que você verificou seu canal personalizado, você pode usar o processo de [Gerenciamento de pool do Python](./apache-spark-manage-python-packages.md) para atualizar as bibliotecas em seu pool de Apache Spark.

## <a name="next-steps"></a>Próximas etapas
- Exibir as bibliotecas padrão: [suporte à versão Apache Spark](apache-spark-version-support.md)
- Gerenciar pacotes do Python: [Gerenciamento de pacotes do Python](./apache-spark-manage-python-packages.md)

