# Análise de Dados do Programa Bolsa Família

Este repositório contém o pipeline de extração, tratamento e análise de dados dos beneficiários do Programa Bolsa Família. O projeto foi desenvolvido com o objetivo de centralizar volumes expressivos de dados públicos, aplicar regras de negócio para saneamento da base e gerar insights estatísticos sobre a distribuição dos recursos federais.

## Contexto do Projeto

O Bolsa Família é o principal programa de transferência de renda do Governo Federal brasileiro. Devido à escala nacional do programa, a manipulação de suas bases de dados exige estratégias eficientes de engenharia e análise de dados para garantir a integridade das informações e viabilizar consultas de performance aceitável.

Este projeto aborda os seguintes tópicos técnicos:
* **Ingestão de Dados Volumosos:** Download e leitura eficiente de bases de dados públicas.
* **Saneamento e Qualidade de Dados:** Tratamento de valores ausentes, inconsistências geográficas e padronização de tipos primitivos.
* **Análise Exploratória de Dados (EDA):** Identificação de padrões socioeconômicos e modelagem da distribuição regional dos benefícios.

---

## Estrutura do Repositório e Tecnologias

A solução foi desenvolvida em **Python**, utilizando bibliotecas consolidadas do ecossistema de Ciência de Dados:

* **Pandas / Polars:** Para manipulação, filtragem e agregação de dataframes de grande porte.
* **NumPy:** Para operações matemáticas e vetoriais de alta performance.
* **Matplotlib / Seaborn:** Para a construção de gráficos e visualizações estatísticas.
* **Jupyter Notebook:** Como ambiente de desenvolvimento para a análise exploratória.

## Modelagem de Dados e Arquitetura

O modelo analítico foi estruturado seguindo as melhores práticas de modelagem dimensional (Star Schema), conforme ilustrado na arquitetura relacional do projeto (referência: `image_6eb55a.png`).

### Tabelas do Modelo

* **`fato_bolsa_familia`**: Concentra os dados transacionais e métricas de repasses históricos. Contém chaves e atributos como `ANO`, `MÊS`, `VALOR PARCELA`, além de dados cadastrais anonimizados/identificadores do favorecido (`CPF FAVORECIDO`, `NIS FAVORECIDO`, `NOME FAVORECIDO`).
* **`dim_regioes`**: Tabela dimensional contendo os atributos geográficos `UF` e `Regiao`, utilizada para realizar o particionamento e a granularidade espacial das análises.
* **`dim_usuarios_rls`**: Tabela de governança responsável pelo mapeamento de segurança, relacionando o `email_usuario` à sua respectiva `regiao_permitida`.

### Relacionamentos e Governança (RLS)

* **`dim_regioes` $\rightarrow$ `fato_bolsa_familia`**: Relacionamento de **1 para Muitos (1:*)** baseado no campo `UF`. A tabela de dimensões filtra a tabela de fatos para consolidação dos indicadores regionais.
* **`dim_usuarios_rls` $\rightarrow$ `dim_regioes`**: Relacionamento de **1 para Muitos (1:*)** configurado com **filtro bidirecional**. Esta estrutura implementa a segurança baseada em Row-Level Security (RLS). Ao validar o e-mail do usuário conectado, o modelo restringe dinamicamente as regiões visíveis na tabela `dim_regioes`, propagando o filtro para a tabela `fato_bolsa_familia` e garantindo que o usuário acesse estritamente os dados autorizados para a sua respectiva região.

<img width="645" height="617" alt="image" src="https://github.com/user-attachments/assets/2f582cab-e4f2-41a5-8716-c57807336974" />

## Instruções para Execução

### Pré-requisitos

* Python 3.8 ou superior instalado.

### 1. Clonagem do Repositório

```bash
git clone [https://github.com/lannacsoares/bolsa_familia.git](https://github.com/lannacsoares/bolsa_familia.git)
cd bolsa_familia

Resultados Obtidos
Distribuição Regional: Mapeamento dos estados e municípios com maior densidade de beneficiários, permitindo correlações socioeconômicas locais.

Métricas de Valor: Cálculo estatístico do ticket médio repassado por unidade familiar, considerando as faixas de transição e bônus do programa.

Otimização de Memória: Otimização do pipeline para processamento de arquivos extensos (escala de Gigabytes) com uso controlado de memória RAM através de leituras em blocos (chunking) ou bibliotecas de alta performance.
