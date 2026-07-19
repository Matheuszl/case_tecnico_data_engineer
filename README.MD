# Case Técnico — Engenheiro(a) de Dados Pleno
## Grupo Panvel

Este repositório contém a solução desenvolvida para o Case Técnico de Engenharia de Dados do Grupo Panvel.

O objetivo foi projetar uma camada analítica preparada para consumo por Inteligência Artificial, permitindo que gestores consultem indicadores de negócio em linguagem natural através do **Databricks Genie**.

---

# Objetivos do Projeto

- Estruturar os dados para análises de negócio.
- Construir uma camada semântica preparada para IA Generativa.
- Definir regras de negócio para respostas consistentes.
- Garantir qualidade e confiabilidade dos dados.
- Demonstrar uma arquitetura alinhada às tecnologias utilizadas pela Panvel (Databricks + Delta Lake).

---

# Arquitetura da Solução

```text
Arquivos Brutos
        │
        ▼
 Bronze (Delta Tables)
        │
        ▼
Silver (Modelo Analítico)
 ├── silver_vendas
 ├── silver_itens_venda
 ├── silver_produtos
 └── bronze_filiais
        │
        ▼
Databricks Genie
        │
        ▼
Consultas em Linguagem Natural
```

---

# Estrutura do Repositório

```
/
├── 00_Utils/
│   ├── utils
│   ├── query_create_volume_bronze
│   ├── Descicao Tabelas Genie
│       ├── query_instrucoes_tabela_filiais
│       ├── query_instrucoes_tabela_produtos
│       ├── query_instrucoes_tabela_vendas
│       ├── query_instrucoes_tabela_vendas_itens
│        
├── 01_etl/
│   ├── etl_bronze.ipynb
│   ├── etl_prata.ipynb
│
├── 02_eda/
│   ├── eda.ipynb
│   ├── obs.md
│
├── 03_genie/
│   ├── genie.md
│   ├── genie_instructions.md
│   ├── genie_querys.ipynb
│
└── README.md
```

---

# Principais Entregas

- Ingestão dos arquivos fornecidos.
- Análise exploratória dos dados.
- Identificação de inconsistências.
- Modelagem analítica em Delta Tables.
- Criação de camada semântica para IA.
- Documentação das tabelas utilizando `COMMENT`.
- Configuração do Databricks Genie.
- Definição de regras de negócio para respostas consistentes.

---

# Modelagem

A solução foi normalizada em duas tabelas fato:

### silver_vendas

Uma linha por venda.

Responsável pelas análises de:

- Receita
- Ticket médio
- Clientes fidelidade
- Forma de pagamento
- Crescimento
- Evolução

### silver_itens_venda

Uma linha por item vendido.

Responsável pelas análises de:

- Produtos
- Categorias
- Quantidade vendida
- Descontos
- Marca própria

As dimensões de **Produtos** e **Filiais** complementam as análises através de relacionamentos semânticos.

---

# Tecnologias Utilizadas

- Databricks
- Apache Spark
- Delta Lake
- Python
- SQL
- Databricks Genie

---

# Premissas Adotadas

Durante a análise foram identificadas algumas características do conjunto de dados que influenciaram a modelagem.

Entre elas:

- Produtos com nomes duplicados e SKUs distintos.
- Categoria `id_categoria = 8` inexistente na dimensão de categorias.
- Diferença entre o preço de tabela atual e o preço praticado nas vendas.
- Necessidade de documentar regras de negócio para evitar respostas incorretas da IA.

Todas essas decisões encontram-se documentadas nos arquivos da pasta `docs`.

---

# Demonstração

Durante a apresentação serão demonstrados:

- Processo de ingestão dos dados.
- Análise exploratória.
- Modelagem analítica.
- Configuração do Databricks Genie.
- Exemplos de perguntas em linguagem natural.
- Validação das respostas obtidas.

---

# Autor

**Matheus Zalamena**

Software Engineer | Data Engineering | Databricks | Spark | SQL | Python