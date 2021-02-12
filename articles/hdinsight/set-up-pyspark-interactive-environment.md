---
title: Ambiente interativo do PySpark com as ferramentas do Azure HDInsight
description: Saiba como usar as Ferramentas do Azure HDInsight para Visual Studio Code para criar e enviar consultas e scripts.
keywords: VSCode, Ferramentas do Azure HDInsight, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Hive Interativo, Consulta Interativa
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: 337a9f3f2ea25e5a4d4fa4204a0f3fa4dcc9369b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940636"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Configurar o ambiente interativo do PySpark para o Visual Studio Code

As etapas a seguir mostram como configurar o ambiente interativo do PySpark no VSCode. Esta etapa é apenas para usuários que não são do Windows.

Usamos comando **python/pip** para criar o ambiente virtual em seu caminho de Página Inicial. Se você deseja usar outra versão, precisa alterar a versão padrão do comando **python/pip** manualmente. Para obter mais detalhes, confira [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Instale o [Python](https://www.python.org/downloads/) e o [Pip](https://pip.pypa.io/en/stable/installing/).

   * Instale o Python do [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * Instale o Pip do [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (se ele não estiver instalado na instalação do Python).
   * Opcionalmente, valide se o Python e o Pip são instalados com êxito usando os comandos `python --version` e `pip --version` , respectivamente. 

     > [!NOTE]
     > É recomendável instalar o Python manualmente em vez de usar a versão padrão do macOS.

2. Instale **virtualenv** executando o comando a seguir.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Outros pacotes

No Linux, se você entrar na mensagem de erro abaixo, instale os pacotes necessários executando os dois comandos a seguir.

   ![Instalar o pacote libkrb5 para Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Reinicie o VSCode e, em seguida, volte para o editor VSCode e execute o comando do **Spark: PySPark Interactive** .

## <a name="next-steps"></a>Próximas etapas

### <a name="demo"></a>Demonstração

* HDInsight para VS Code: [Vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar a ferramenta Azure HDInsight para Visual Studio Code](hdinsight-for-vscode.md)
* [Use o Azure Toolkit for IntelliJ para criar e enviar aplicativos Scala do Apache Spark](spark/apache-spark-intellij-tool-plugin.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
