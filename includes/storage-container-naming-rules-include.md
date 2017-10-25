Todos os blobs no armazenamento do Azure devem residir em um contêiner. O contêiner faz parte do nome do blob. Por exemplo, `mycontainer` é o nome do contêiner nesses URIs do blob de exemplo:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Um nome de contêiner deve ser um nome DNS válido e estar em conformidade com as seguintes regras de nomenclatura:

1. Os nomes de contêiner devem começar com uma letra ou número e podem conter apenas letras, números e o caractere traço (-).
2. Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou número. Não são permitidos traços consecutivos em nomes de contêiner.
3. Todas as letras do nome de um contêiner devem ser minúsculas.
4. Os nomes de contêiner devem ter de 3 a 63 caracteres.

> [!IMPORTANT]
> Observe que o nome de um contêiner deve sempre estar em minúsculas. Se você incluir uma letra maiúscula em um nome de contêiner ou de alguma forma violar as regras de nomenclatura do contêiner, você receberá um erro 400 (solicitação incorreta). 
> 
> 

