# Análise Exploratória dos Dados

## Filiais

- Não foram identificados registros duplicados, valores nulos ou inconsistências de preenchimento.
- Cada filial possui um **id_filial** e **nome_filial** exclusivos.
- Os campos **cidade** e **UF** estão consistentes com a localização das filiais.
- Foram identificados apenas **3 tipos de loja**: Rua, Shopping e Drive-thru.
- Todas as filiais possuem status **"Ativa"**, indicando que o conjunto de dados contempla apenas lojas em operação.
- O estado com maior número de filiais é o **Rio Grande do Sul**, seguido por **Santa Catarina**, **Paraná** e **São Paulo**.
- As cidades com maior concentração de filiais são **Porto Alegre**, **São Paulo** e **Curitiba**.
- As datas de abertura variam entre **2008 e 2025**, indicando que o conjunto de dados representa um recorte até 2025. Os anos com maior expansão da rede foram **2009, 2013, 2020 e 2024**.

---

## Produtos

- Não foram encontrados **id_produto** ou **SKU** duplicados.
- Foram identificados produtos com **mesmo nome comercial**, porém com **id_produto**, **SKU** e **preço de tabela** diferentes.

**Exemplos:**

- Creme Anti-idade 30g - Neo Química
- Mamadeira 300ml - Legrand

Esse comportamento pode representar cenários reais como:

- Atualização de cadastro;
- Mudança de fornecedor;
- Nova formulação;
- Alteração de embalagem;
- Migração de ERP;
- Lotes promocionais.

### Impacto para IA

Consultas realizadas apenas pelo **nome do produto** podem gerar respostas ambíguas.

**Exemplo:**

> Qual o preço do produto *Creme Anti-idade 30g - Neo Química*?

Existem múltiplos registros válidos para esse nome. Sem uma regra de negócio, um modelo de IA pode retornar qualquer um dos valores ou até calcular uma média indevida.

**Premissa adotada para o Genie:** caso existam múltiplos produtos com o mesmo nome, o assistente deverá solicitar ao usuário que especifique o SKU ou outro identificador antes de responder.

### Categorias

Foi identificado um problema de integridade referencial.

A dimensão **categorias** possui apenas **7 categorias**, porém existem **25 produtos** classificados com **id_categoria = 8**, inexistente na tabela de categorias.

Essa inconsistência deve ser considerada durante a modelagem e documentada como limitação do conjunto de dados.

### Preço de tabela

Todos os produtos possuem preço informado.

- **Valor mínimo:** R$ 2,03
- **Valor máximo:** R$ 124,11

Outro ponto relevante é que o **preço_tabela_atual** representa o preço vigente no momento da carga dos dados e **não necessariamente** o preço praticado nas vendas.

---

## Vendas

A tabela de vendas possui granularidade de **item vendido**, portanto um mesmo **id_venda** aparece em múltiplos registros.

Foi validado que cada venda possui apenas:

- Um status;
- Uma forma de pagamento;
- Uma filial.

Com base nessa estrutura, foram criadas duas tabelas analíticas:

### silver_vendas

Tabela contendo **uma linha por venda**, destinada a análises de:

- Receita;
- Faturamento;
- Ticket médio;
- Clientes fidelidade;
- Formas de pagamento.

### silver_itens_venda

Tabela contendo **uma linha por item vendido**, destinada a análises de:

- Produtos;
- Categorias;
- Quantidade vendida;
- Descontos;
- Participação de produtos nas vendas.

Também foi identificado que o **valor_unitario** registrado na venda não corresponde necessariamente ao **preco_tabela_atual** da dimensão de produtos, indicando que esta representa apenas o preço vigente no momento da carga e não o histórico de preços praticados.

---

# Cobertura dos Requisitos do Case

| Requisito | Solução |
|-----------|---------|
| Qual foi a receita líquida da Regional Serra em julho? | ✅ Atendida pela tabela `silver_vendas`. |
| Como está o ticket médio da Loja X comparado ao trimestre anterior? | ✅ Utiliza `silver_vendas` e comparações temporais. |
| Qual a venda de produtos da categoria própria em relação à rede? | ✅ Utiliza `silver_itens_venda` + `silver_produtos`, considerando `tipo_categoria = 'Própria'`. |
| Quais filiais tiveram queda de receita no último mês? | ✅ Utiliza `silver_vendas` comparando períodos consecutivos. |
| Qual foi o crescimento da Loja X? | ✅ Comparação entre o mês atual e o mês imediatamente anterior. |
| Qual foi a evolução da receita da Regional Y? | ✅ Comparação entre o período atual e o mesmo período do ano anterior. |

---

# Principais Premissas Adotadas

- O **preço de tabela atual** é apenas uma referência comercial e não deve ser utilizado para cálculos de receita.
- Consultas sobre produtos devem utilizar preferencialmente o **id_produto** ou **SKU**, evitando ambiguidades causadas por nomes duplicados.
- Produtos classificados com **id_categoria = 8** foram mantidos na modelagem e registrados como inconsistência do conjunto de dados.
- A modelagem foi normalizada em tabelas de **vendas** e **itens da venda**, reduzindo redundâncias e facilitando análises pelo Genie.
- As métricas e descrições semânticas foram documentadas por meio de **comentários nas tabelas e colunas**, permitindo que o Databricks Genie interprete corretamente os conceitos de negócio.