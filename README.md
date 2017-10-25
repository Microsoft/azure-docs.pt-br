## <a name="microsoft-open-source-code-of-conduct"></a>Código de Conduta de Software Livre da Microsoft

Este projeto adotou o [Código de Conduta Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/).
Para obter mais informações, confira as [Perguntas frequentes sobre o código de conduta](https://opensource.microsoft.com/codeofconduct/faq/) ou entre em contato com [opencode@microsoft.com](mailto:opencode@microsoft.com) para enviar outras perguntas ou comentários.

## <a name="contribute-to-azure-technical-documentation"></a>Contribuir com a documentação técnica do Azure
Apreciamos as contribuições de nossa comunidade (usuários, clientes, parceiros e funcionários do MSFT fora das unidades básicas de produtos do Azure, etc.), bem como de funcionários que trabalham em unidades básicas de produtos do Azure. A forma como você contribui depende de quem você é:

* **Comunidade – atualizações secundárias**: se estiver contribuindo com atualizações secundárias de forma espontânea, encontre o artigo neste repositório ou visite o artigo em [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) e clique no link **Editar** no artigo que leva à fonte no GitHub do artigo. Em seguida, basta usar a interface do usuário do GitHub para fazer as atualizações. Caso contrário, fique à vontade para criar um fork do repositório e enviar atualizações do fork.

* **Comunidade – novos artigos**: se fizer parte da comunidade do Azure e desejar criar um novo artigo, precisará para trabalhar com um funcionário para ajudar a inserir esse novo conteúdo por meio de uma combinação de trabalho nos repositórios público e privado.

* **Funcionários**: se você for um escritor técnico, gerente de programa ou desenvolvedor da equipe de produtos de um serviço do Azure e seu trabalho for contribuir ou escrever artigos técnicos, use o repositório privado (https://github.com/MicrosoftDocs/azure-docs-pr). Se estiver fazendo alterações significativas em um artigo existente, adicionando ou alterando imagens ou contribuindo com um novo artigo, precisará criar um fork desse repositório, instalar o Git Bash, um Markdown editor e aprender alguns comandos Git. Consulte [o guia do colaborador interno](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) para obter mais informações.


## <a name="about-your-contributions-to-azure-content"></a>Sobre as suas contribuições ao conteúdo do Azure
### <a name="minor-corrections"></a>Correções secundárias
As correções secundárias ou esclarecimentos enviados para a documentação e os exemplos de código deste repositório são cobertos pelos [Termos de Uso de docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Contribuições maiores
Se enviar uma solicitação pull com alterações novas ou significativas para a documentação e para os exemplos de código, enviaremos um comentário no GitHub solicitando que você envie um CLA (Contrato de Licença de Contribuição) online caso você não seja um funcionário da Microsoft. Precisamos que você preencha o formulário online antes de aceitarmos sua solicitação pull.

## <a name="tools-and-setup"></a>Ferramentas e configuração
Os colaboradores da comunidade podem usar a interface do usuário do GitHub ou criar um fork do repositório para fazer sua contribuição. Os funcionários devem acessar [o guia do colaborador interno](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) para obter mais informações sobre como contribuir para a documentação técnica.

## <a name="repository-organization"></a>Organização do repositório
O conteúdo do repositório azure-docs segue a organização da documentação em https://docs.microsoft.com/azure. Esse repositório contém duas pastas raiz:

### <a name="articles"></a>\articles
A pasta *\articles* contém os artigos de documentação formatados como arquivos de markdown com uma extensão *.md*. Os artigos são agrupados pelo serviço do Azure.

A pasta *\articles* contém a pasta *\media* para arquivos de mídia do artigo do diretório raiz, onde há subpastas com as imagens de cada artigo.  As pastas de serviço contêm uma pasta de mídia separada para os artigos em cada pasta de serviço. As pastas de imagens do artigo têm nomes idênticos ao do arquivo do artigo, menos a extensão de arquivo *.md* .

### <a name="includes"></a>\includes
Você pode criar seções de conteúdo reutilizável para serem incluídas em um ou mais artigos. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Como usar o markdown para formatar seu tópico
Todos os artigos desse repositório usam o markdown do GitHub.  Veja uma lista de recursos.

* [Noções básicas de markdown](https://help.github.com/articles/markdown-basics/)
* [Folha de referência de markdowns que pode ser impressa](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)


## <a name="labels"></a>Rótulos
No repositório público azure-docs, os rótulos automatizados são atribuídos a solicitações de pull para nos ajudar a gerenciar o fluxo de trabalho de solicitações de pull e ajudá-lo a saber o que está acontecendo com sua solicitação de pull:

* Contrato de Licença de Contribuição relacionado
  * cla-not-required: a alteração é relativamente pequena e não requer que você assine um CLA.
  * cla-required: o escopo da alteração é relativamente grande e exige que você assine um CLA.
  * cla-signed: o colaborador assinou o CLA, assim, a solicitação pull agora pode ir para a revisão.
* Alteração enviada ao autor: o autor foi notificado da solicitação pull pendente.
* ready-to-merge: pronto para revisão por nossa equipe de revisão de solicitações de pull.


