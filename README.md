 
# Vendas x Metas — Análise de Business Intelligence com Power BI

Projeto de Business Intelligence desenvolvido no Microsoft Power BI para análise de vendas, acompanhamento de metas comerciais e comparação do desempenho entre 2017 e 2018.

---

## 1. Sobre o projeto

O projeto foi desenvolvido a partir do desafio **Vendas x Metas**, cujo objetivo é construir um relatório capaz de analisar a evolução do faturamento, compará-lo com as metas estabelecidas e identificar oportunidades de melhoria no desempenho comercial.

A base disponibilizada contempla o período de **01/01/2017 a 16/03/2019**.

O desafio propõe uma análise exploratória dos principais indicadores da empresa e uma análise comparativa entre 2017 e 2018, fornecendo informações que possam apoiar a diretoria e as equipes de gestão regional na revisão das projeções de faturamento e das metas comerciais.

O desenvolvimento contemplou as seguintes etapas:

- Entendimento do problema de negócio;
- Importação e exploração das fontes de dados;
- Tratamento e transformação dos dados com Power Query;
- Construção do modelo de dados;
- Criação de medidas e indicadores utilizando DAX;
- Desenvolvimento dos dashboards;
- Análise dos resultados e identificação de insights.

## 2. Objetivos de negócio

O relatório foi construído para responder às 15 perguntas propostas no documento original, divididas em dois grupos:

**Análise Exploratória**

- Avaliar o faturamento consolidado do período.
- Comparar o faturamento realizado com a meta.
- Identificar as categorias e subcategorias de maior representatividade.
- Analisar a distribuição geográfica do faturamento.

**Comparativo entre 2017 e 2018**

- Identificar a evolução do faturamento.
- Avaliar o crescimento ou a retração das subcategorias.
- Investigar a mudança de representatividade de Desktops.
- Identificar os continentes responsáveis pelas variações.
- Comparar o desempenho das categorias e subcategorias entre os anos.

---

## 3. Ferramentas e tecnologias

| Tecnologia | Aplicação |
|---|---|
| Microsoft Power BI Desktop | Desenvolvimento dos relatórios, indicadores e visualizações. |
| Power Query | Importação, limpeza e transformação dos dados. |
| Linguagem M | Criação de colunas personalizadas e regras de transformação. |
| DAX | Desenvolvimento de medidas e cálculos analíticos. |
| Microsoft Excel | Fonte de dados de vendas e metas. |
| CSV e JSON | Fontes de dados cadastrais e auxiliares. |
| GitHub | Versionamento e documentação do projeto. |

---

## 4. Fontes de dados e estrutura

Foram disponibilizados dados transacionais de vendas e metas, além de cadastros auxiliares de clientes, produtos, subcategorias e localização.

As principais tabelas utilizadas no modelo foram:

| Tabela | Descrição |
|---|---|
| `Vendas` | Registros das transações comerciais, datas, produtos, clientes, quantidades, preços, custos e descontos. |
| `Cliente` | Informações cadastrais e identificadores dos clientes. |
| `Produto` | Cadastro de produtos, marcas e identificadores de subcategoria. |
| `Subcategoria` | Classificação dos produtos em subcategorias e categorias. |
| `Localizacao` | Informações geográficas de cidades, estados/províncias e países. |
| `Metas` | Metas comerciais organizadas por categoria, continente e ano. |
| `DimAno` | Dimensão auxiliar para análise temporal. |
| `DimCategoria` | Dimensão auxiliar para padronização das categorias. |
| `DimContinente` | Dimensão auxiliar para análise geográfica. |

---

## 5. Tratamento e transformação dos dados — Power Query

A preparação dos dados foi realizada no Power Query, com o objetivo de garantir consistência, integridade e estrutura adequada para a modelagem e as análises.

### 5.1. Tabela Vendas

Na tabela de vendas foram criadas três colunas personalizadas:

**Valor Venda**

Calcula o valor efetivo da venda após a aplicação do desconto:

`Valor Venda = (Preço Unitário × Quantidade) - Valor Desconto`

**Valor Custo**

Calcula o custo total dos produtos vendidos:

`Valor Custo = Custo Unitário × Quantidade`

**Lucro Bruto**

Calcula a diferença entre o valor da venda e o custo:

`Lucro Bruto = Valor Venda - Valor Custo`

Esses campos foram utilizados para estruturar os valores financeiros e possibilitar análises posteriores.

### 5.2. Tabela Cliente

Foram realizadas verificações de qualidade dos dados, incluindo:

- Conferência da unicidade do identificador do cliente;
- Verificação de campos importantes sem preenchimento;
- Validação da identificação da cidade;
- Análise dos campos cadastrais.

Os campos de nome da empresa sem preenchimento foram considerados uma situação esperada para registros de clientes do tipo pessoa física.

### 5.3. Tabelas Produto e Subcategoria

Foram verificadas a integridade dos identificadores e a consistência da classificação dos produtos.

As verificações contemplaram:

- Unicidade do identificador do produto;
- Integridade dos identificadores de subcategoria;
- Presença dos nomes dos produtos e marcas;
- Consistência entre subcategorias e categorias.

A separação dessas informações permite analisar o faturamento em diferentes níveis de detalhamento.

### 5.4. Tabela Localizacao

A fonte de localização apresentava registros em diferentes níveis geográficos: cidade, estado/província e país/região.

Durante o tratamento foram realizadas as seguintes operações:

1. Remoção de linhas iniciais que não representavam registros da tabela.
2. Remoção de uma linha de identificação de continente que estava fora da estrutura tabular.
3. Filtragem de registros completamente vazios.
4. Verificação dos campos de cidade, estado/província e país.
5. Criação de uma coluna personalizada chamada `Continente`.

Após a limpeza, a consulta ficou com **671 registros**.

**Classificação de continentes**

A coluna `Continente` foi criada utilizando o país como referência, por meio de condições personalizadas em linguagem M.

A classificação respeitou exatamente a correspondência disponibilizada no arquivo da empresa, sem substituir sua classificação por uma divisão geográfica externa.

Foi utilizada a função `Text.Trim()` para remover espaços excedentes dos nomes dos países antes da comparação. Esse ajuste foi necessário porque alguns registros apresentavam espaços no final do texto, impedindo a correspondência correta.

Os continentes utilizados foram:

- Asia
- Europe
- North America

### 5.5. Tabela Metas

O arquivo de metas estava dividido em três planilhas: `2017`, `2018` e `2019`.

Os continentes estavam organizados em colunas, enquanto as categorias estavam dispostas em linhas.

Para adequar a estrutura ao modelo analítico, foi realizado o processo de transformação de colunas em linhas (*Unpivot Columns*).

Etapas realizadas:

1. Identificação da primeira coluna como `Categoria`.
2. Remoção da linha de total.
3. Transformação das colunas dos continentes em registros.
4. Renomeação da coluna `Atributo` para `Continente`.
5. Padronização da coluna de valores como `Meta`.
6. Criação da coluna `Ano`, identificando o ano de cada planilha.
7. Ajuste dos tipos de dados.
8. Combinação das três consultas utilizando **Acrescentar Consultas (Append Queries)**.

O resultado foi uma tabela consolidada chamada `Metas`, com **27 registros**, correspondentes a três categorias, três continentes e três anos.

---

## 6. Modelagem de dados

Após o tratamento, as tabelas foram relacionadas no modelo do Power BI para permitir a propagação dos filtros e a integração das informações.

Foram utilizadas dimensões auxiliares para organizar as informações compartilhadas entre vendas e metas.

**Relacionamentos principais:**

- `Cliente` → `Vendas`
- `Produto` → `Vendas`
- `Localizacao` → `Vendas`
- `Subcategoria` → `Produto`

**Dimensões auxiliares:**

- `DimAno`
- `DimCategoria`
- `DimContinente`

As dimensões auxiliares permitem compartilhar os filtros de ano, categoria e continente entre as análises de vendas e metas.

O relacionamento direto entre `Subcategoria` e `Vendas` foi mantido inativo para evitar ambiguidade no caminho de filtragem, uma vez que já existe um caminho ativo entre essas tabelas por meio de `Produto`.

Essa estrutura foi adotada para manter os relacionamentos organizados e evitar caminhos de filtragem ambíguos.

---

## 7. Medidas e indicadores — DAX

As medidas foram desenvolvidas com foco nas perguntas propostas pelo desafio.

Entre os principais indicadores utilizados estão:

| Indicador | Finalidade |
|---|---|
| `Faturamento Total` | Consolidar o faturamento do período. |
| `Meta Total` | Consolidar o valor das metas. |
| `% Atingimento da Meta` | Comparar o faturamento realizado com a meta estabelecida. |
| `% Participação no Faturamento` | Calcular a representatividade das categorias no faturamento total. |
| `Faturamento 2017` | Apurar o faturamento de 2017. |
| `Faturamento 2018` | Apurar o faturamento de 2018. |
| `Variação Faturamento %` | Comparar o faturamento de 2018 com o de 2017. |
| `Variação Representatividade Desktops %` | Avaliar a mudança de participação da subcategoria Desktops. |
| `Variação Desktops por Continente %` | Comparar o comportamento de Desktops entre os continentes. |
| `Faturamento por Categoria 2018` | Apresentar o faturamento por categoria em 2018. |

Também foram utilizadas medidas de faturamento por categoria e subcategoria para construção dos rankings e identificação dos maiores resultados.

---

## 8. Desenvolvimento dos relatórios

O relatório final foi organizado em duas páginas, com navegação entre elas.

### 8.1. Análise Exploratória de Vendas

Esta página apresenta a visão geral do desempenho comercial durante todo o período analisado.

![Dashboard de Análise Exploratória](./dashboard-analise-exploratoria.jpg)

**Indicadores principais**

| Indicador | Resultado |
|---|---:|
| Faturamento Total | R$ 171,38 milhões |
| Meta Total | R$ 205,02 milhões |
| Atingimento da Meta | 83,59% |

**Visualizações desenvolvidas**

- Faturamento por categoria;
- Participação percentual de cada categoria no faturamento;
- Top 3 subcategorias por faturamento;
- Faturamento por continente considerando as três principais subcategorias.

**Principais resultados**

- A categoria **Computers** apresentou o maior faturamento, com participação de **74,33%** no resultado total.
- As três subcategorias de maior faturamento foram **Projectors & Screens**, **Laptops** e **Desktops**.
- O faturamento consolidado foi de R$ 171,38 milhões, enquanto a meta totalizou R$ 205,02 milhões.
- O percentual de atingimento da meta foi de **83,59%**.

### 8.2. Comparativo de Vendas — 2017 x 2018

Esta página apresenta a evolução do faturamento entre os dois anos, destacando os segmentos que apresentaram crescimento ou retração.

![Dashboard Comparativo 2017 x 2018](./dashboard-comparativo-2017-2018.jpg)

**Indicadores principais**

| Indicador | Resultado |
|---|---:|
| Variação do Faturamento | +27,19% |
| Variação da Representatividade de Desktops | -24,94% |

**Visualizações desenvolvidas**

- Crescimento do faturamento por categoria;
- Crescimento do faturamento por subcategoria;
- Variação de Desktops por continente;
- Faturamento por categoria em 2018;
- Top 3 subcategorias por faturamento em 2018;
- Comparativo do faturamento das principais subcategorias entre 2017 e 2018.

---

## 9. Respostas às 15 perguntas do desafio

Esta seção segue a ordem das perguntas apresentadas no PDF original.

### 9.1. Análise Exploratória

| Nº | Pergunta | Resultado |
|---:|---|---|
| 1 | Qual foi o faturamento de todo o período? | **R$ 171,38 milhões** |
| 2 | Qual era a meta de faturamento deste período? | **R$ 205,02 milhões** |
| 3 | Qual foi o percentual de atendimento da meta? | **83,59%** |
| 4 | Qual foi a categoria com maior faturamento? | **Computers** |
| 5 | Quanto essa categoria representa no faturamento total? | **74,33%** |
| 6 | Quais foram as três subcategorias com maior faturamento? | **Projectors & Screens, Laptops e Desktops** |
| 7 | Considerando essas três subcategorias, quais continentes apresentaram maior faturamento? | **Europe, Asia e North America**, nessa ordem. |

### 9.2. Comparativo entre 2017 e 2018

| Nº | Pergunta | Resultado |
|---:|---|---|
| 8 | Qual foi a variação entre o faturamento de 2018 e o ano anterior? | **Crescimento de 27,19%** |
| 9 | A variação foi positiva em todas as subcategorias? | **Não. Desktops apresentou queda de 4,53%.** |
| 10 | Qual foi a variação da representatividade de Desktops entre 2018 e 2017? | **-24,94%** |
| 11 | Qual continente foi responsável pela queda de faturamento de Desktops? | **Europe, com redução de 21,08%.** |
| 12 | Qual categoria apresentou o maior faturamento em 2018? Qual o valor? | **Computers — R$ 60.015.300,44** |
| 13 | Qual categoria apresentou a maior variação percentual entre 2017 e 2018? | **TV and Video — crescimento de 131,86%.** |
| 14 | Quais foram as três subcategorias com maior faturamento em 2018? | **Projectors & Screens, Laptops e Desktops.** |
| 15 | São as mesmas subcategorias em destaque considerando todos os períodos? | **Sim. As três subcategorias permanecem no Top 3.** |

---

## 10. Principais insights

A análise dos resultados permitiu identificar alguns pontos importantes sobre o desempenho comercial.

**1. Atingimento da meta**

O faturamento consolidado foi de R$ 171,38 milhões, equivalente a 83,59% da meta de R$ 205,02 milhões.

**2. Concentração do faturamento**

A categoria Computers representa 74,33% do faturamento total, demonstrando uma concentração significativa das receitas nessa categoria.

**3. Crescimento entre os anos**

O faturamento de 2018 apresentou crescimento de 27,19% em relação a 2017.

**4. Desempenho desigual entre subcategorias**

Apesar do crescimento geral, Desktops apresentou retração de 4,53%, sendo a única subcategoria com variação negativa no comparativo.

**5. Desempenho geográfico de Desktops**

Europe apresentou redução de 21,08% no faturamento de Desktops, enquanto Asia cresceu 5,45% e North America cresceu 10,58%.

**6. Destaque de crescimento por categoria**

TV and Video apresentou a maior variação percentual entre as categorias, com crescimento de 131,86%.

**7. Consistência das principais subcategorias**

Projectors & Screens, Laptops e Desktops aparecem entre as três maiores subcategorias tanto no período completo quanto no ano de 2018.

---

## 11. Estrutura do repositório

```text
desafio-metas-vendas/
│
├── README.md
│
├── desafio-metas-vendas-final.pbix
│
├── dashboard-analise-exploratoria.jpg
│
└── dashboard-comparativo-2017-2018.jpg
```

O repositório contém o arquivo do relatório Power BI e as imagens utilizadas para apresentar os dashboards.

Os arquivos de dados originais não foram incluídos nesta publicação.

---

## 12. Como visualizar o projeto

1. Acesse o repositório no GitHub.
2. Baixe o arquivo `desafio-metas-vendas-final.pbix`.
3. Abra o arquivo utilizando o **Microsoft Power BI Desktop**.

As imagens dos dashboards também estão disponíveis nesta documentação para consulta rápida, sem necessidade de abrir o Power BI.

---

## 13. Considerações finais

O projeto permitiu aplicar, na prática, diferentes etapas de um processo de Business Intelligence, desde a exploração e preparação dos dados até a modelagem, criação de indicadores e desenvolvimento de relatórios analíticos.

Mais do que apresentar números, o objetivo foi transformar os dados disponíveis em informações que respondam a perguntas de negócio e auxiliem na identificação de tendências, oportunidades e pontos de atenção.

**Projeto desenvolvido como prática de Business Intelligence, análise de dados e visualização de informações utilizando Microsoft Power BI.**
