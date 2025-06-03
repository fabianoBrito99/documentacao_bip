
# 📦 Bloco 1

```bash
# Bloco 1 - Código
```

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
pd.set_option('display.max_columns', None)

pd.options.display.float_format = '{:.2f}'.format
pd.set_option('display.max_rows', None)
```

## 📘 O que este bloco faz:
Este bloco importa bibliotecas do Python que são essenciais para o funcionamento do código:

- `pandas`: para ler, organizar e analisar tabelas (planilhas).
- `xml.etree.ElementTree`: para ler arquivos no formato XML (um tipo de arquivo de texto estruturado).
- `numpy`: para fazer cálculos matemáticos de forma mais eficiente.
- `os`, `glob`: para navegar pelas pastas e encontrar arquivos automaticamente.
- `matplotlib.pyplot`: para criar gráficos.

Essas bibliotecas funcionam como ferramentas que ajudam o código a fazer tarefas específicas com poucos comandos.


---


# 📦 Bloco 2

```bash
# Bloco 2 - Código
```

```python
def process_xml(xml_path, xml_file_name):
    xmlparse = ET.parse(xml_path)
    root = xmlparse.getroot()
    cols = ["File_Name", "Cd", 'Tp', "ClassCli", "IniRelactCli", 'CongEcon',
            "Mod", 'Cosif', 'CaracEspecial', 'DiaAtraso', 'Contrt', "ClassOp", 'ProvConsttd', 'DtVencOp', 'DtContr', 'VlrContr', 
            "Venc"]
    rows = []
    for cd in root.findall("./Cli"):
        x = cd.get("Cd")
        xa = cd.get("Tp")
        cl = cd.get("ClassCli")
        ini = cd.get("IniRelactCli")
        cg = cd.get("CongEcon")
        for mod in cd.findall("./Op"):
            y = mod.get('Mod') #ok
            bb = mod.get('Cosif')
            b = mod.get('CaracEspecial') #ok
            i = mod.get('DiaAtraso')
            v = mod.get('VlrContr')
            c = mod.get('Contrt') #ok
            op = mod.get('ClassOp') #ok
            prov = mod.get('ProvConsttd') #ok
            dv = mod.get('DtVencOp')
            dc = mod.get('DtContr')
            for venc in mod.findall("./Venc"):
                z = venc.attrib

                rows.append({'File_Name': xml_file_name, 'Cd': x, 'IniRelactCli': ini, 'ClassCli': cl, 'CongEcon': cg, 'Tp': xa, "Mod": y, 'Cosif': bb, 'CaracEspecial': b,
                             'DiaAtraso': i, 'VlrContr': v, 'Contrt': c, 'ClassOp': op, 'ProvConsttd': prov, 'DtVencOp': dv, 'DtContr': dc,
                             'Venc': z})

    df = pd.DataFrame(rows, columns=cols)

    objs = [df, pd.DataFrame(df['Venc'].tolist())]
    df_lv2 = pd.concat(objs, axis=1).drop('Venc', axis=1)

    return df_lv2
```

## 📘 O que este bloco faz:
Aqui estamos criando uma **função chamada `process_xml`** que serve para:

- **Ler um arquivo XML** com dados de clientes e contratos.
- **Extrair os dados importantes** desses contratos (ex: valor, data, atraso).
- **Transformar os dados** em uma **tabela (`DataFrame`)**, que é um formato fácil de analisar.

Isso é útil porque os arquivos XML são difíceis de ler manualmente, e esta função transforma esses dados em algo parecido com uma planilha do Excel.


---


# 📦 Bloco 3

```bash
# Bloco 3 - Código
```

```python
def process_xml_gar(xml_path, xml_file_name):
    xmlparse = ET.parse(xml_path)
    root = xmlparse.getroot()
    cols = ["File_Name","Cd",'Tp',"Mod",'IPOC','Contrt',"Gar"] 
    rows = [] 
    for cd in root.findall("./Cli"):
        x = cd.get("Cd")
        xa = cd.get("Tp")
        for mod in cd.findall("./Op"):
            y=mod.get('Mod')
            bb =mod.get('IPOC')
            c=mod.get('Contrt')
            for venc in mod.findall("./Gar"):
                z= venc.attrib

                rows.append({'File_Name': xml_file_name,'Cd':x,'Tp':xa,"Mod": y,'IPOC':bb,'Contrt':c,'Gar':z})

    df_gar = pd.DataFrame(rows, columns=cols)
    df_cod_gar = df_gar

    return df_cod_gar
```

## 📘 O que este bloco faz:
Aqui estamos criando uma **função chamada `process_xml`** que serve para:

- **Ler um arquivo XML** com dados de clientes e contratos.
- **Extrair os dados importantes** desses contratos (ex: valor, data, atraso).
- **Transformar os dados** em uma **tabela (`DataFrame`)**, que é um formato fácil de analisar.

Isso é útil porque os arquivos XML são difíceis de ler manualmente, e esta função transforma esses dados em algo parecido com uma planilha do Excel.


---


# 📦 Bloco 4

```bash
# Bloco 4 - Código
```

```python
# Directory containing XML files
xml_directory = r"C:\Users\fabiano.souza\Documents\codigosBIP\base352\3040\0001"

# List all XML files in the directory
xml_files = [os.path.join(xml_directory, file) for file in os.listdir(xml_directory) if file.endswith(".xml")]

# Process each XML file and concatenate the results
all_dfs = []
for xml_file in xml_files:
    xml_file_name = os.path.basename(xml_file)  # Extract the file name without the path
    df_result = process_xml(xml_file, xml_file_name)
    all_dfs.append(df_result)

# Concatenate all dataframes into a single dataframe
df_lv2 = pd.concat(all_dfs, ignore_index=True)

# Display the resulting dataframe
print('Final DataFrame Shape:', df_lv2.shape)
```

## 📘 O que este bloco faz:
Este bloco procura todos os **arquivos XML** dentro de uma **pasta específica no computador**.
Esses arquivos são os dados brutos que o programa precisa ler.

Depois, ele passa cada um para a função que transforma XML em tabela.

Resultado: uma grande tabela com todos os dados combinados, pronta para análises.


---


# 📦 Bloco 5

```bash
# Bloco 5 - Código
```

```python
df_lv2.head()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 6

```bash
# Bloco 6 - Código
```

```python
# Directory containing XML files
xml_directory = r'C:\Users\fabiano.souza\Documents\codigosBIP\base352\3040\0001'

# List all XML files in the directory
xml_files = [os.path.join(xml_directory, file) for file in os.listdir(xml_directory) if file.endswith(".xml")]

# Process each XML file and concatenate the results
all_dfs = []
for xml_file in xml_files:
    xml_file_name = os.path.basename(xml_file)  # Extract the file name without the path
    df_result = process_xml_gar(xml_file, xml_file_name)
    all_dfs.append(df_result)

# Concatenate all dataframes into a single dataframe
df_cod_gar = pd.concat(all_dfs, ignore_index=True)

# Display the resulting dataframe
print('Final DataFrame Shape:', df_cod_gar.shape)
```

## 📘 O que este bloco faz:
Este bloco procura todos os **arquivos XML** dentro de uma **pasta específica no computador**.
Esses arquivos são os dados brutos que o programa precisa ler.

Depois, ele passa cada um para a função que transforma XML em tabela.

Resultado: uma grande tabela com todos os dados combinados, pronta para análises.


---


# 📦 Bloco 7

```bash
# Bloco 7 - Código
```

```python
# Ajustando a coluna de Tp
objs = [df_cod_gar, pd.DataFrame(df_cod_gar['Gar'].tolist())]
df_gar2  = pd.concat(objs, axis=1).drop('Gar', axis=1)
df_apoio_gar = df_gar2.copy()
# Create a list of column names
columns = list(df_apoio_gar.columns)
# # Find the index of the second occurrence of 'Tp'
tp_indices = [i for i, col in enumerate(columns) if col == 'Tp']
second_tp_index = tp_indices[1]  # Get the second occurrence
# Rename the second 'Tp' to a new name, e.g., 'Tp2'
columns[second_tp_index] = 'Tp_gar_trat'
# # Assign the modified list back to the DataFrame
df_apoio_gar.columns = columns
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 8

```bash
# Bloco 8 - Código
```

```python
df_3040 = pd.merge(df_lv2,df_apoio_gar,on=['Cd','Tp','Mod','Contrt','File_Name'], how='left').fillna(0)
colunas_venc = ['v40', 'v110', 'v120', 'v130', 'v20', 'v140', 'v150', 'v160', 'v165', 'v170', 'v175', 'v180', 'v205', 'v220', 'v230', 'v240', 'v245', 'v250', 'v255', 'v260', 'v270', 'v280', 'v330', 'v320', 'v210', 'v199', 'v310', 'v190', 'v290', 'v60', 'v80']

for coluna in colunas_venc:
    if coluna not in df_3040.columns:
        df_3040[coluna] = 0  # Adiciona a coluna ausente e preenche com zeros
    df_3040[coluna] = df_3040[coluna].astype(float)
    
df_3040.head()
```

## 📘 O que este bloco faz:
Neste bloco, o código **combina duas tabelas diferentes**:
- A primeira contém os dados dos contratos.
- A segunda contém as garantias desses contratos.

A junção é feita usando informações em comum (como código do cliente, número do contrato, etc.).
O objetivo é ter tudo em uma única tabela para facilitar os cálculos e análises futuras.


---


# 📦 Bloco 9

```bash
# Bloco 9 - Código
```

```python

```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 10

```bash
# Bloco 10 - Código
```

```python
df_3040.query('DiaAtraso == "2460310"')
```

## 📘 O que este bloco faz:
Este bloco **filtra a tabela** para mostrar somente os registros que atendem a uma condição específica.
Exemplo: contratos com atraso acima de um valor ou de um cliente específico.

É útil para análises mais pontuais ou para entender melhor determinados casos.


---


# 📦 Bloco 11

```bash
# Bloco 11 - Código
```

```python
df_3040.describe()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 12

```bash
# Bloco 12 - Código
```

```python
df_3040.info()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 13

```bash
# Bloco 13 - Código
```

```python
# df_3040['CaracEspecial'] = df_3040['CaracEspecial'].astype('float64')
df_3040['Tp_gar_trat'] = df_3040['Tp_gar_trat'].astype('float64')
df_3040['Ident'] = df_3040['Ident'].astype('float64')
df_3040['PercGar'] = df_3040['PercGar'].astype('float64')
df_3040['VlrOrig'] = df_3040['VlrOrig'].astype('float64')
df_3040['VlrContr'] = df_3040['VlrContr'].astype('float64')
df_3040['CongEcon'] = df_3040['CongEcon'].astype('float64')
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 14

```bash
# Bloco 14 - Código
```

```python
# Criando coluna de referencia
df_3040['AnoMesReferencia'] = df_3040['File_Name'].str.extract(r'_(\d{6})\D')
df_3040['AnoMesReferencia'] = pd.to_datetime(df_3040['AnoMesReferencia'], format='%m%Y')
df_3040['AnoMesReferencia'] = df_3040['AnoMesReferencia'].dt.strftime('%Y-%m-%d')
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 15

```bash
# Bloco 15 - Código
```

```python
df_3040.query('Contrt == "487100CHESPJ0000259292213"')
```

## 📘 O que este bloco faz:
Este bloco **filtra a tabela** para mostrar somente os registros que atendem a uma condição específica.
Exemplo: contratos com atraso acima de um valor ou de um cliente específico.

É útil para análises mais pontuais ou para entender melhor determinados casos.


---


# 📦 Bloco 17

```bash
# Bloco 17 - Código
```

```python
df_test = df_3040
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 18

```bash
# Bloco 18 - Código
```

```python
# converte a coluna de 'DtBase' para o tipo datetime
df_test['AnoMesReferencia'] = pd.to_datetime(df_test['AnoMesReferencia']) 

# converte a coluna de 'Tp' para o tipo inteiro
df_test['Tp'] = df_test['Tp'].astype('int64')

# converte a coluna de 'DiaAtraso' para o tipo inteiro
df_test['DiaAtraso'] = df_test['DiaAtraso'].replace("nan", None)
df_test['DiaAtraso'] = df_test['DiaAtraso'].fillna(0)
df_test['DiaAtraso'] = df_test['DiaAtraso'].str.replace("'", "")
df_test['DiaAtraso'] = df_test['DiaAtraso'].fillna(0)
df_test['DiaAtraso'] = df_test['DiaAtraso'].astype('float64')
df_test['DiaAtraso'] = df_test['DiaAtraso'].astype('int64')

# converte a coluna de 'IniRelactCli' para o tipo datetime
df_test['IniRelactCli'] = pd.to_datetime(df_test['IniRelactCli'])

# converte a coluna de 'Mod' para o tipo inteiro
df_test['Mod'] = df_test['Mod'].astype('str')
# df_test['Mod'] = df_test['Mod'].apply(lambda x: x.strip('0') if isinstance(x, str) else x)

# converte a coluna de 'DtContr' para o tipo datetime
df_test['DtContr'] = pd.to_datetime(df_test['DtContr']) 

# converte a coluna de 'VlrContr' para o tipo float
df_test['VlrContr'] = df_test['VlrContr'].astype('float64')

# converte a coluna de 'DtVencOp' para o tipo datetime
df_test['DtVencOp'] = pd.to_datetime(df_test['DtVencOp'])

# converte as colunas 'vCOD' para o tipo float
v_cols = [c for c in df_test.columns if 
          (c.startswith('v1') or c.startswith('v2') or c.startswith('v3') 
           or c.startswith('v4') or c.startswith('v5') or c.startswith('v6') 
           or c.startswith('v7') or c.startswith('v8') or c.startswith('v9')
           or c.startswith('v0'))]

df_test[v_cols] = df_test[v_cols].astype('float64')
df_test[v_cols] = df_test[v_cols].fillna(0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 19

```bash
# Bloco 19 - Código
```

```python
df_test['chave'] = df_test['Cd'].astype(str) + "_" + df_test['Contrt'].astype(str) + "_" + df_test['AnoMesReferencia'].astype(str) + "_" + df_test['Mod'].astype(str) + "_" + df_test['Tp_gar_trat'].astype(str)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 20

```bash
# Bloco 20 - Código
```

```python
df_test.chave.nunique() == df_test.shape[0]
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 21

```bash
# Bloco 21 - Código
```

```python
duplicados = df_test[df_test.duplicated('chave', keep=False)]['chave'].unique()

if len(duplicados) > 0:
    print("Valores duplicados na coluna 'key':", duplicados)
else:
    print("Não há valores duplicados na coluna 'key'.")
```

## 📘 O que este bloco faz:
Esse comando apenas **informa que o código terminou de executar com sucesso**.
É útil para confirmar que tudo ocorreu como esperado.


---


# 📦 Bloco 22

```bash
# Bloco 22 - Código
```

```python
df_test.query('chave == "05597773_0003000247_2019-01-01_0215_901.0"')
```

## 📘 O que este bloco faz:
Este bloco **filtra a tabela** para mostrar somente os registros que atendem a uma condição específica.
Exemplo: contratos com atraso acima de um valor ou de um cliente específico.

É útil para análises mais pontuais ou para entender melhor determinados casos.


---


# 📦 Bloco 23

```bash
# Bloco 23 - Código
```

```python
df_test['Tp_gar_trat'] = df_test['Tp_gar_trat'].astype(str)
#df_3040['tp_gar_trat'] = df_3040['tp_gar_trat'].astype(float)
df_test['Cat']  = 'outro'

####C1#######

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['426', '427', '671', '674', '1201']))), 'Cat'] = 'C1'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0426', '0427', '0671', '0674']))), 'Cat'] = 'C1'

####C2#######

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['210', '21', '323', '324', '325', '423', '424', '499', '563', '902'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0210', '0323', '0324', '0325', '0423', '0424', '0499', '0563', '0902'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['881','882','883','884','885','886','887','888','889','89', '890','899'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0881','0882','0883','0884','0885','0886','0887','0888','0889', '0890','0899'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'
####C3#######

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['101', '102', '103', '104', '105', '106', '107', '108', '199', '201', '202', '203', '204', '205', '206', '207', '208', '209', '299'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0101', '0102', '0103', '0104', '0105', '0106', '0107', '0108', '0199', '0201', '0202', '0203', '0204', '0205', '0206', '0207', '0208', '0209', '0299'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['322', '35', '350', '399', '423', '424', '428', '499', '901', '902', '903', '904', '562', '563', '564', '565'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0322', '0350', '0399', '0423', '0424', '0428', '0499', '0901', '0902', '0903', '0904', '0562', '0563', '0564', '0565'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 24

```bash
# Bloco 24 - Código
```

```python
df_test.Cat.value_counts()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 25

```bash
# Bloco 25 - Código
```

```python
# Ordena o DataFrame original em ordem crescente por 'Coluna1' e decrescente por 'Coluna2'
df_test.sort_values(by=['Cat'], ascending=[True], inplace=True)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 26

```bash
# Bloco 26 - Código
```

```python
df_test.tail()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 27

```bash
# Bloco 27 - Código
```

```python
'C1' < 'OUTRO'
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 28

```bash
# Bloco 28 - Código
```

```python
aggregations = {
    'Tp': 'max',
    'IniRelactCli': 'max',
    'CaracEspecial': 'max',
    'DiaAtraso': 'max',
    'ClassOp': 'max',
    'ProvConsttd': 'max',
    'CongEcon': 'max',
    'DtContr': 'max',
    'VlrContr': 'sum',
    'DtVencOp': 'max',
    'DtContr': 'max',
    'Tp_gar_trat': 'first',
    'Ident': 'max',
    'PercGar': 'max',
    'VlrOrig': 'sum',
    'v20': 'max',
    'v40': 'max',
    'v60': 'max',
    'v80': 'max',
    'v110': 'max',
    'v120': 'max',
    'v130': 'max',
    'v140': 'max',
    'v150': 'max',
    'v160': 'max',
    'v165': 'max',
    'v170': 'max',
    'v175': 'max',
    'v180': 'max',
    'v190': 'max',
    'v199': 'max',
    'v205': 'max',
    'v210': 'max',
    'v220': 'max',
    'v230': 'max',
    'v240': 'max',
    'v245': 'max',
    'v250': 'max',
    'v255': 'max',
    'v260': 'max',
    'v270': 'max',
    'v280': 'max',
    'v290': 'max',
    'v310': 'max',
    'v320': 'max',
    'v330': 'max'
}
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 29

```bash
# Bloco 29 - Código
```

```python
df_test = df_test.groupby(['Cd', 'Contrt', 'AnoMesReferencia', 'Mod']).agg(aggregations).reset_index()
```

## 📘 O que este bloco faz:
Aqui os dados são **agrupados por cliente ou por contrato**, e para cada grupo o código faz cálculos como:
- Soma de valores de contratos
- Máximo de atraso
- Quantidade de garantias, entre outros.

É como fazer um **resumo por cliente**, muito útil para relatórios ou dashboards.


---


# 📦 Bloco 30

```bash
# Bloco 30 - Código
```

```python
df_test['chave'] = df_test['Cd'].astype(str) + "_" + df_test['Contrt'].astype(str) + "_" + df_test['AnoMesReferencia'].astype(str) + "_" + df_test['Mod'].astype(str)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 31

```bash
# Bloco 31 - Código
```

```python
duplicados = df_test[df_test.duplicated('chave', keep=False)]['chave'].unique()

if len(duplicados) > 0:
    print("Valores duplicados na coluna 'key':", duplicados)
else:
    print("Não há valores duplicados na coluna 'key'.")
```

## 📘 O que este bloco faz:
Esse comando apenas **informa que o código terminou de executar com sucesso**.
É útil para confirmar que tudo ocorreu como esperado.


---


# 📦 Bloco 32

```bash
# Bloco 32 - Código
```

```python
df_test.describe()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 33

```bash
# Bloco 33 - Código
```

```python
v_cols = [c for c in df_test.columns if 
          (c.startswith('v1') or c.startswith('v2') or c.startswith('v3') 
           or c.startswith('v4') or c.startswith('v5') or c.startswith('v6') 
           or c.startswith('v7') or c.startswith('v8') or c.startswith('v9')
           or c.startswith('v0'))]

df_test[v_cols] = df_test[v_cols].fillna(0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 34

```bash
# Bloco 34 - Código
```

```python
view_list = [
    df_test
]
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 36

```bash
# Bloco 36 - Código
```

```python
# Saldo Total: Trata-se do somatório de v20, v40, v60, v80, v1XX e v2XX.
for view in view_list:
    view['saldo_total'] = (
        (view['v20'] + 
        view['v40'] +
        view['v60'] + 
        view['v80'] +
        view['v110'] +
        view['v120'] +
        view['v130'] +
        view['v140'] +
        view['v150'] +
        view['v160'] +
        view['v165'] +
        view['v170'] +
        view['v175'] +
        view['v180'] +
        view['v190'] +
        view['v199'] +
        view['v205'] +
        view['v210'] +
        view['v220'] +
        view['v230'] +
        view['v240'] +
        view['v245'] +
        view['v250'] +
        view['v255'] +
        view['v260'] +
        view['v270'] +
        view['v280'] +
        view['v290']).fillna(0)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 38

```bash
# Bloco 38 - Código
```

```python
# Saldo Utilizado: Trata-se do somatório de v1XX e v2XX
for view in view_list:
    view['saldo_utilizado'] = (
       (view['v110'] +
        view['v120'] +
        view['v130'] +
        view['v140'] +
        view['v150'] +
        view['v160'] +
        view['v165'] +
        view['v170'] +
        view['v175'] +
        view['v180'] +
        view['v190'] +
        view['v199'] +
        view['v205'] +
        view['v210'] +
        view['v220'] +
        view['v230'] +
        view['v240'] +
        view['v245'] +
        view['v250'] +
        view['v255'] +
        view['v260'] +
        view['v270'] +
        view['v280'] +
        view['v290']).fillna(0)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 40

```bash
# Bloco 40 - Código
```

```python
# Limite: Trata-se do somatório de v20, v40, v60 e v80
for view in view_list:
    view['limite'] = (
       (view['v20'] +
        view['v40'] +
        view['v60'] +
        view['v80']).fillna(0)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 42

```bash
# Bloco 42 - Código
```

```python
# Índice de Utilização (IU): Trata-se da razão entre o saldo utilizado e a saldo total
for view in view_list:
    view['IU'] = (
        (view['saldo_utilizado'] / view['saldo_total']).fillna(0).replace(np.inf, 1)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 43

```bash
# Bloco 43 - Código
```

```python
df_test.head()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 45

```bash
# Bloco 45 - Código
```

```python
# Saldo Utilizado por Limite: Trata-se da razão entre o saldo utilizado e o limite
for view in view_list:
    view['saldo_utilizado/limite'] = (
        (view['saldo_utilizado'] / view['limite']).fillna(0).replace(np.inf, 99)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 47

```bash
# Bloco 47 - Código
```

```python
# Saldo de Atraso Total: Trata-se do somatório de v2XX
for view in view_list:
    view['saldo_atraso_total'] = (
        (view['v205'] +
        view['v210'] +
        view['v220'] +
        view['v230'] +
        view['v240'] +
        view['v245'] +
        view['v250'] +
        view['v255'] +
        view['v260'] +
        view['v270'] +
        view['v280'] +
        view['v290']).fillna(0)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 49

```bash
# Bloco 49 - Código
```

```python
# Saldo de Atraso Total por Saldo Total: Trata-se da razão entre o saldo de atraso total e a saldo total
for view in view_list:
    view['saldo_atraso_total/saldo_total'] = (
        (view['saldo_atraso_total'] / view['saldo_total']).fillna(0).replace(np.inf, 99)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 51

```bash
# Bloco 51 - Código
```

```python
# Atraso > 30: Trata-se do somatório de v220, v230, v240, v245, v250, v255, v260, v270, v280 e v290
for view in view_list:
    view['DiaAtraso'] = view['DiaAtraso'].fillna(0)
    if (view['DiaAtraso'] >= 0).any():
        view['saldo_maior_30'] = view.apply(
            lambda row: row['v220'] + 
                        row['v230'] +
                        row['v240'] +
                        row['v245'] + 
                        row['v250'] + 
                        row['v255'] + 
                        row['v260'] + 
                        row['v270'] +
                        row['v280'] + 
                        row['v290'] if row['DiaAtraso'] >= 0 else 0, axis=1
        )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 53

```bash
# Bloco 53 - Código
```

```python
# Atraso > 90: Trata-se do somatório de v240, v245, v250, v255, v260, v270, v280 e v290
for view in view_list:
    view['DiaAtraso'] = view['DiaAtraso'].fillna(0)
    if (view['DiaAtraso'] >= 0).any():
        view['saldo_maior_90'] = view.apply(
            lambda row: row['v240'] +
                        row['v245'] + 
                        row['v250'] + 
                        row['v255'] + 
                        row['v260'] + 
                        row['v270'] +
                        row['v280'] + 
                        row['v290'] if row['DiaAtraso'] >= 0 else 0, axis=1
        )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 55

```bash
# Bloco 55 - Código
```

```python
# Atraso > 180: Trata-se do somatório de v255, v260, v270, v280 e v290
for view in view_list:
    view['DiaAtraso'] = view['DiaAtraso'].fillna(0)
    if (view['DiaAtraso'] >= 0).any():
        view['saldo_maior_180'] = view.apply(
            lambda row: row['v255'] + 
                        row['v260'] + 
                        row['v270'] +
                        row['v280'] + 
                        row['v290'] if row['DiaAtraso'] >= 0 else 0, axis=1
        )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 57

```bash
# Bloco 57 - Código
```

```python
# Flag de Atraso: Trata-se de uma variável booleana que descreve a existência ou não de atraso, sendo 1 para True e 0 para False (É importante atentar-se que um atraso igual ou inferior a 5 não é considerado atraso)
for view in view_list:
    view['flag_atraso'] = (
        (view['DiaAtraso'] > 5).astype(int)
    )
```

## 📘 O que este bloco faz:
Este bloco importa bibliotecas do Python que são essenciais para o funcionamento do código:

- `pandas`: para ler, organizar e analisar tabelas (planilhas).
- `xml.etree.ElementTree`: para ler arquivos no formato XML (um tipo de arquivo de texto estruturado).
- `numpy`: para fazer cálculos matemáticos de forma mais eficiente.
- `os`, `glob`: para navegar pelas pastas e encontrar arquivos automaticamente.
- `matplotlib.pyplot`: para criar gráficos.

Essas bibliotecas funcionam como ferramentas que ajudam o código a fazer tarefas específicas com poucos comandos.


---


# 📦 Bloco 59

```bash
# Bloco 59 - Código
```

```python
# Flag de Prejuízo: Trata-se de uma variável booleana que descreve a existência ou não de prejuízo, sendo 1 para True e 0 para False.
for view in view_list:
    view['flag_prejuizo'] = (
        ((view['v310'] != 0) |
        (view['v320'] != 0) |
        (view['v330'] != 0)).astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 61

```bash
# Bloco 61 - Código
```

```python
# Saldo de Prejuízo: Trata-se do somatório de v310, v320 e v330    
for view in view_list:
    view['saldo_prejuizo'] = (
        (view['v310'] +
        view['v320'] +
        view['v330']).fillna(0)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 63

```bash
# Bloco 63 - Código
```

```python
# Tempo de Relacionamento (anos): Trata-se do tempo, em anos, de relacionamento daquele determinado registro
for view in view_list:
    view['cliente_tempo_relac_anos'] = (
        (view['AnoMesReferencia'] - view['IniRelactCli']).dt.days / 365
    ).where((view['AnoMesReferencia'] - view['IniRelactCli']).dt.days / 365 >= 0, 0)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 65

```bash
# Bloco 65 - Código
```

```python
# Tempo de Relacionamento (meses): Trata-se do tempo, em meses, de relacionamento daquele determinado registro
for view in view_list:
    view['cliente_tempo_relac_meses'] = (
        (view['AnoMesReferencia'] - view['IniRelactCli']).dt.days / 30
    ).where((view['AnoMesReferencia'] - view['IniRelactCli']).dt.days / 30 >= 0, 0)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 67

```bash
# Bloco 67 - Código
```

```python
# lista com os códigos de mod's dos produtos rotativos
mods_rotativos = ([
    '0204',
    '0210',
    '0213',
    '0214',
    '0218',
    '0303',
    '0406',
    '1304',
    '1505',
    '1901'
])

# Adicionar valores de mod iniciados por 19 à lista mods_rotativos
mods_rotativos += [mod for mod in range(1900, 2000) if re.match('^19', str(mod))]

# DtVencOp_v2: Trata-se da data de prazo de contrato para o caso da modalidade "parcelados", para o caso "rotativo" a data não existe 
for view in view_list:
    view['DtVencOp_v2'] = view.apply(lambda row: row['DtVencOp'] if row['Mod'] not in mods_rotativos else 0, axis=1)

view['DtVencOp_v2'] = pd.to_datetime(view['DtVencOp_v2'])

# Lista de dias a serem subtraídos para cada linha
dias_subtrair = view['DtVencOp_v2'].dt.day

# Loop para subtrair os dias individualmente em cada linha
for index, dias in enumerate(dias_subtrair):
    view.loc[index, 'DtVencOp_v2'] -= pd.Timedelta(days=dias-1)

view['DtVencOp_v2'] = view['DtVencOp_v2'].replace(pd.to_datetime('1970-01-01'), pd.NaT)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 69

```bash
# Bloco 69 - Código
```

```python
# Prazo de Contrato em Dias: Trata-se da quantidade de dias do prazo de contrato daquele determinado registro
view['DtContr'] = pd.to_datetime(view['DtContr'])

# Lista de dias a serem subtraídos para cada linha
dias_subtrair = view['DtContr'].dt.day

# Loop para subtrair os dias individualmente em cada linha
for index, dias in enumerate(dias_subtrair):
    view.loc[index, 'DtContr'] -= pd.Timedelta(days=dias-1)

view['DtContr'] = view['DtContr'].replace(pd.to_datetime('1970-01-01'), pd.NaT)

for view in view_list:
    view['prazo_contrato'] = (view['DtVencOp'] - view['DtContr']).dt.days
    view['prazo_contrato'] = view['prazo_contrato'].apply(lambda x: 0 if x < 0 or x is None else x)
    view['prazo_contrato'] = view['prazo_contrato'].fillna(0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 71

```bash
# Bloco 71 - Código
```

```python
# Prazo Remanescente em Dias: Trata-se da quantidade de dias remanescente do prazo de contrato daquele determinado registro
for view in view_list:
    view['prazo_remanescente'] = (view['DtVencOp'] - view['AnoMesReferencia']).dt.days
    view['prazo_remanescente'] = view['prazo_remanescente'].apply(lambda x: 0 if x < 0 or x is None else x)
    view['prazo_remanescente'] = view['prazo_remanescente'].fillna(0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 73

```bash
# Bloco 73 - Código
```

```python
# Percentual de Prazo Remanescente: Trata-se do percentual de prazo remanescente daquele determinado registro
for view in view_list:
    view['percentual_prazo_remanescente'] = (
        (view['prazo_remanescente'] / view['prazo_contrato']) * 100
    )
    view['percentual_prazo_remanescente'] = view['percentual_prazo_remanescente'].fillna(0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 75

```bash
# Bloco 75 - Código
```

```python
# Tempo Conta Corrente em Dias: Trata-se da quantidade de dias em que esse determinado cliente possui sua conta corrente aberta
for view in view_list:
    view['tempo_conta_corrente_dias'] = (
        (view['AnoMesReferencia'] - view['IniRelactCli']).dt.days
    ).fillna(0).where((view['AnoMesReferencia'] - view['IniRelactCli']).dt.days >= 0, 0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 77

```bash
# Bloco 77 - Código
```

```python
# # Saldo em Cheque Especial: Trata-se do saldo em cheque especial daquele determinado cliente
for view in view_list:
    view['saldo_cheque_especial'] = np.where(view['Mod'] == '0213', view['saldo_total'], 0)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 79

```bash
# Bloco 79 - Código
```

```python
# Flag de Adiantamento a Depositante: Trata-se de uma variável booleana que descreve a existência ou não de adiantamento a depositante, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_adiantamento_depositante'] = (
        (view['Mod'] == "0101").astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 81

```bash
# Bloco 81 - Código
```

```python
# Flag de Atraso: Trata-se de uma variável booleana que descreve a existência ou não de vencidos de 5 a 361 dias, sendo 1 para True e 0 para False
# É importante atentar-se que um atraso igual ou inferior a 5 não é considerado atraso
for view in view_list:
    view['flag_atraso_1_361'] = (
        (view['DiaAtraso'] > 5) & (view['DiaAtraso'] < 361)
    ).astype(int)
```

## 📘 O que este bloco faz:
Este bloco importa bibliotecas do Python que são essenciais para o funcionamento do código:

- `pandas`: para ler, organizar e analisar tabelas (planilhas).
- `xml.etree.ElementTree`: para ler arquivos no formato XML (um tipo de arquivo de texto estruturado).
- `numpy`: para fazer cálculos matemáticos de forma mais eficiente.
- `os`, `glob`: para navegar pelas pastas e encontrar arquivos automaticamente.
- `matplotlib.pyplot`: para criar gráficos.

Essas bibliotecas funcionam como ferramentas que ajudam o código a fazer tarefas específicas com poucos comandos.


---


# 📦 Bloco 83

```bash
# Bloco 83 - Código
```

```python
# Flag de Saldo em Cheque Especial: Trata-se de uma variável booleana que descreve a existência ou não de saldo em cheque especial, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_cheque_especial'] = (
        (view['Mod'] == "0213").astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 85

```bash
# Bloco 85 - Código
```

```python
# Percentual de Contrato Pago: Trata-se do percentual de contrato pago daquele determinado registro
for view in view_list:
    denominador_zero = view['VlrContr'] == 0
    percentual_contrato_pago = 1 - (view['saldo_total'] / view['VlrContr'])
    view['percentual_contrato_pago'] = np.where(
        np.logical_and(~denominador_zero, percentual_contrato_pago >= 0),
        percentual_contrato_pago,
        0
    )
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 87

```bash
# Bloco 87 - Código
```

```python
# Flag de Atraso maior do que 30: Trata-se de uma variável booleana que descreve a existência ou não de atraso maior do que 30 dias para o determinado registro, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_atraso_maior30'] = (
        (view['DiaAtraso'] > 30).astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 89

```bash
# Bloco 89 - Código
```

```python
# Flag de Atraso maior do que 90: Trata-se de uma variável booleana que descreve a existência ou não de atraso maior do que 90 dias para o determinado registro, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_atraso_maior90'] = (
        (view['DiaAtraso'] > 90).astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 91

```bash
# Bloco 91 - Código
```

```python
# Flag de Atraso maior do que 180: Trata-se de uma variável booleana que descreve a existência ou não de atraso maior do que 180 dias para o determinado registro, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_atraso_maior180'] = (
        (view['DiaAtraso'] > 180).astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 93

```bash
# Bloco 93 - Código
```

```python
mods_rotativos = ([
    '0204',
    '0210',
    '0213',
    '0214',
    '0218',
    '0303',
    '0406',
    '1304',
    '1505',
])

# Adicionar valores de mod iniciados por 19 à lista mods_rotativos
mods_rotativos += [mod for mod in range(1900, 2000) if re.match('^19', str(mod))]

# Flag de Produtos Rotativos: Trata-se de uma variável booleana que descreve se aquele determinado registro é ou não um produto rotativo, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_prod_rotativos'] = (
        view['Mod'].isin(mods_rotativos).astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 94

```bash
# Bloco 94 - Código
```

```python
# Flag de Garantia: Trata-se de uma variável booleana que descreve a existência ou não de garantia, sendo 1 para True e 0 para False
for view in view_list:
    view['flag_garantia'] = (
        (view['Tp_gar_trat'].notnull() & 
        (view['Tp_gar_trat'] != 0)).astype(int)
    )
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 95

```bash
# Bloco 95 - Código
```

```python
df_test.info()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 96

```bash
# Bloco 96 - Código
```

```python
df_test.IU.value_counts()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 97

```bash
# Bloco 97 - Código
```

```python

```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 98

```bash
# Bloco 98 - Código
```

```python
# pasta em que a base processada será alocada
output_folder = r'C:\Users\GalinoBeatriz(BipGro\OneDrive - BUSINESS INTEGRATION PARTNERS SPA\General - CREDISIS - PROJETO R4966\PE352 & PD Concessao\03. Bases\01. Book de variáveis\3040\0001'

# exportação da base processadaem parquet
df_test.to_parquet(f'{output_folder}\\novo_novo_01-base_contrato_variaveis.parquet', index=False)
```

## 📘 O que este bloco faz:
Esse comando **salva a tabela final** que o código montou em um **arquivo de saída**, nos formatos `.parquet` ou `.csv`.
- `.csv` pode ser aberto em Excel.
- `.parquet` é usado por sistemas mais avançados como Power BI e Python.

Assim você pode usar os dados tratados fora do Python.


---


# 📦 Bloco 99

```bash
# Bloco 99 - Código
```

```python
df_test.sample(10)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 100

```bash
# Bloco 100 - Código
```

```python
df_test.describe()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 101

```bash
# Bloco 101 - Código
```

```python
df_test.query('cliente_tempo_relac_anos < 0')
```

## 📘 O que este bloco faz:
Este bloco **filtra a tabela** para mostrar somente os registros que atendem a uma condição específica.
Exemplo: contratos com atraso acima de um valor ou de um cliente específico.

É útil para análises mais pontuais ou para entender melhor determinados casos.


---


# 📦 Bloco 103

```bash
# Bloco 103 - Código
```

```python
df_test['Tp_gar_trat'] = df_test['Tp_gar_trat'].astype(str)
#df_3040['tp_gar_trat'] = df_3040['tp_gar_trat'].astype(float)
df_test['Cat']  = 'outro'

####C1#######

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['426', '427', '671', '674', '1201']))), 'Cat'] = 'C1'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0426', '0427', '0671', '0674']))), 'Cat'] = 'C1'

####C2#######

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['210', '21', '323', '324', '325', '423', '424', '499', '563', '902'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0210', '0323', '0324', '0325', '0423', '0424', '0499', '0563', '0902'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['881','882','883','884','885','886','887','888','889','89', '890','899'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0881','0882','0883','0884','0885','0886','0887','0888','0889', '0890','0899'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C2'
####C3#######

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['101', '102', '103', '104', '105', '106', '107', '108', '199', '201', '202', '203', '204', '205', '206', '207', '208', '209', '299'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0101', '0102', '0103', '0104', '0105', '0106', '0107', '0108', '0199', '0201', '0202', '0203', '0204', '0205', '0206', '0207', '0208', '0209', '0299'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'

df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['322', '35', '350', '399', '423', '424', '428', '499', '901', '902', '903', '904', '562', '563', '564', '565'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'
df_test.loc[(df_test['Tp_gar_trat'].apply(lambda x: any(item in x for item in ['0322', '0350', '0399', '0423', '0424', '0428', '0499', '0901', '0902', '0903', '0904', '0562', '0563', '0564', '0565'])) & (df_3040['Cat'] == 'outro')), 'Cat'] = 'C3'
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 104

```bash
# Bloco 104 - Código
```

```python
# Ordena o DataFrame original em ordem crescente 
df_test.sort_values(by=['Cat'], ascending=[True], inplace=True)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 105

```bash
# Bloco 105 - Código
```

```python
# dicionário com as variáveis que serão agregadas e seus repectivos métodos estatísticos
aggregations = {
    'IniRelactCli': 'max',
    'CaracEspecial': 'max',
    'DiaAtraso': 'max',
    'ClassOp': 'max',
    'ProvConsttd': 'max',
    'CongEcon': 'max',
    'DtContr': 'max',
    'VlrContr': 'sum',
    'DtVencOp': 'max',
    'DtContr': 'max',
    'Tp_gar_trat': 'first',
    'Ident': 'max',
    'PercGar': 'max',
    'VlrOrig': 'sum',
    'v20': 'max',
    'v40': 'max',
    'v60': 'max',
    'v80': 'max',
    'v110': 'max',
    'v120': 'max',
    'v130': 'max',
    'v140': 'max',
    'v150': 'max',
    'v160': 'max',
    'v165': 'max',
    'v170': 'max',
    'v175': 'max',
    'v180': 'max',
    'v190': 'max',
    'v199': 'max',
    'v205': 'max',
    'v210': 'max',
    'v220': 'max',
    'v230': 'max',
    'v240': 'max',
    'v245': 'max',
    'v250': 'max',
    'v255': 'max',
    'v260': 'max',
    'v270': 'max',
    'v280': 'max',
    'v290': 'max',
    'v310': 'max',
    'v320': 'max',
    'v330': 'max',
    'flag_garantia': 'max',   
    'saldo_total': 'sum',
    'saldo_utilizado': 'sum',
    'limite': 'sum',
    'IU': 'first',
    # 'saldo_utilizado/limite': 'first',
    'saldo_atraso_total': 'sum',
#    'saldo_atraso_total/saldo_total': 'first',
    'saldo_maior_30': 'sum',
    'saldo_maior_90': 'sum',
    'saldo_maior_180': 'sum',
    'flag_atraso': 'max',
    'flag_prejuizo': 'max',
    'saldo_prejuizo': 'sum',
    'cliente_tempo_relac_anos': 'max',
    'cliente_tempo_relac_meses': 'max',
    'prazo_contrato': 'max', 
    'prazo_remanescente': 'max', 
    # 'percentual_prazo_remanescente': 'max', 
    'tempo_conta_corrente_dias': 'max', 
    'flag_atraso_1_361': 'max', 
    # 'percentual_contrato_pago': 'max',
    'flag_atraso_maior30': 'max', 
    'flag_atraso_maior90': 'max', 
    'flag_atraso_maior180': 'max'
}
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 106

```bash
# Bloco 106 - Código
```

```python
# Consolidação da visão (df) de clientes
df_cliente = df_test.groupby(['Cd', 'AnoMesReferencia']).agg(aggregations).reset_index()
```

## 📘 O que este bloco faz:
Aqui os dados são **agrupados por cliente ou por contrato**, e para cada grupo o código faz cálculos como:
- Soma de valores de contratos
- Máximo de atraso
- Quantidade de garantias, entre outros.

É como fazer um **resumo por cliente**, muito útil para relatórios ou dashboards.


---


# 📦 Bloco 107

```bash
# Bloco 107 - Código
```

```python
df_cliente.head()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 109

```bash
# Bloco 109 - Código
```

```python
# Saldo Utilizado por Limite: Trata-se da razão entre o saldo utilizado e o limite
df_cliente['saldo_utilizado/limite'] = (
    (df_cliente['saldo_utilizado'] / df_cliente['limite']).fillna(0).replace(np.inf, 99)
)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 110

```bash
# Bloco 110 - Código
```

```python
# Saldo de Atraso Total por Saldo Total: Trata-se da razão entre o saldo de atraso total e a saldo total
df_cliente['saldo_atraso_total/saldo_total'] = (
    (df_cliente['saldo_atraso_total'] / df_cliente['saldo_total']).fillna(0).replace(np.inf, 99)
)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 111

```bash
# Bloco 111 - Código
```

```python
denominador_zero = df_cliente['VlrContr'] == 0
percentual_contrato_pago = 1 - (df_cliente['saldo_total'] / df_cliente['VlrContr'])
df_cliente['percentual_contrato_pago'] = np.where(
    np.logical_and(~denominador_zero, percentual_contrato_pago >= 0),
    percentual_contrato_pago,
    0
)
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 112

```bash
# Bloco 112 - Código
```

```python
df_cliente['percentual_prazo_remanescente'] = (
    (df_cliente['prazo_remanescente'] / df_cliente['prazo_contrato']) * 100
)
df_cliente['percentual_prazo_remanescente'] = df_cliente['percentual_prazo_remanescente'].fillna(0)
```

## 📘 O que este bloco faz:
Este trecho **limpa e padroniza os dados** da tabela:
- Converte textos em números ou datas, para que possam ser somados ou comparados corretamente.
- Substitui espaços vazios (valores ausentes) por zeros ou valores padrão.

Esses passos são muito importantes para evitar erros nos cálculos posteriores.


---


# 📦 Bloco 113

```bash
# Bloco 113 - Código
```

```python
df_cliente.columns
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 114

```bash
# Bloco 114 - Código
```

```python
# pasta em que a base processada será alocada
output_folder = r'C:\Users\GalinoBeatriz(BipGro\OneDrive - BUSINESS INTEGRATION PARTNERS SPA\General - CREDISIS - PROJETO R4966\PE352 & PD Concessao\03. Bases\01. Book de variáveis\3040\0001'

# exportação da base processadaem parquet
df_cliente.to_parquet(f'{output_folder}\\novo_novo_01-base_cliente_variaveis.parquet', index=False)
```

## 📘 O que este bloco faz:
Esse comando **salva a tabela final** que o código montou em um **arquivo de saída**, nos formatos `.parquet` ou `.csv`.
- `.csv` pode ser aberto em Excel.
- `.parquet` é usado por sistemas mais avançados como Power BI e Python.

Assim você pode usar os dados tratados fora do Python.


---


# 📦 Bloco 115

```bash
# Bloco 115 - Código
```

```python
df_cliente.IU.value_counts()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 116

```bash
# Bloco 116 - Código
```

```python
df_cliente.info()
```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 117

```bash
# Bloco 117 - Código
```

```python

```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.


---


# 📦 Bloco 118

```bash
# Bloco 118 - Código
```

```python

```

## 📘 O que este bloco faz:
Esse trecho realiza alguma transformação ou cálculo intermediário, como criação de colunas com indicadores (ex: saldo, tempo de contrato, flag de atraso etc).
Esses passos ajudam a montar os dados que serão usados no relatório final.
