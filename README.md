## <a name="microsoft-open-source-code-of-conduct"></a>Código de Conduta de Open Source da Microsoft

Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/).
Para obter mais informações, confira as [Perguntas frequentes sobre o código de conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou entre em contato com [opencode@microsoft.com](mailto:opencode@microsoft.com) para enviar outras perguntas ou comentários.

# <a name="azure-technical-documentation-contributor-guide"></a>Guia do colaborador da documentação técnica do Azure
Você encontrou o repositório do GitHub que hospeda a fonte da documentação técnica do Azure publicada em [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure).

Esse repositório também contém orientações para ajudá-lo a contribuir com a nossa documentação técnica. Para obter uma lista de artigos do guia do colaborador, confira o [índice](contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribua com a documentação do Azure
Obrigado por seu interesse na documentação do Azure.

* [Formas de contribuição](#ways-to-contribute)
* [Código de conduta](#code-of-conduct)
* [Sobre as suas contribuições ao conteúdo do Azure](#about-your-contributions-to-azure-content)
* [Organização do repositório](#repository-organization)
* [Como usar o GitHub, o Git e este repositório](#use-github-git-and-this-repository)
* [Como usar o markdown para formatar seu tópico](#how-to-use-markdown-to-format-your-topic)
* [Mais recursos](#more-resources)
* [Índice de todos os artigos do guia do colaborador](contributor-guide/contributor-guide-index.md) (abre uma nova página)

## <a name="ways-to-contribute"></a>Formas de contribuição
Você pode enviar atualizações para a [documentação do Azure](https://docs.microsoft.com/azure) da seguinte maneira:

* Você pode contribuir facilmente com artigos técnicos na interface do usuário do GitHub. Localize o artigo neste repositório ou acesse o site [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) e clique no link do artigo para ir para a fonte do GitHub do artigo.
* Se você estiver fazendo alterações importantes em um artigo existente, adicionando ou alterando imagens ou contribuindo com um novo artigo, precisará bifurcar esse repositório, instalar o Git Bash, o Markdown Pad e aprender alguns comandos git.

## <a name="about-your-contributions-to-azure-content"></a>Sobre as suas contribuições ao conteúdo do Azure
### <a name="minor-corrections"></a>Correções secundárias
As correções secundárias ou esclarecimentos enviados para a documentação e os exemplos de código deste repositório são cobertos pelos [Termos de Uso de docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Contribuições maiores
Se enviar uma solicitação pull com alterações novas ou significativas para a documentação e para os exemplos de código, enviaremos um comentário no GitHub solicitando que você envie um CLA (Contrato de Licença de Contribuição) online caso você não seja um funcionário da Microsoft. Precisamos que você preencha o formulário online antes de aceitarmos sua solicitação pull.

## <a name="repository-organization"></a>Organização do repositório
O conteúdo do repositório azure-docs segue a organização da documentação em https://docs.microsoft.com/azure. Esse repositório contém duas pastas raiz:

### <a name="articles"></a>\articles
A pasta *\articles* contém os artigos de documentação formatados como arquivos de markdown com uma extensão *.md*. Os artigos são agrupados pelo serviço do Azure.

Os artigos precisam seguir diretrizes estritas de nomenclatura de arquivo. Para obter detalhes, confira [nossas diretrizes de nomenclatura de arquivo](contributor-guide/file-names-and-locations.md).

A pasta *\articles* contém a pasta *\media* para arquivos de mídia do artigo do diretório raiz, onde há subpastas com as imagens de cada artigo.  As pastas de serviço contêm uma pasta de mídia separada para os artigos em cada pasta de serviço. As pastas de imagens do artigo têm nomes idênticos ao do arquivo do artigo, menos a extensão de arquivo *.md* .

### <a name="includes"></a>\includes
Você pode criar seções de conteúdo reutilizável para serem incluídas em um ou mais artigos. Confira [Extensões personalizadas usadas em nosso conteúdo técnico](contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>\markdown templates
Essa pasta contém nosso modelo de markdown padrão com a formatação básica de markdown necessária para um artigo.

### <a name="contributor-guide"></a>\contributor-guide
Essa pasta contém artigos que fazem parte do nosso guia do colaborador.

## <a name="use-github-git-and-this-repository"></a>Como usar o GitHub, o Git e este repositório
Para saber mais sobre como contribuir, como usar a IU do GitHub para contribuir com alterações pequenas e como bifurcar e clonar o repositório para contribuições mais significativas, confira [Install and set up tools for authoring in GitHub](contributor-guide/tools-and-setup.md)(Instalar e configurar as ferramentas de criação no GitHub).

Se instalar o GitBash e optar por trabalhar localmente, as etapas para criar um novo branch de trabalho local, para fazer alterações e para enviar as alterações de volta ao branch principal serão listadas em [Git commands for creating a new article or updating an existing article](contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Branches
É recomendável que você crie branches de trabalho locais destinados a um escopo de alteração específico. Cada branch deve ser limitado a um único conceito/artigo para simplificar o fluxo de trabalho e reduzir a possibilidade de conflitos de mesclagem.  Os seguintes itens fazem parte do escopo apropriado de um branch novo:

* Um artigo novo (e as imagens associadas)
* Edições de ortografia e gramática em um artigo.
* Aplicação de uma única alteração de formatação em um grande conjunto de artigos (por exemplo, um novo rodapé de direitos autorais).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Como usar o markdown para formatar seu tópico
Todos os artigos desse repositório usam o markdown do GitHub.  Veja uma lista de recursos.

* [Noções básicas de markdown](https://help.github.com/articles/markdown-basics/)
* [Folha de referência de markdowns que pode ser impressa](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>Metadados do artigo
Os metadados do artigo habilitam certas funcionalidades, como a atribuição de autor, a atribuição de colaborador, as trilhas, as descrições do artigo e as otimizações de SEO, bem como os relatórios usados pela Microsoft para a avaliação do desempenho do conteúdo. Portanto, os metadados são importantes! [Veja as diretrizes para garantir que seus metadados estejam corretos](contributor-guide/article-metadata.md).

### <a name="labels"></a>Rótulos
Rótulos automatizados são atribuídos a solicitações pull para nos ajudar a gerenciar o fluxo de trabalho de solicitação pull e ajudá-lo a saber o que está acontecendo com a solicitação de recepção:

* Contrato de Licença de Contribuição relacionado
  * cla-not-required: a alteração é relativamente pequena e não requer que você assine um CLA.
  * cla-required: o escopo da alteração é relativamente grande e exige que você assine um CLA.
  * cla-signed: o colaborador assinou o CLA, assim, a solicitação pull agora pode ir para a revisão.
* Rótulos de pilar: rótulos como PnP, Aplicativos Modernos e TDC ajudam a categorizar as solicitações pull pela organização interna que precisa examinar a solicitação pull.
* Alteração enviada ao autor: o autor foi notificado da solicitação pull pendente.

## <a name="more-resources"></a>Mais recursos
Acesse o [índice do nosso guia do colaborador](contributor-guide/contributor-guide-index.md) para conferir todos os tópicos de orientação.

