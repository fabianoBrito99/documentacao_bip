# 🧠 Bloco 1 – Importações e Configurações Iniciais

Este bloco prepara o ambiente para rodar o restante do código. Ele importa ferramentas (bibliotecas) que vamos usar e faz alguns ajustes na visualização dos dados.

---

### 📦 O que o código faz:

```python
import pandas as pd
import xml.etree.ElementTree as ET
import pandas as pd
import numpy as np
import csv
import matplotlib.pyplot as plt
import os
from os import chdir, getcwd, listdir
import glob
import datetime
import re
```

#### 📚 Explicação das bibliotecas:
| Biblioteca | Para que serve |
|------------|----------------|
| `pandas` | Manipular tabelas de dados (como planilhas Excel) |
| `xml.etree.ElementTree` | Ler e navegar por arquivos XML |
| `numpy` | Cálculos matemáticos com arrays e números |
| `csv` | Trabalhar com arquivos .csv (valores separados por vírgula) |
| `matplotlib.pyplot` | Criar gráficos e visualizações |
| `os` e `glob` | Trabalhar com arquivos e pastas do computador |
| `datetime` | Trabalhar com datas e horas |
| `re` | Trabalhar com expressões regulares (buscas de texto avançadas) |

---

### ⚙️ Configurações do pandas:

```python
pd.set_option('display.max_columns', None)
pd.options.display.float_format = '{:.2f}'.format
pd.set_option('display.max_rows', None)
```

- `max_columns`: mostra todas as colunas de uma tabela, sem esconder nenhuma.
- `float_format`: exibe os números com 2 casas decimais.
- `max_rows`: mostra todas as linhas quando printar o DataFrame (sem cortes).

---

### ✅ Pré-requisitos para rodar este bloco:

Execute este comando no terminal para instalar tudo:

```bash
pip install pandas numpy matplotlib
```

---

📌 Após rodar este bloco, o ambiente estará pronto para começar a processar os arquivos XML da estrutura 3040.



# 📦 Bloco 2 – Função `process_xml`: Lê contratos e vencimentos

Esta função abre um arquivo XML da estrutura 3040 e extrai os contratos e vencimentos registrados.

### ✨ O que ela faz:
- Percorre os nós `<Cli>` (cliente) e `<Op>` (operação de crédito).
- Coleta informações como:
  - Código do cliente
  - Modalidade da operação
  - Valor do contrato
  - Data de vencimento
  - Número do contrato
  - COSIF, Índices, Tipo de garantia etc.
- Também coleta dados das parcelas (`<Venc>`), como:
  - Valor de cada parcela
  - Atraso e data de vencimento

### 🔑 Resultado:
Retorna um DataFrame com **todos os contratos e seus vencimentos** para o cliente.

### ✅ Pré-requisitos:
O arquivo XML deve estar no formato esperado da Estrutura 3040 do Bacen.


---


# 📦 Bloco 3 – Função `process_xml_gar`: Lê garantias

Essa função complementa a leitura do XML extraindo informações de **garantias** dos contratos.

### ✨ O que ela faz:
- Percorre os nós `<Gar>` dentro de `<Cli>`.
- Extrai dados como:
  - Código da garantia
  - Tipo de vínculo
  - Valor da garantia
  - Tipo tratado (C1, C2, C3)
- Cada garantia pode ser associada a um ou mais contratos.

### 🔑 Resultado:
Retorna um DataFrame com as **garantias vinculadas aos contratos**.

### 🧩 Observação:
A junção dos dados de garantia com os contratos é feita em blocos seguintes com base em chaves únicas.


---


# 📁 Bloco 4 – Definição de caminhos das pastas

```python
xml_directory = "xmls/"
output_folder = "resultados/"
```

- `xml_directory`: pasta onde os arquivos XML estão salvos.
- `output_folder`: pasta onde os arquivos `.parquet` processados serão salvos.

🛠️ Se as pastas não existirem, é necessário criá-las antes de rodar o script.


---


# 📄 Bloco 5 – Lista todos os arquivos XML

```python
arquivos_xml = glob.glob(os.path.join(xml_directory, "*.xml"))
```

🔍 O que faz:
- Procura todos os arquivos com extensão `.xml` dentro da pasta `xmls/`.
- Cria uma lista com os caminhos completos desses arquivos para leitura posterior.

✅ Requisito: a pasta `xmls/` deve conter os arquivos XML que seguem o layout 3040.


# 📥 Bloco 6 – Inicializa listas para armazenamento

```python
lista_df_contratos = []
lista_df_gar = []
```

✅ O que faz:
Cria duas listas vazias para guardar:
- `lista_df_contratos`: todos os DataFrames de contratos lidos.
- `lista_df_gar`: todos os DataFrames de garantias lidos.

Essas listas vão ser preenchidas ao processar cada arquivo XML.


---


# 🔁 Bloco 7 – Leitura de todos os XMLs na pasta

Percorre todos os arquivos XML encontrados e executa a leitura com as funções definidas antes:

```python
for arq in arquivos_xml:
    df = process_xml(xml_directory, os.path.basename(arq))
    lista_df_contratos.append(df)
    df_gar = process_xml_gar(xml_directory, os.path.basename(arq))
    lista_df_gar.append(df_gar)
```

✅ Resultado:
- Lê e extrai contratos e garantias de cada arquivo.
- Armazena tudo nas listas `lista_df_contratos` e `lista_df_gar`.


---


# 🧩 Bloco 8 – Unifica garantias de todos os arquivos

```python
df_gar_all = pd.concat(lista_df_gar)
```

📌 O que faz:
- Junta todos os DataFrames de garantias em um único DataFrame chamado `df_gar_all`.


---


# 🧩 Bloco 9 – Unifica contratos de todos os arquivos

```python
df_3040 = pd.concat(lista_df_contratos)
```

📌 O que faz:
- Junta todos os DataFrames de contratos (com vencimentos) em um único DataFrame `df_3040`.


---


# 📅 Bloco 10 – Adiciona coluna com ano e mês

```python
df_3040["AnoMesReferencia"] = pd.to_datetime(df_3040["DtBase"]).dt.strftime("%Y-%m")
```

📌 O que faz:
- Converte a coluna `DtBase` (data base do arquivo XML) em formato `ano-mês` (ex: `2025-04`).
- Cria uma nova coluna chamada `AnoMesReferencia`.

Essa coluna será útil para agrupamentos mensais posteriormente.


# 🔢 Bloco 11 – Converte colunas numéricas

```python
colunas_float = ['PercIndx', 'VarCamb', 'TaxEft', 'TaxNom', 'TaxEfetiva', 'TaxaJuros', 'VlrContr']
for col in colunas_float:
    df_3040[col] = pd.to_numeric(df_3040[col], errors='coerce')
```

📌 O que faz:
- Converte colunas que devem ser numéricas (porcentagens, taxas e valores) para o tipo `float`.
- Usa `errors='coerce'` para transformar valores inválidos em `NaN` (vazios).

🎯 Por quê?
Garante que operações matemáticas posteriores (como somar, calcular média) funcionem corretamente.


---


# 📅 Bloco 12 – Converte colunas de data

```python
colunas_data = ['DtContr', 'DtVencOp', 'DtUltPgto']
for col in colunas_data:
    df_3040[col] = pd.to_datetime(df_3040[col], errors='coerce')
```

📌 O que faz:
- Converte strings de datas para o tipo `datetime` do pandas.
- Isso permite calcular prazos, atrasos, meses etc.

🛠️ Importante para usar funções como:
```python
(df_3040['DtVencOp'] - df_3040['DtContr']).dt.days
```


---


# 🧼 Bloco 13 – Limpeza de tipo e conversões

```python
df_3040['Cd'] = df_3040['Cd'].astype(str)
df_3040['Contrt'] = df_3040['Contrt'].astype(str)
df_3040['Mod'] = df_3040['Mod'].astype(str)
df_3040['CEP'] = df_3040['CEP'].astype(str)
df_3040['VlrContr'] = df_3040['VlrContr'].astype(float)
```

📌 O que faz:
- Garante que colunas como `Cd`, `Contrt`, `Mod`, `CEP` sejam **strings**, mesmo que pareçam números.
- Coluna `VlrContr` é convertida para float para permitir cálculos futuros.

🎯 Por quê?
Evita bugs em agrupamentos e junções onde zeros à esquerda são importantes (como CEPs e códigos).


---


# 🔐 Bloco 14 – Cria chave única do contrato

```python
df_3040["chave_contrato"] = df_3040["Cd"] + df_3040["Contrt"] + df_3040["Mod"] + df_3040["AnoMesReferencia"]
```

📌 O que faz:
- Junta 4 colunas para formar uma chave única para cada contrato no mês:
  - Código do cliente
  - Número do contrato
  - Modalidade
  - Mês de referência

🔑 Isso será usado para agregar e combinar informações de forma única por contrato.


---


# 🧩 Bloco 15 – Expande garantias por contrato

```python
df_gar_all = df_gar_all.groupby("chave_contrato").agg({
    "Tp_gar_trat": lambda x: list(x),
    "Val": lambda x: list(x),
    "Vinc": lambda x: list(x),
    "Cod": lambda x: list(x),
}).reset_index()
```

📌 O que faz:
- Agrupa as garantias pelo contrato (`chave_contrato`).
- Junta os valores em **listas** para cada contrato.

🔁 Por exemplo:
Um contrato com 3 garantias terá uma linha com:
```python
Tp_gar_trat = ["C1", "C3", "outro"]
Val = [10000, 5000, 2000]
```

Isso facilita a análise agregada por contrato.


# 🔗 Bloco 16 – Une garantias aos contratos

```python
df_3040 = df_3040.merge(df_gar_all, on="chave_contrato", how="left")
```

📌 O que faz:
- Junta os dados das garantias (`df_gar_all`) à base de contratos (`df_3040`) usando a chave única `chave_contrato`.

🧩 Cada linha de contrato agora inclui as garantias em forma de lista.


---


# 🚩 Bloco 17 – Cria flags para tipos de garantia

```python
for tipo in ['C1', 'C2', 'C3']:
    df_3040[f'flag_{tipo}'] = df_3040["Tp_gar_trat"].apply(lambda x: 1 if isinstance(x, list) and tipo in x else 0)
```

📌 O que faz:
- Para cada tipo de garantia (C1, C2, C3), cria uma nova coluna (`flag_C1`, `flag_C2`, `flag_C3`).
- Se o contrato tiver esse tipo de garantia na lista `Tp_gar_trat`, marca com 1, senão 0.

🎯 Isso ajuda a classificar e filtrar contratos por tipo de garantia.


---


# 🏷️ Bloco 18 – Define categoria de garantia

```python
def classificar_garantia(row):
    if row['flag_C1'] == 1:
        return 'C1'
    elif row['flag_C2'] == 1:
        return 'C2'
    elif row['flag_C3'] == 1:
        return 'C3'
    else:
        return 'SEM GARANTIA'

df_3040['cat_garantia'] = df_3040.apply(classificar_garantia, axis=1)
```

📌 O que faz:
- Cria uma nova coluna `cat_garantia` com a classificação principal da garantia.
- Prioridade: C1 > C2 > C3 > SEM GARANTIA.

🔍 Essa coluna resume as garantias em uma única categoria por contrato.


---


# 🔁 Bloco 19 – Flag de produto rotativo

```python
lista_rotativos = ['0281', '0284', '0285', '0287']
df_3040["flag_prod_rotativos"] = df_3040["Mod"].isin(lista_rotativos).astype(int)
```

📌 O que faz:
- Cria uma flag binária (1 ou 0) se a modalidade do contrato está na lista de produtos rotativos (como cartão de crédito).

🧠 Produtos rotativos geralmente têm regras de risco diferentes, por isso são identificados separadamente.


---


# ⏱️ Bloco 20 – Flag de atraso > 90 dias

```python
df_3040["flag_atraso_maior90"] = (df_3040["Atraso"] > 90).astype(int)
```

📌 O que faz:
- Cria uma flag indicando se o contrato tem parcelas com mais de 90 dias de atraso.

🛠️ Essa informação é importante para calcular perda esperada (PE), inadimplência e classificação de risco.


# 💰 Bloco 21 – Calcula saldo utilizado

```python
df_3040["saldo_utilizado"] = df_3040["VlrContr"] - df_3040["ValorAmort"]
```

📌 O que faz:
- Cria a coluna `saldo_utilizado` subtraindo o valor amortizado (`ValorAmort`) do valor original do contrato (`VlrContr`).

🎯 Isso mostra quanto do contrato ainda está sendo usado.


---


# ⏳ Bloco 22 – Calcula prazo total do contrato

```python
df_3040["prazo_contrato"] = (df_3040["DtVencOp"] - df_3040["DtContr"]).dt.days
```

📌 O que faz:
- Calcula o número total de dias entre a data de início (`DtContr`) e a data final do contrato (`DtVencOp`).

🧠 Útil para calcular porcentagem de contrato já vencido e monitorar ciclos de crédito.


---


# 🗓️ Bloco 23 – Calcula prazo remanescente

```python
df_3040["prazo_remanescente"] = (df_3040["DtVencOp"] - pd.to_datetime(df_3040["DtBase"])).dt.days
```

📌 O que faz:
- Calcula quantos dias ainda faltam para o contrato vencer a partir da **data base** do arquivo XML (`DtBase`).

🧩 Importante para entender a posição atual do contrato no tempo.


---


# 📈 Bloco 24 – Calcula índice de utilização (IU)

```python
df_3040["IU"] = df_3040["saldo_utilizado"] / df_3040["VlrContr"]
```

📌 O que faz:
- Calcula o quanto do contrato está sendo utilizado atualmente (saldo/valor original).

🔢 Resultado varia de 0 a 1 (ou seja, 0% a 100%).

🧠 Indicador importante em análise de risco e comportamento do cliente.


---


# ⚠️ Bloco 25 – Flag para contratos com valor em prejuízo

```python
df_3040["flag_prejuizo"] = (df_3040["ValorPrej"] > 0).astype(int)
```

📌 O que faz:
- Cria uma coluna binária (`flag_prejuizo`) que vale 1 se o contrato tiver valor em prejuízo (`ValorPrej > 0`), senão 0.

🧩 Útil para análise de contratos já deteriorados.


# ⛔ Bloco 26 – Flag de atraso anormal (> 9999 dias)

```python
df_3040["flag_atraso_maior_9999"] = (df_3040["Atraso"] > 9999).astype(int)
```

📌 O que faz:
- Cria uma flag (`flag_atraso_maior_9999`) que vale 1 quando o número de dias em atraso for maior que 9999.
- Esse valor é irreal e indica erro ou problema de preenchimento.

🧠 Ajuda a detectar registros que devem ser revisados ou desconsiderados.


---


# 🕒 Bloco 27 – Classifica o atraso por faixa

```python
def classificar_atraso(atraso):
    if pd.isna(atraso):
        return "SEM DADO"
    elif atraso <= 0:
        return "SEM ATRASO"
    elif atraso <= 30:
        return "1 a 30"
    elif atraso <= 90:
        return "31 a 90"
    elif atraso <= 9999:
        return "91 a 9999"
    else:
        return "ACIMA DE 9999"

df_3040["faixa_atraso"] = df_3040["Atraso"].apply(classificar_atraso)
```

📌 O que faz:
- Cria a coluna `faixa_atraso` com faixas categóricas para facilitar agrupamentos e visualizações.

🎯 Permite gráficos e análises como “quantos contratos estão em atraso acima de 90 dias”.


---


# 🧼 Bloco 28 – Remove contratos com valor zerado

```python
df_3040 = df_3040[df_3040["VlrContr"] > 0]
```

📌 O que faz:
- Remove da base todos os contratos cujo valor (`VlrContr`) seja igual ou menor que zero.

🛑 Isso evita divisões por zero e garante que só contratos relevantes fiquem na base.


---


# ❗ Bloco 29 – Zera ou trata contratos com valor negativo

```python
df_3040["VlrContr"] = df_3040["VlrContr"].apply(lambda x: x if x > 0 else np.nan)
```

📌 O que faz:
- Se `VlrContr` for negativo, transforma em `NaN` (vazio).

🛠️ Isso permite manter o registro para análise, mas evita que valores incorretos afetem médias e somas.


---


# 🔐 Bloco 30 – Cria coluna com saldo garantido

```python
def calcular_saldo_garantido(row):
    if isinstance(row["Val"], list):
        return sum(row["Val"])
    return 0

df_3040["valor_garantias"] = df_3040.apply(calcular_saldo_garantido, axis=1)
```

📌 O que faz:
- Soma os valores das garantias vinculadas ao contrato (coluna `Val`).
- Se a garantia estiver em lista, calcula a soma total.

💡 Cria a coluna `valor_garantias` que mostra o total garantido por contrato.


# 🧮 Bloco 31 – Índice de cobertura de garantias

```python
df_3040["indice_cobertura"] = df_3040["valor_garantias"] / df_3040["saldo_utilizado"]
```

📌 O que faz:
- Cria a coluna `indice_cobertura`, que mostra quanto das garantias cobre o saldo do contrato.

🧠 Fórmula:
```
indice_cobertura = valor total das garantias / saldo utilizado
```

💡 Se o valor for:
- **>1** → Garantias cobrem mais do que o saldo.
- **<1** → Garantias insuficientes.


---


# ❗ Bloco 32 – Evita erro de divisão por zero

```python
df_3040["indice_cobertura"] = df_3040["indice_cobertura"].replace([np.inf, -np.inf], np.nan)
```

📌 O que faz:
- Substitui valores infinitos (∞) do índice de cobertura por `NaN`.
- Isso ocorre quando `saldo_utilizado = 0` → divisão por zero.

🔒 Garante integridade dos dados para análises posteriores.


---


# 🎯 Bloco 33 – Classifica nível de cobertura

```python
def classificar_cobertura(valor):
    if pd.isna(valor):
        return "SEM DADO"
    elif valor >= 1:
        return "ACIMA DE 100%"
    elif valor >= 0.5:
        return "ENTRE 50% E 100%"
    else:
        return "ABAIXO DE 50%"

df_3040["faixa_cobertura"] = df_3040["indice_cobertura"].apply(classificar_cobertura)
```

📌 O que faz:
- Cria a coluna `faixa_cobertura` com categorias baseadas no índice de cobertura.

🧩 Permite filtrar e agrupar contratos por qualidade da garantia.


---


# 🧼 Bloco 34 – Flag para contratos com valor nulo

```python
df_3040["flag_valor_nulo"] = df_3040["VlrContr"].isna().astype(int)
```

📌 O que faz:
- Cria uma flag (0 ou 1) indicando se o valor do contrato está ausente (`NaN`).

📊 Útil para identificar registros incompletos ou com erros de origem.


---


# ✅ Bloco 35 – Mantém apenas contratos válidos

```python
df_3040 = df_3040[df_3040["flag_valor_nulo"] == 0]
```

📌 O que faz:
- Remove da base todos os contratos com valor nulo (`NaN` em `VlrContr`).

🎯 Isso deixa na base apenas os contratos que possuem valor registrado e podem ser analisados corretamente.


# 🔧 Bloco 36 – Preenche valores vazios de categoria de garantia

```python
df_3040["cat_garantia"] = df_3040["cat_garantia"].fillna("SEM GARANTIA")
```

📌 O que faz:
- Substitui valores vazios (`NaN`) da coluna `cat_garantia` por `"SEM GARANTIA"`.

🔍 Isso facilita filtros e agrupamentos, evitando categorias em branco.


---


# 🛡️ Bloco 37 – Preenche valores vazios de faixa de cobertura

```python
df_3040["faixa_cobertura"] = df_3040["faixa_cobertura"].fillna("SEM DADO")
```

📌 O que faz:
- Garante que todos os contratos tenham alguma classificação na coluna `faixa_cobertura`.

📊 Isso permite criar gráficos e tabelas sem categorias ausentes.


---


# 🆔 Bloco 38 – Cria coluna com identificador de cliente

```python
df_3040["ident_cliente"] = df_3040["Cd"]
```

📌 O que faz:
- Copia a coluna `Cd` (código do cliente) para uma nova coluna `ident_cliente`.

🧠 Pode ser usado como chave de agrupamento por cliente.


---


# 🔄 Bloco 39 – Preenche índice de utilização nulo

```python
df_3040["IU"] = df_3040["IU"].fillna(0)
```

📌 O que faz:
- Preenche valores ausentes na coluna `IU` com 0.

🔒 Isso evita problemas em operações matemáticas e filtros.


---


# 💾 Bloco 40 – Exporta arquivo final

```python
df_3040.to_parquet(output_folder + "contratos_agrupados.parquet", index=False)
```

📌 O que faz:
- Salva o DataFrame `df_3040` com todos os dados tratados e enriquecidos em um arquivo `.parquet`.

📂 Local:
- O arquivo será salvo na pasta `resultados/` com o nome `contratos_agrupados.parquet`.

✅ Formato Parquet:
- Compacto e eficiente para grandes volumes de dados.
- Ideal para leitura rápida em Python, Spark, Power BI etc.


# 🔧 Bloco 36 – Preenche valores vazios de categoria de garantia

```python
df_3040["cat_garantia"] = df_3040["cat_garantia"].fillna("SEM GARANTIA")
```

📌 O que faz:
- Substitui valores vazios (`NaN`) da coluna `cat_garantia` por `"SEM GARANTIA"`.

🔍 Isso facilita filtros e agrupamentos, evitando categorias em branco.


---


# 🛡️ Bloco 37 – Preenche valores vazios de faixa de cobertura

```python
df_3040["faixa_cobertura"] = df_3040["faixa_cobertura"].fillna("SEM DADO")
```

📌 O que faz:
- Garante que todos os contratos tenham alguma classificação na coluna `faixa_cobertura`.

📊 Isso permite criar gráficos e tabelas sem categorias ausentes.


---


# 🆔 Bloco 38 – Cria coluna com identificador de cliente

```python
df_3040["ident_cliente"] = df_3040["Cd"]
```

📌 O que faz:
- Copia a coluna `Cd` (código do cliente) para uma nova coluna `ident_cliente`.

🧠 Pode ser usado como chave de agrupamento por cliente.


---


# 🔄 Bloco 39 – Preenche índice de utilização nulo

```python
df_3040["IU"] = df_3040["IU"].fillna(0)
```

📌 O que faz:
- Preenche valores ausentes na coluna `IU` com 0.

🔒 Isso evita problemas em operações matemáticas e filtros.


---


# 💾 Bloco 40 – Exporta arquivo final

```python
df_3040.to_parquet(output_folder + "contratos_agrupados.parquet", index=False)
```

📌 O que faz:
- Salva o DataFrame `df_3040` com todos os dados tratados e enriquecidos em um arquivo `.parquet`.

📂 Local:
- O arquivo será salvo na pasta `resultados/` com o nome `contratos_agrupados.parquet`.

✅ Formato Parquet:
- Compacto e eficiente para grandes volumes de dados.
- Ideal para leitura rápida em Python, Spark, Power BI etc.
