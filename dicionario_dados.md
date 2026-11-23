# Dicionário de dados — Guias de ITBI pagas (Prefeitura de São Paulo)

Fonte: planilhas em `dados/brutos/` (2024, 2025 e jan–set 2026), uma aba por mês.
As definições vêm da aba `EXPLICAÇÕES` das próprias planilhas.

Origem do dado:
- **DTI**: declarado pelo contribuinte na Declaração de Transações Imobiliárias
- **IPTU**: puxado do cadastro do IPTU (exercício atual)
- **Sistema**: calculado pela Prefeitura

## Colunas

| # | Coluna | Significado | Origem | Uso no projeto |
|---|--------|-------------|--------|----------------|
| 0 | N° do Cadastro (SQL) | Identificador do imóvel no cadastro do IPTU (setor-quadra-lote + dígito) | DTI | **Chave do imóvel** |
| 1 | Nome do Logradouro | Rua, avenida etc. | IPTU | **Localização** |
| 2 | Número | Número do imóvel na rua | IPTU | **Localização** |
| 3 | Complemento | Apto, bloco, torre | IPTU | Não |
| 4 | Bairro | Bairro do endereço | IPTU | **Localização** (muitos vazios) |
| 5 | Referência | Referência do endereço (nome do condomínio etc.) | IPTU | Não |
| 6 | CEP | CEP do logradouro | IPTU | **Localização** |
| 7 | Natureza de Transação | Tipo de negócio: compra e venda, doação, cessão de direitos etc. | DTI | **Filtro** |
| 8 | Valor de Transação (declarado pelo contribuinte) | Quanto o comprador pagou | DTI | **Preço** |
| 9 | Data de Transação | Data da escritura ou do contrato | DTI | **Tempo** |
| 10 | Valor Venal de Referência | Valor que a Prefeitura atribui ao imóvel | Sistema | **Pergunta 6** |
| 11 | Proporção Transmitida (%) | Quanto do imóvel foi vendido | DTI | **Filtro (só 100%)** |
| 12 | Valor Venal de Referência (proporcional) | Valor Venal × Proporção Transmitida | Sistema | Não |
| 13 | Base de Cálculo adotada | Maior valor entre Valor de Transação e Valor Venal proporcional | Sistema | Não |
| 14 | Tipo de Financiamento | Modalidade de financiamento (SFH, SFI, Minha Casa Minha Vida…) | DTI | **Pergunta 5** |
| 15 | Valor Financiado | Valor emprestado pelo banco | DTI | **Pergunta 5** |
| 16 | Cartório de Registro | Cartório onde o imóvel está registrado | DTI | Não |
| 17 | Matrícula do Imóvel | Número da matrícula no cartório | DTI | Não |
| 18 | Situação do SQL | Se o cadastro está ativo, e se é predial ou territorial | IPTU | Talvez (filtro) |
| 19 | Área do Terreno (m2) | Área do terreno inteiro (em condomínio, é a do prédio todo) | IPTU | Não |
| 20 | Testada (m) | Largura da frente do terreno | IPTU | Não |
| 21 | Fração Ideal | Fatia do terreno que cabe à unidade (1,0 se não for condomínio) | IPTU | Não |
| 22 | Área Construída (m2) | Área construída, incluindo a parte das áreas comuns | IPTU | **Preço do m²** |
| 23 | Uso (IPTU) | Código do uso: residencial, comercial etc. | IPTU | **Pergunta 3** |
| 24 | Descrição do uso (IPTU) | Texto do código de uso (aba `Tabela de USOS`) | IPTU | **Pergunta 3** |
| 25 | Padrão (IPTU) | Código do tipo e padrão da construção (A, B, C…) | IPTU | **Pergunta 3** |
| 26 | Descrição do padrão (IPTU) | Texto do código de padrão (aba `Tabela de PADRÕES`) | IPTU | **Pergunta 3** |
| 27 | ACC (IPTU) | Ano de conclusão da construção, corrigido por reformas | IPTU | **Idade do imóvel** |

## Cuidados para a limpeza

- **Abas sem cabeçalho:** em `JAN-2024` e `OUT-2024`, a primeira linha já é um imóvel. Essas abas precisam ser lidas com `header=None` e receber os nomes padrão.
- **`OUT-2024`** tem 27 colunas, não 28.
- **Nomes diferentes:**
  - `FEV-2026` escreve "Descrição do **pardão** (IPTU)" e tem uma coluna extra vazia (`Unnamed: 28`).
  - Em `FEV-2024`, `MAI-2024` e `SET-2024`, a coluna de descrição do padrão veio com o nome "ACC (IPTU)", duplicado.
- **Tipos misturados:** valor, data, área do terreno, padrão e ACC vieram como texto em alguns meses. É preciso converter.
- **CEP** veio como número e perde o zero da frente.
- **Número `99999`** parece indicar imóvel sem número.
- **Proporção Transmitida:** conferir a escala antes de filtrar. Há valores como `0.25` e `100`.
- **Área Construída = 0:** acontece em terrenos. Esses imóveis precisam sair do cálculo do preço do m².
