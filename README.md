# README.md
MVP ENGENHARIA DE DADOS PUC RIO

Documentação do MVP - Pipeline de Dados na Nuvem


## 1. Contexto de Negócios e Perguntas (Etapa 2 e 4.1)
Problema de Negócio: Analisar o desempenho de vendas de um e-commerce para entender padrões de consumo, faturamento por categoria de produto e ticket médio geográfico.

Perguntas de Negócio:

Qual é a categoria de produto que gera o maior faturamento total?

Qual é o ticket médio (valor médio gasto por pedido) por estado dos clientes?

Existe alguma categoria responsável por mais de 30% da receita total do e-commerce?

Estrutura dos Dados Brutos: Arquivo CSV (vendas_brutas.csv) contendo as colunas id_venda, data_venda, id_cliente, estado, categoria, preco e quantidade.

Licença dos Dados: Uso acadêmico público / Domínio Público (CC0).

## 2. Carga dos Dados (Etapa 4.2)
A carga dos dados foi realizada diretamente na plataforma Databricks Free Edition. O arquivo bruto em formato CSV foi carregado em um Volume gerenciado no Unity Catalog no caminho /Volumes/workspace/default/dados_brutos/vendas_brutas.csv.
<img width="1620" height="421" alt="image" src="https://github.com/user-attachments/assets/10f9070d-950c-4bdf-bba8-6a94fb4bb235" />


## 3. Modelagem e Catálogo de Dados (Etapa 4.3)

A modelagem segue o padrão da **Arquitetura Medalhão** (Bronze, Silver e Gold).

### Catálogo de Dados — Tabela Silver (`workspace.default.silver_vendas`)
* **Contexto:** Tabela com os dados limpos e tipados do e-commerce.
* **Linhagem:** Origem a partir da leitura da camada `bronze_vendas`.

| Campo | Tipo | Domínio / Valores Esperados | Descrição |
| :--- | :--- | :--- | :--- |
| `id_venda` | `INT` | Numérico Inteiro > 0 | Chave primária do pedido |
| `data_venda` | `DATE` | Formato YYYY-MM-DD | Data em que a venda foi realizada |
| `id_cliente` | `STRING` | Texto alfanumérico (ex: C01) | Identificador do cliente |
| `estado` | `STRING` | Sigla de estado com 2 letras | Estado de destino do pedido |
| `categoria` | `STRING` | Categorias cadastradas no sistema | Categoria do produto vendido |
| `preco` | `DECIMAL(10,2)` | Valores decimais > 0 | Valor unitário do produto |
| `quantidade` | `INT` | Inteiro > 0 | Unidades compradas |

## 4. Pipeline de Dados (Etapa 4.4)
O pipeline de dados ETL foi estruturado em um Notebook PySpark/SQL utilizando a Arquitetura Medalhão:

Bronze: Ingestão dos dados em estado bruto do Volume para uma tabela Delta (bronze_vendas).

Silver: Limpeza de nulos, deduplicação por id_venda e conversão dos tipos de dados para DATE, DECIMAL e INT (silver_vendas).

Gold: Agregação dos dados para geração de tabelas prontas para consumo analítico (gold_vendas_categoria e gold_ticket_medio_estado).

<img width="1911" height="904" alt="image" src="https://github.com/user-attachments/assets/0a9bf45b-9f08-494b-892e-f3d64f8aac53" />
<img width="1902" height="907" alt="image" src="https://github.com/user-attachments/assets/89dc58d9-0f6c-47c2-82d7-55a74be0fe98" />
<img width="1907" height="514" alt="image" src="https://github.com/user-attachments/assets/3968b452-9971-41d6-8548-060785e258e6" />


## 5. Qualidade de Dados (Etapa 4.5)
Durante o processo de validação da camada Bronze para Silver, foram checados os pilares de qualidade:

Completude: Remoção de registros com id_venda nulo.

Unicidade: Aplicação de .dropDuplicates(["id_venda"]) para garantir que nenhuma transação fosse contada duas vezes.

Consistência: Padronização das datas do formato texto para o formato nativo DATE (YYYY-MM-DD) e o preço para formato financeiro DECIMAL.

## 6. Análise de Dados e Resposta às Perguntas (Etapa 4.5)
Com base nas consultas SQL executadas na camada Gold:

Pergunta 1 (Maior faturamento por categoria): A categoria com maior receita acumulada foi Eletrônicos, impulsionada pelo alto valor unitário dos itens.

Pergunta 2 (Ticket médio por estado): Os estados de SP e RJ apresentaram os maiores tickets médios por transação.

Pergunta 3 (Concentração de receita): A categoria Eletrônicos responde por mais de 50% do faturamento total do conjunto de dados, demonstrando alta dependência do e-commerce nesta categoria.

<img width="1907" height="590" alt="image" src="https://github.com/user-attachments/assets/c9127091-6165-45b6-a1e6-39a47c58e767" />
<img width="1906" height="588" alt="image" src="https://github.com/user-attachments/assets/b1be1af5-3779-4301-8179-f3c6e0b370a5" />
<img width="1912" height="770" alt="image" src="https://github.com/user-attachments/assets/ba52b974-21c2-4b4a-aa8a-b3fed5a9f932" />



## 7. Autoavaliação
O objetivo geral do MVP foi atingido com sucesso. Foi possível construir uma linha de montagem funcional de dados na nuvem utilizando o Databricks Free Edition, cobrindo desde a ingestão do arquivo bruto até a entrega de respostas para perguntas de negócio na camada Gold. A principal dificuldade encontrada foi a navegação inicial na interface do Databricks e o entendimento da gestão do servidor Serverless, superados ao longo da execução. Como trabalho futuro, pretende-se automatizar a ingestão via API em tempo real e criar um dashboard visual no Databricks SQL Dashboards.
