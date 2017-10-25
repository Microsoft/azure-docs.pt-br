---
title: Como ler e gravar arquivos de dados grandes | Microsoft Docs
description: Como ler e gravar arquivos grandes em experimentos do ML do Azure.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: fb3158ef786ad73440a59c07b38476a98ced0768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="persisting-changes-and-dealing-with-large-files"></a>Como manter alterações e lidar com arquivos grandes

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolamento, portabilidade e capacidade de reprodução de execução
O Serviço de Experimentação do Azure ML permite configurar diferentes destinos de execução, alguns locais, como computador local ou um contêiner do Docker em um computador local, outros remotos, como um contêiner do Docker em um computador remoto ou um cluster do HDInsight. Consulte o artigo [Execução de experimentação de configuração](experiment-execution-configuration.md) para obter mais detalhes. Antes de qualquer execução ser possível, a pasta do projeto é copiada para o destino computação. Isso é verdadeiro mesmo no caso de uma execução local em que uma pasta temporária local é usada para essa finalidade. 

O objetivo deste design é garantir o isolamento, a capacidade de reprodução e a portabilidade da execução. Se você executar o mesmo script duas vezes, no mesmo destino de computação ou em um destino de computação diferente, você com certeza receberá os mesmos resultados. As alterações feitas pela primeira execução não deve afetar a segunda execução. Com esse design, é possível tratar destinos de computação como recursos de computação sem estado e sem afinidade com os trabalhos executados depois de eles serem concluídos.

## <a name="challenges"></a>Desafios
Com os benefícios de portabilidade e capacidade de repetição, esse design também traz alguns desafios exclusivos:
### <a name="persisting-state-changes"></a>Como manter alterações de estado
Se o seu script modificar o estado no contexto de computação, as alterações não serão mantidas para a próxima execução, nem serão propagadas de volta para o computador cliente automaticamente. 

Mais concretamente, se o seu script criar uma subpasta ou gravar um arquivo, você não encontrará esse arquivo ou pasta no diretório do seu projeto após a execução. Eles são deixados em uma pasta temporária no ambiente de computação de destino, onde quer que ele esteja. Eles podem ser usados para fins de depuração, mas você nunca pode assumir dependências da sua existência.

### <a name="dealing-with-large-files-in-project-folder"></a>Como lidar com grandes arquivos na pasta de projeto

Se a sua pasta de projeto contiver algum arquivo grande, você incorrerá em latência quando a pasta do projeto for copiada para o ambiente de computação de destino no início de cada execução. Mesmo que a execução ocorra localmente, ainda é o tráfego de disco desnecessário que deve ser evitado. É por isso que podemos atualmente limitar o tamanho máximo do projeto a 25 MB no momento.

## <a name="option-1-use-the-outputs-folder"></a>Opção 1: usar a pasta de saída
Essa é a opção preferencial se o script gerar arquivos e você esperar que esses arquivos mudem a cada execução do experimento e você queira manter um histórico desses arquivos. O caso de uso comum é se você treinar um modelo ou criar um conjunto de dados ou plotar um gráfico como um arquivo de imagem como parte da sua execução de treinamento do modelo. E você deseja comparar essas saídas entre execuções, voltar e selecionar um arquivo de saída (como um modelo) produzido por uma execução anterior e usá-lo para uma tarefa subsequente (como pontuação).

Essencialmente, você pode gravar arquivos em uma pasta chamada `outputs` com relação ao diretório raiz. Essa é uma pasta especial que recebe tratamento especial pelo Serviço de Experimentação. Qualquer elemento que seu script crie lá durante a execução, como um arquivo de modelo, um arquivo de dados ou um arquivo de imagem plotada (coletivamente conhecidos como _artefatos_), é copiado para a conta de Armazenamento de Blobs do Azure associada à sua conta de Experimentação depois que a execução for concluída. Eles se tornam parte do seu registro de histórico de execução.

Aqui está um exemplo rápido para armazenar um modelo na pasta `outputs`:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Você pode baixar qualquer _artefato_ navegando até a seção **Arquivos de Saída** da página de detalhes de execução de uma determinada execução no Azure ML Workbench, selecionando-o e clicando no botão **Baixar**. Ou você pode usar o comando `az ml asset download` na janela da CLI.

Para ver um exemplo mais completo, consulte o script Python `iris_sklearn.py` no exemplo de projeto de _Classificando a íris_.

## <a name="option-2-use-the-shared-folder"></a>Opção 2: usar a pasta compartilhada
Usar uma pasta compartilhada que pode ser acessada nas execuções independentes pode ser uma ótima opção para os seguintes cenários, desde que você não precise manter um histórico desses arquivos para cada execução: 
- Seu script precisa carregar dados de arquivos locais, como arquivos csv ou um diretório de arquivos de texto ou imagem, como dados de treinamento ou teste. 
- Seu script processa dados brutos e grava os resultados intermediários, como dados de treinamento de destacados de arquivos de texto/imagem que são usados em uma execução de treinamento subsequente. 
- Seu script produz um modelo e seu script de pontuação subsequente precisa acompanhar o modelo e usá-lo para avaliação. 

Uma limitação importante é que a pasta compartilhada reside localmente no mesmo destino de computação que você escolher. Portanto, isso só funcionará se todas as suas execuções de script que fizerem referência a essa pasta compartilhada ocorrerem no mesmo destino de computação e o destino de computação não será reciclado entre as execuções.

O recurso de pasta compartilhada permite ler ou gravar em uma pasta especial identificada por uma variável de ambiente, `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Exemplo
Aqui está um código Python de exemplo para usar essa pasta de compartilhamento para ler e gravar um arquivo de texto:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Para ver um exemplo mais completo, consulte o arquivo `iris_sklearn_shared_folder.py` no exemplo de projeto _Classificando a íris_.

Antes de usar esse recurso, você deve definir algumas configurações simples no arquivo `.compute` representando o contexto de execução de destino na pasta `aml_config`. O caminho real para essa pasta pode variar conforme o destino de computação que você escolher e o valor que você configurar.

### <a name="configure-local-compute-context"></a>Configurar contexto de computação local

Para habilitar esse recurso em um contexto de computação local, basta adicionar a seguinte linha ao seu arquivo `.compute` representando o ambiente _local_ (geralmente denominado `local.compute`).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

O `~/.azureml/share` é o caminho padrão da pasta base. Você pode alterá-lo para qualquer caminho absoluto local acessível pela execução do script. Nome da conta de experimentação, nome do Espaço de Trabalho e nome do Projeto são adicionados automaticamente ao diretório base, que compõe o caminho completo do diretório compartilhado. Por exemplo, os arquivos podem ser gravados (e recuperados) no seguinte caminho se você usar o valor padrão anterior:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-docker-compute-context-local-or-remote"></a>Configurar o contexto de computação do Docker (local ou remoto)
Para habilitar esse recurso em um contexto de computação do Docker, você precisa adicionar as duas linhas a seguir ao seu arquivo _.compute_ local ou remoto do Docker.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>`sharedVolume` deve ser definido como `true` quando você usa a variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` para acessar a pasta compartilhada, caso contrário, a execução falhará.

O código em execução no contêiner do Docker sempre vê essa pasta compartilhada como `/azureml-share/`. Esse caminho de pasta, como visto pelo contêiner do Docker, não é configurável. E você não deve assumir uma dependência desse nome de pasta em seu código. Em vez disso, use sempre o nome da variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` para referir-se a essa pasta. Ele é mapeado para uma pasta local no contexto de computação/computador host do Docker. O diretório base desta pasta local é o valor configurável da configuração `nativeSharedDirectory` no arquivo `.compute`. O caminho local da pasta compartilhada no computador host, se você usar o valor padrão acima, é o seguinte:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!TIP]
>Observe que o caminho da pasta compartilhada no disco local é o mesmo entre um contexto de computação local e um contexto de computação local do Docker. Isso significa que você pode, inclusive, compartilhar arquivos entre uma execução local e uma execução do Docker local.

Você pode colocar dados de entrada diretamente nessas pastas e esperar que as execuções do Docker ou locais naquele computador consigam pegá-los. Você também pode gravar arquivos nesta pasta das suas execuções do Docker ou locais, e esperar que os arquivos persistam naquela pasta, sobrevivendo ao ciclo de vida de execução.

Para obter mais informações nos arquivos de configuração no Serviço de Execução do Azure ML, consulte este artigo: [Arquivos de Configuração de Execução](experiment-execution-configuration-reference.md).

>[!NOTE]
>A variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` não tem suporte no contexto de computação do HDInsight. No entanto, é fácil obter o mesmo resultado usando explicitamente um caminho absoluto do WASB para ler/gravar no Armazenamento de Blobs conectado.

## <a name="option-3-use-external-durable-storage"></a>Opção 3: usar o armazenamento durável externo

Obviamente, você pode usar o repositório durável externo para manter o estado durante a execução. Isso é útil nos seguintes cenários:
- Seus dados de entrada já estão armazenados em um armazenamento durável acessível do ambiente de computação de destino.
- Esses arquivos não precisam fazer parte dos registros de histórico de execução.
- Esses arquivos precisam ser compartilhados por execuções entre diferentes ambientes de computação.
- Esses arquivos precisam sobreviver ao contexto de computação em si.

Um exemplo disso é usar o Armazenamento de Blobs do Azure no seu código Python/PySpark.

Aqui está um exemplo rápido de usar o Armazenamento de Blobs do Azure no código Python:
```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Aqui está também um exemplo rápido para anexar qualquer Armazenamento de Blobs do Azure arbitrário a um tempo de execução do HDI Spark:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusão
Uma vez que o Azure ML executa scripts copiando toda a pasta do projeto para o contexto de computação de destino, é preciso ter cuidado especial com arquivos grandes de entrada, saída e intermediários. Você pode usar a pasta `outputs` especial, a pasta compartilhada acessível por meio da variável de ambiente `AZUREML_NATIVE_SHARE_DIRECTORY` ou armazenamento durável externo para transações de arquivo grande. 

## <a name="next-steps"></a>Próximas etapas
- Examine [Configurando a execução de Experimentação](experiment-execution-configuration-reference.md).
- Veja como o projeto do tutorial [Classificando a íris](tutorial-classifying-iris-part-1.md) usa a pasta `outputs` para manter o modelo treinado.