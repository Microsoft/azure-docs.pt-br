---
title: Tipos de classe de exemplo no Azure Lab Services | Microsoft Docs
description: Fornece alguns tipos de classes para os quais você pode configurar laboratórios usando o Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627426"
---
# <a name="class-types-overview---azure-lab-services"></a>Visão geral dos tipos de classes – Azure Lab Services

O Azure Lab Services permite que você configure rapidamente ambientes de laboratório de sala de aula na nuvem. Os artigos nesta seção fornecem orientação sobre como configurar vários tipos de laboratórios usando Azure Lab Services.

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) é um tipo de GIS (sistema de informações geográficas).  Você pode configurar um laboratório que usa vários aplicativos do ArcGIS Desktop, como o [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) para fazer, editar e analisar mapas 2D.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para o ArcMap\ArcGIS desktop](class-type-arcgis.md).

## <a name="big-data-analytics"></a>Análise de Big Data
Você pode configurar um laboratório de GPU para ensinar uma classe de análise de Big Data. Com esse tipo de classe, os alunos aprendem como lidar com grandes volumes de dados e aplicam algoritmos de aprendizado estatísticos e de máquina para gerar informações de dados. Um objetivo fundamental para os alunos é aprender a usar as ferramentas de análise de dados, como o pacote de software livre Apache Hadoop, que fornece ferramentas para armazenar, gerenciar e processar Big Data. 

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para análise de Big data usando a implantação do Docker da plataforma de dados do HortonWorks](class-type-big-data-analytics.md).

## <a name="database-management"></a>Gerenciamento de banco de dados
Os conceitos de bancos de dados são um dos cursos introdutórios ensinados na maioria dos departamentos de ciência da computação na faculdade. Você pode configurar um laboratório para uma classe básica de gerenciamento de bancos de dados nos Azure Lab Services. Por exemplo, você pode configurar um modelo de máquina virtual em um laboratório com um servidor de banco de dados [MySQL](https://www.mysql.com/) ou um servidor [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019).

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar gerenciamento de banco de dados para bancos de dados relacionais](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Aprendizado profundo no processamento em idioma natural
Configure um laboratório voltado para o aprendizado profundo em NLP (processamento em idioma natural) usando o Azure Lab Services. O NLP (processamento de idioma natural) é uma forma de IA (inteligência artificial) que habilita os computadores com tradução, reconhecimento de fala e outras funcionalidades de reconhecimento vocal. Os alunos que fazem uma aula de NLP obtêm uma VM (máquina virtual) do Linux para saber como aplicar algoritmos de rede neural para desenvolver modelos de aprendizado profundo que são usados para analisar a linguagem humana escrita.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório voltado para o aprendizado profundo no processamento em idioma natural usando o Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Hacking ético
Você pode configurar um laboratório para uma classe que se concentra no lado da perícia de hacking ético. O teste de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta obter acesso ao sistema ou rede para demonstrar vulnerabilidades que um invasor mal-intencionado pode explorar.

Em uma classe de hacking ético, os alunos podem aprender técnicas modernas para se defenderem de vulnerabilidades. Cada aluno recebe uma máquina virtual de host do Windows Server com duas máquinas virtuais aninhadas – uma com uma imagem do [Metasploitable3](https://github.com/rapid7/metasploitable3) e outra com uma imagem do [Kali Linux](https://www.kali.org/). A máquina virtual com Metasploitable é usada para fins de exploração.  A máquina virtual com Kali Linux fornece acesso às ferramentas necessárias para executar tarefas forenses.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar uma classe de hacking ético](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
[MATLAB](https://www.mathworks.com/products/matlab.html), que significa o laboratório de matriz, é a plataforma de programação do [MathWorks](https://www.mathworks.com/).  Ele combina a capacidade computacional e a visualização, tornando-a uma ferramenta popular nos campos de matemática, engenharia, física e química.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para ensinar MATLAB](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Rede com GNS3
Você pode configurar um laboratório para uma classe que se concentre em permitir que os alunos emulem, configurem, testem e solucionem problemas de redes virtuais e reais usando o software [GNS3](https://www.gns3.com/) . 

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para ensinar uma classe de rede](class-type-networking-gns3.md).

## <a name="project-lead-the-way-pltw"></a>Liderança do projeto no caminho (PLTW)
[O PLTW (líder do projeto)](https://www.pltw.org/) é uma organização sem fins lucrativos que fornece um currículo PreK-12 em toda a Estados Unidos em ciência da computação, engenharia e ciência biomédica.  Em cada classe PLTW, os alunos usam uma variedade de aplicativos de software como parte de sua experiência prática de aprendizado.

Para obter informações detalhadas sobre como configurar esses tipos de laboratório, consulte [set up Labs for Project Lead The Way classes](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Python e Jupyter Notebooks
Você pode configurar uma máquina de modelo nos Serviços de Laboratório do Azure com as ferramentas necessárias para ensinar aos alunos como usar os [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io). O Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código-fonte executável [Python](https://www.python.org/) em uma única tela chamada notebook. A execução de um notebook resulta em um registro linear de entradas e saídas.  Essas saídas podem incluir texto, tabelas de informações, gráficos de dispersão e muito mais.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar ciência de dados com Python e Jupyter Notebooks](class-type-jupyter-notebook.md).

## <a name="shell-scripting-on-linux"></a>Script de Shell no Linux
Configure um laboratório para ensinar o script de shell no Linux. O script é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de exemplo, a classe abrange scripts do Bash tradicionais e scripts avançados. Scripts avançados são scripts que combinam comandos do Bash e o Ruby. Essa abordagem permite que o Ruby passe os dados e que os comandos do Bash interajam com o shell.

Os alunos que fazem essas aulas de script obtêm uma máquina virtual do Linux para aprender os conceitos básicos do Linux e também se familiarizarem com os scripts do shell Bash. A máquina virtual do Linux é fornecida com o acesso à Área de Trabalho Remota habilitado e com os editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Script de shell no Linux](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>CAD (design assistido por computador) da SolidWorks
Você pode configurar um laboratório de GPU que dá aos alunos de engenharia acesso ao [SolidWorks](https://www.solidworks.com/).  O SolidWorks fornece um ambiente CAD 3D para modelar objetos sólidos.  Com o SolidWorks, os engenheiros podem facilmente criar, Visualizar, simular e documentar seus designs.

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, consulte [configurar um laboratório para classes de engenharia usando o SolidWorks](class-type-solidworks.md).

## <a name="sql-database-and-management"></a>Banco de dados SQL e gerenciamento
O linguagem SQL (SQL) é a linguagem padrão para o gerenciamento de banco de dados relacional, incluindo a adição, o acesso e o gerenciamento de conteúdo em um banco de dados.  Você pode configurar um laboratório para ensinar conceitos de banco de dados usando o servidor de banco de dados [MySQL](https://www.mysql.com/) e o servidor [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Para obter informações detalhadas sobre como configurar esse tipo de laboratório, confira [Configurar um laboratório para ensinar gerenciamento de banco de dados para bancos de dados relacionais](class-type-database-management.md).

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Configurar um laboratório voltado para o aprendizado profundo em processamento em idioma natural usando o Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Script de Shell no Linux](class-type-shell-scripting-linux.md)
- [Hacking ético](class-type-ethical-hacking.md)