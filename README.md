# Desafio Vendas x Metas — Análise de Vendas com Power BI

## 1. Sobre o projeto

Este projeto foi desenvolvido para atender ao desafio **Vendas x Metas**, cujo objetivo é construir um dashboard capaz de analisar a evolução do faturamento, compará-lo com as metas comerciais e apresentar uma visão comparativa entre 2017 e 2018.

A base de dados disponibilizada contempla o período de **01/01/2017 a 16/03/2019**. O desafio propõe análises que auxiliem a avaliação do desempenho comercial, a revisão das projeções de faturamento e, quando necessário, o recálculo das metas de 2019.

O relatório foi organizado em duas páginas:

1. **Análise Exploratória de Vendas:** visão geral do faturamento, metas, participação das categorias, principais subcategorias e distribuição geográfica.
2. **Comparativo 2017 x 2018:** evolução do faturamento, desempenho por categoria e subcategoria, representatividade de Desktops e variação por continente.

## 2. Objetivos de negócio

O dashboard foi desenvolvido para responder às perguntas propostas no desafio e oferecer informações relevantes para a diretoria e as equipes de gestão regional.

As análises estão divididas em dois grupos:

- **Análise exploratória:** compreender o resultado consolidado do período e identificar a concentração do faturamento.
- **Comparativo entre anos:** avaliar o desempenho de 2018 em relação a 2017, identificar crescimentos e quedas e destacar os segmentos que merecem atenção.

## 3. Ferramentas e tecnologias

- **Microsoft Power BI Desktop:** modelagem, criação de medidas, visualizações e desenvolvimento do relatório.
- **Power Query:** extração, transformação e preparação dos dados.
- **Linguagem M:** criação de colunas personalizadas e transformações no Power Query.
- **DAX (Data Analysis Expressions):** desenvolvimento de medidas e indicadores.
- **Microsoft Excel, CSV e JSON:** formatos das fontes de dados.
- **GitHub:** versionamento e documentação do projeto.

## 4. Fontes de dados e estrutura

O desafio disponibilizou dados de vendas e metas, além de cadastros de clientes, produtos, subcategorias e localização.

As principais tabelas utilizadas no modelo foram:

| Tabela | Finalidade |
|---|---|
| `Vendas` | Registros das transações comerciais, incluindo datas, produtos, clientes, quantidades, preços, custos e descontos. |
| `Cliente` | Informações cadastrais dos clientes. |
| `Produto` | Cadastro de produtos, marcas e identificadores de subcategoria. |
| `Subcategoria` | Classificação dos produtos em subcategorias e categorias. |
| `Localizacao` | Informações geográficas de cidades, estados/províncias e países, com classificação de continente. |
| `Metas` | Metas comerciais organizadas por categoria, continente e ano. |
| `DimAno` | Dimensão auxiliar para análise temporal. |
| `DimCategoria` | Dimensão auxiliar para padronizar a análise por categoria. |
| `DimContinente` | Dimensão auxiliar para padronizar a análise por continente. |

## 5. Preparação e transformação dos dados — Power Query

A preparação dos dados foi realizada no Power Query, contemplando inspeção das fontes, limpeza de registros, padronização de campos e criação de colunas necessárias às análises.

### 5.1. Tabela Vendas

Foram criadas três colunas personalizadas:

**Valor Venda**

Calcula o valor efetivo da venda após a aplicação do desconto.

```text
Valor Venda = (Preço Unitário × Quantidade) - Valor Desconto
```

**Valor Custo**

Calcula o custo total dos itens vendidos.

```text
Valor Custo = Custo Unitário × Quantidade
```

**Lucro Bruto**

Calcula a diferença entre o valor da venda e o custo.

```text
Lucro Bruto = Valor Venda - Valor Custo
```

Essas colunas permitiram consolidar os valores financeiros necessários às análises.

### 5.2. Tabela Cliente

Foram realizadas verificações de qualidade, incluindo:

- Conferência da unicidade do identificador do cliente.
- Verificação de campos importantes sem preenchimento.
- Validação da identificação das cidades.
- Análise dos campos cadastrais.

Os nomes de empresa vazios foram considerados uma situação esperada para registros de clientes do tipo pessoa física.

### 5.3. Tabelas Produto e Subcategoria

Foram verificadas a integridade dos identificadores e a consistência da classificação dos produtos.

As verificações incluíram:

- Unicidade do identificador do produto.
- Integridade dos identificadores de subcategoria.
- Presença dos nomes dos produtos e marcas.
- Consistência da classificação entre subcategoria e categoria.

### 5.4. Tabela Localizacao

A fonte de localização apresentava registros em diferentes níveis geográficos, como cidade, estado/província e país/região.

Foram realizadas as seguintes transformações:

1. Remoção de linhas iniciais que não representavam registros da tabela.
2. Remoção de uma linha de identificação de continente fora da estrutura tabular.
3. Remoção de registros completamente vazios.
4. Verificação dos campos de cidade, estado/província e país.
5. Criação da coluna `Continente`.

Após a limpeza, a consulta ficou com **671 registros**.

A classificação de continente foi criada utilizando o país como referência e respeitando a correspondência fornecida na própria fonte da empresa.

Também foi utilizada a função `Text.Trim()` para remover espaços excedentes dos nomes dos países antes das comparações, evitando falhas de correspondência.

Os continentes utilizados foram:

- Asia
- Europe
- North America

### 5.5. Tabela Metas

O arquivo de metas estava dividido em três planilhas: `2017`, `2018` e `2019`. Os continentes estavam organizados em colunas.

Para padronizar a estrutura, foi aplicado o processo de transformação de colunas em linhas (*Unpivot Columns*).

As principais etapas foram:

1. Identificação da primeira coluna como `Categoria`.
2. Remoção da linha de total.
3. Transformação das colunas dos continentes em registros.
4. Renomeação da coluna de atributos para `Continente`.
5. Renomeação da coluna de valores para `Meta`.
6. Criação da coluna `Ano`.
7. Ajuste dos tipos de dados.
8. Combinação das três consultas utilizando **Acrescentar Consultas (Append Queries)**.

O resultado foi uma tabela consolidada `Metas`, com **27 registros**, correspondentes a três categorias, três continentes e três anos.

## 6. Modelagem de dados

Após a preparação, as tabelas foram relacionadas no modelo do Power BI.

Os principais relacionamentos utilizados foram:

- `Cliente` → `Vendas`
- `Produto` → `Vendas`
- `Localizacao` → `Vendas`
- `Subcategoria` → `Produto`

Também foram utilizadas dimensões auxiliares compartilhadas entre as análises de vendas e metas:

- `DimAno`
- `DimCategoria`
- `DimContinente`

Essas dimensões permitem que os filtros de ano, categoria e continente sejam aplicados de maneira consistente nas tabelas de vendas e metas.

O relacionamento direto entre `Subcategoria` e `Vendas` foi mantido inativo para evitar ambiguidade, pois já existe um caminho ativo entre essas tabelas por meio de `Produto`.

## 7. Medidas e indicadores — DAX

As medidas foram desenvolvidas de acordo com as perguntas de negócio do desafio.

Entre os principais indicadores utilizados estão:

| Medida | Finalidade |
|---|---|
| `Faturamento Total` | Consolidar o faturamento do período. |
| `Meta Total` | Consolidar o valor das metas. |
| `Atingimento da Meta %` | Comparar o faturamento realizado com a meta. |
| `% Participação no Faturamento` | Calcular a representatividade das categorias no faturamento total. |
| `Faturamento 2017` | Apurar o faturamento de 2017. |
| `Faturamento 2018` | Apurar o faturamento de 2018. |
| `Variação Faturamento %` | Comparar o faturamento de 2018 com o de 2017. |
| `Participação Desktops 2017 %` | Calcular a participação de Desktops no faturamento de 2017. |
| `Participação Desktops 2018 %` | Calcular a participação de Desktops no faturamento de 2018. |
| `Variação Representatividade Desktops %` | Avaliar a mudança da participação de Desktops entre os anos. |
| `Variação Desktops por Continente %` | Comparar o comportamento de Desktops entre os continentes. |
| `Faturamento por Categoria 2018` | Apresentar o faturamento de cada categoria em 2018. |

As medidas permitem que os indicadores sejam recalculados dinamicamente de acordo com os filtros aplicados no relatório.

## 8. Desenvolvimento dos dashboards

O relatório final possui duas páginas.

### 8.1. Análise Exploratória de Vendas

A primeira página apresenta uma visão consolidada do desempenho comercial durante todo o período analisado.

![Dashboard de Análise Exploratória](./dashboard-analise-exploratoria.jpg)

**Principais indicadores:**

| Indicador | Resultado |
|---|---:|
| Faturamento total | R$ 171.380.000,00 (aproximadamente) |
| Meta total | R$ 205.020.000,00 (aproximadamente) |
| Atingimento da meta | 83,59% |
| Categoria de maior faturamento | Computers |
| Participação de Computers | 74,33% |

**Principais visualizações:**

- Faturamento por categoria.
- Participação percentual das categorias no faturamento.
- Top 3 subcategorias por faturamento.
- Faturamento por continente considerando as três principais subcategorias.

**Top 3 subcategorias de todo o período:**

1. Projectors & Screens
2. Laptops
3. Desktops

**Faturamento por continente, considerando as três principais subcategorias:**

| Continente | Faturamento aproximado |
|---|---:|
| Europe | R$ 41 milhões |
| Asia | R$ 35 milhões |
| North America | R$ 33 milhões |

### 8.2. Comparativo 2017 x 2018

A segunda página apresenta a evolução do faturamento entre 2017 e 2018, destacando o desempenho das categorias, subcategorias e continentes.

![Dashboard Comparativo 2017 x 2018](./dashboard-comparativo-2017-2018.jpg)

**Comparativo anual de faturamento e metas:**

| Ano | Faturamento | Meta | Atingimento |
|---|---:|---:|---:|
| 2017 | R$ 67.197.712,59 | R$ 74.361.964,00 | 90,37% |
| 2018 | R$ 85.470.908,87 | R$ 80.727.644,63 | 105,88% |

**Principais indicadores comparativos:**

| Indicador | Resultado |
|---|---:|
| Variação do faturamento | +27,19% |
| Variação da representatividade de Desktops | -24,94% |
| Variação total do faturamento de Desktops | -4,53% |
| Maior retração de Desktops por continente | Europe (-21,08%) |

## 9. Respostas às 15 perguntas do desafio

Esta seção apresenta as respostas na mesma ordem do PDF original.

### 9.1. Análise exploratória

| Nº | Pergunta | Resposta |
|---:|---|---|
| 1 | Qual foi o faturamento de todo o período? | R$ 171,38 milhões. |
| 2 | Qual era a meta de faturamento deste período? | R$ 205,02 milhões. |
| 3 | Qual foi o percentual de atendimento da meta? | 83,59%. |
| 4 | Qual foi a categoria com maior faturamento? | Computers. |
| 5 | O quanto esta categoria representa no total do faturamento? | 74,33%. |
| 6 | Quais foram as três subcategorias com maior faturamento? | Projectors & Screens, Laptops e Desktops. |
| 7 | Considerando essas três subcategorias, quais são os continentes em ordem decrescente de faturamento? | Europe, Asia e North America. |

### 9.2. Comparativo entre 2017 e 2018

| Nº | Pergunta | Resposta |
|---:|---|---|
| 8 | Qual foi a variação entre o faturamento de 2018 e o ano anterior? | Crescimento de 27,19%. |
| 9 | A variação do faturamento foi positiva em todas as subcategorias? | Não. Desktops apresentou queda de 4,53%. |
| 10 | Qual foi a variação percentual da representatividade de Desktops? | Redução de 24,94%. |
| 11 | Qual foi o continente responsável pela queda de faturamento de Desktops? | Europe, com variação de -21,08%. |
| 12 | Qual foi a categoria com maior faturamento em 2018 e seu valor? | Computers, com R$ 60.015.300,44. |
| 13 | Qual foi a categoria com a maior variação percentual entre 2017 e 2018? | TV and Video, com crescimento de 131,86%. |
| 14 | Quais foram as três subcategorias com maior faturamento em 2018? | Projectors & Screens, Laptops e Desktops. |
| 15 | São as mesmas subcategorias em destaque considerando todos os períodos? | Sim. As três são Projectors & Screens, Laptops e Desktops. |

### 9.3. Crescimento por categoria

| Categoria | Variação do faturamento |
|---|---:|
| TV and Video | +131,86% |
| Audio | +59,00% |
| Computers | +7,55% |
| **Total** | **+27,19%** |

### 9.4. Crescimento por subcategoria

| Subcategoria | Variação do faturamento |
|---|---:|
| Home Theater System | +414,32% |
| Recording Pen | +221,02% |
| Televisions | +194,15% |
| Monitors | +98,48% |
| Bluetooth Headphones | +47,61% |
| MP4&MP3 | +16,66% |
| Car Video | +11,99% |
| Laptops | +10,44% |
| VCD & DVD | +9,86% |
| Printers, Scanners & Fax | +2,36% |
| Projectors & Screens | +0,81% |
| Desktops | -4,53% |

### 9.5. Variação de Desktops por continente

| Continente | Variação |
|---|---:|
| Europe | -21,08% |
| Asia | +5,45% |
| North America | +10,58% |
| **Total** | **-4,53%** |

### 9.6. Top 3 subcategorias por faturamento em 2018

| Subcategoria | Faturamento em 2018 |
|---|---:|
| Projectors & Screens | R$ 23.146.231,19 |
| Laptops | R$ 16.621.569,42 |
| Desktops | R$ 10.885.103,36 |
| **Total** | **R$ 50.652.903,97** |

## 10. Principais insights

A análise dos resultados permite destacar os seguintes pontos:

1. **Atingimento da meta:** o faturamento consolidado foi de R$ 171,38 milhões, equivalente a 83,59% da meta total de R$ 205,02 milhões.
2. **Concentração do faturamento:** Computers representa 74,33% do faturamento total, demonstrando forte concentração nessa categoria.
3. **Crescimento anual:** o faturamento de 2018 cresceu 27,19% em relação a 2017.
4. **Crescimento desigual:** apesar do crescimento geral, Desktops apresentou queda de 4,53% no faturamento.
5. **Ponto de atenção geográfico:** Europe apresentou a maior retração de faturamento de Desktops, com -21,08%.
6. **Destaque de categoria:** TV and Video apresentou a maior variação percentual entre as categorias, com crescimento de 131,86%.
7. **Consistência no ranking:** Projectors & Screens, Laptops e Desktops aparecem entre as três maiores subcategorias tanto no período completo quanto em 2018.

Os resultados ajudam a identificar oportunidades de crescimento, concentração de receita e segmentos que merecem atenção na revisão das projeções comerciais.

## 11. Observação sobre os dados de 2019

A base disponibilizada pelo desafio termina em **16/03/2019**. Portanto, os resultados de 2019 representam apenas uma parte do ano.

O atingimento da meta de 2019 não deve ser interpretado como resultado anual definitivo, pois o período de vendas disponível é parcial.

## 12. Estrutura do repositório

```text
desafio-metas-vendas/
│
├── README.md
├── desafio-metas-vendas-final.pbix
├── dashboard-analise-exploratoria.jpg
└── dashboard-comparativo-2017-2018.jpg
```

O repositório contém o arquivo do relatório Power BI e as imagens utilizadas para apresentar as duas páginas do dashboard.

## 13. Como visualizar o projeto

1. Acesse o repositório no GitHub.
2. Abra uma das imagens para visualizar as páginas do dashboard.
3. Para explorar os filtros, indicadores e interações, baixe o arquivo `.pbix`.
4. Abra o arquivo utilizando o **Microsoft Power BI Desktop**.

## 14. Considerações finais

O desenvolvimento deste projeto envolveu as principais etapas de um processo de Business Intelligence: entendimento do problema, exploração das fontes de dados, tratamento e transformação, modelagem, criação de medidas DAX e desenvolvimento de dashboards.

O resultado é um relatório estruturado para responder às 15 perguntas propostas no desafio, permitindo analisar o faturamento, o atingimento das metas, a participação das categorias e as mudanças de desempenho entre 2017 e 2018.

**Projeto desenvolvido como prática de análise de dados e Business Intelligence utilizando Power BI, Power Query, linguagem M e DAX.**
