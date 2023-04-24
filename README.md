# Como pesquisar produtos no Mercado Livre e exportar os resultados para um arquivo CSV

Este tutorial tem como objetivo explicar o código Python apresentado acima, que faz a pesquisa de produtos no site do Mercado Livre e exporta os resultados para um arquivo CSV.

## Requisitos

Antes de iniciar, é necessário ter instalado as seguintes bibliotecas:

-   **requests**: usada para fazer requisições HTTP;
-   **BeautifulSoup**: usada para manipular HTML;
-   **csv**: usada para ler e escrever arquivos CSV.

Você pode instalar as bibliotecas com os seguintes comandos no terminal:

Copy code
`pip install requests
pip install beautifulsoup4` 

## Como o código funciona

### Importação de bibliotecas

A primeira parte do código importa as bibliotecas necessárias:

javascriptCopy code

`import requests`
`from bs4 import BeautifulSoup`
`import csv` 

A biblioteca `requests` é usada para fazer requisições HTTP, `BeautifulSoup` é usada para manipular o HTML da página do Mercado Livre e `csv` é usada para ler e escrever arquivos CSV.

### Função `pesquisar_produtos_mercado_livre`

A função `pesquisar_produtos_mercado_livre` é definida com os seguintes parâmetros: `produto`, `num_paginas`, `preco_min` e `preco_max`. Essa função realiza a pesquisa de produtos no site do Mercado Livre.

`def pesquisar_produtos_mercado_livre(produto, num_paginas, preco_min, preco_max):` 

A variável `url_base` é definida para armazenar a URL base da página de pesquisa de produtos do Mercado Livre:

`url_base = "https://lista.mercadolivre.com.br/{}"` 

A variável `produtos` é criada como uma lista vazia para armazenar os resultados da pesquisa:

`produtos = []` 

O loop `for` é usado para percorrer o número de páginas informado pelo usuário:

scssCopy code

`for i in range(num_paginas):` 

Dentro do loop, a variável `url` é formatada para incluir o nome do produto e a posição inicial da página:

`url = url_base.format(produto) + f"_Desde_{i*50+1}"` 

O número 50 é o número máximo de produtos exibidos por página no Mercado Livre. A posição inicial da página é calculada multiplicando o número da página pelo número máximo de produtos exibidos e somando 1.

A função `requests.get` é usada para fazer uma requisição GET para a URL e armazenar a resposta na variável `resposta`:

`resposta = requests.get(url, verify=False)` 

O parâmetro `verify=False` é usado para ignorar a verificação do certificado SSL.

A função `BeautifulSoup` é usada para criar um objeto `soup` a partir do texto da resposta HTML:

`soup = BeautifulSoup(resposta.text, "html.parser")` 

A função `find_all` é usada para encontrar todos os elementos `li` com a classe `ui-search-layout__item` e armazenar na variável `itens`:

`itens = soup.find_all("li", class_="ui-search-layout__item")` 

Dentro do loop `for`, a variável `titulo` é usada para extrair o título do produto e remover espaços em br
Continuação...

`for item in itens:
    titulo = item.find("h2", class_="ui-search-item__title").text.strip()` 

A função `strip` é usada para remover os espaços em branco do começo e do final do texto.

A variável `link` é usada para extrair o link do produto:

`link = item.find("a", class_="ui-search-link")["href"]` 

As variáveis `preco_original` e `preco_desconto` são usadas para extrair o preço original e o preço com desconto do produto, respectivamente:

`preco_tags = item.find_all("span", class_="price-tag-fraction")`
`preco_original = preco_tags[0].text.strip()`
`if len(preco_tags) > 1:`
`    preco_desconto = preco_tags[1].text.strip()`
`else:`
`    preco_desconto = preco_original` 

A função `float` é usada para converter os preços para um número float:


`preco_original_float = float(preco_original.replace(".", "").replace(",", "."))`
`preco_desconto_float = float(preco_desconto.replace(".", "").replace(",", "."))` 

A condição `if` é usada para verificar se o preço com desconto está dentro do intervalo especificado pelo usuário:

`if preco_min <= preco_desconto_float <= preco_max:` 

Se o preço com desconto estiver dentro do intervalo especificado, o produto é adicionado à lista de produtos:

`produtos.append({"titulo": titulo, "preco_original": preco_original, "preco_desconto": preco_desconto, "link": link})` 

A função `pesquisar_produtos_mercado_livre` retorna a lista de produtos encontrados:

`return produtos` 

### Função `exportar_para_csv`

A função `exportar_para_csv` é definida com os parâmetros `produtos` e `nome_arquivo`. Essa função exporta os resultados da pesquisa para um arquivo CSV.

`def exportar_para_csv(produtos, nome_arquivo):` 

A função `open` é usada para abrir (ou criar) o arquivo CSV no modo de escrita, com codificação UTF-8:

`with open(nome_arquivo, mode='w', newline='', encoding='utf-8') as arquivo_csv:` 

A variável `campos` é definida para armazenar os nomes dos campos que serão incluídos no arquivo CSV:

`campos = ["titulo", "preco_original", "preco_desconto", "link"]` 

O objeto `DictWriter` é criado para escrever dicionários no arquivo CSV:

`escritor = csv.DictWriter(arquivo_csv, fieldnames=campos)` 

O método `writeheader` é usado para escrever o cabeçalho no arquivo CSV com os nomes dos campos:

`escritor.writeheader()` 

O loop `for` é usado para percorrer cada produto na lista de produtos:

`for produto in produtos:` 

Dentro do loop, o método `writerow` é usado para escrever uma linha no arquivo CSV com os dados do produto:

`escritor.writerow(produto)` 

### Solicitação de entrada do usuário

O código solicita ao usuário o nome do produto que deseja pesquisar, o número de páginas que deseja percorrer, o valor mínimo e o valor máximo do produto:

graphqlCopy code

`produto = input("Digite o produto que você deseja pesquisar: ")`
`num_pag`
`num_paginas = int(input("Digite o número de páginas que deseja percorrer: "))`
`preco_min = float(input("Digite o valor mínimo do produto: ").replace(",", "."))`
`preco_max = float(input("Digite o valor máximo do produto: ").replace(",", "."))` 

Os valores de `preco_min` e `preco_max` são convertidos para `float` e a vírgula é substituída por ponto.

A função `pesquisar_produtos_mercado_livre` é chamada e os resultados são armazenados na variável `resultados`:

`resultados = pesquisar_produtos_mercado_livre(produto, num_paginas, preco_min, preco_max)` 

O nome do arquivo CSV que será criado é definido:

`nome_arquivo = "resultados.csv"` 

A função `exportar_para_csv` é chamada para escrever os resultados no arquivo CSV:

`exportar_para_csv(resultados, nome_arquivo)` 

Uma mensagem é exibida informando que os resultados foram exportados para o arquivo CSV:

`print(f"Os resultados foram exportados para o arquivo '{nome_arquivo}'.")` 

## Conclusão

Este tutorial explicou como pesquisar produtos no Mercado Livre e exportar os resultados para um arquivo CSV usando Python. A função `pesquisar_produtos_mercado_livre` é usada para realizar a pesquisa de produtos e a função `exportar_para_csv` é usada para exportar os resultados para um arquivo CSV. O código é interativo, permitindo que o usuário informe o nome do produto, o número de páginas que deseja percorrer, o valor mínimo e o valor máximo do produto. Com este tutorial, você pode adaptar o código para suas necessidades e personalizar a pesquisa de produtos no Mercado Livre.
