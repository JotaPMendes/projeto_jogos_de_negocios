import streamlit as st
import pandas as pd
import numpy as np
import random

# Função para calcular métricas financeiras
def calcular_custos_e_faturamento(df, sazonalidade, evento_impacto):
    df["salario_vendedores"] = df["vendedores"] * 1500
    df["hora_extra"] = np.where(df["quantidade"] < 0, df["quantidade"] * 10, 0)
    df["estocagem"] = np.where(df["quantidade"] > 0, df["quantidade"] * 4, 0)
    df["cmv"] = df["quantidade"] * 0.20  # Custo da mercadoria vendida
    df["salarios_gerais"] = df["quantidade"] * 0.08  # Salários gerais
    df["aluguel"] = 10  # Aluguel fixo
    df["total_despesas"] = df["propaganda"] + df["salario_vendedores"] + df["hora_extra"] + df["estocagem"] + df["cmv"] + df["salarios_gerais"] + df["aluguel"]
    df["faturamento"] = df['quantidade'] * evento_impacto * sazonalidade * (1 + df['propaganda'] / 100 + df['vendedores'] / 100)
    df["lucro"] = df["faturamento"] - df["total_despesas"]
    df["classificacao"] = np.where(df['lucro'] > 0, 'Lucrativa', 'Prejuízo')
    return df

# Configuração inicial
st.title("Jogo de Empresas")
st.sidebar.header("Configurações do Mercado")

# Entrada de sazonalidade
sazonalidade = st.sidebar.slider("Sazonalidade do Mercado", 1, 10, 5)

# Geração de evento aleatório
eventos = [
    {"evento": "Alta demanda sazonal", "impacto": 1.2},
    {"evento": "Queda no consumo", "impacto": 0.8},
    {"evento": "Nova tecnologia reduz custos", "impacto": 0.9},
    {"evento": "Concorrente agressivo aumenta propaganda", "impacto": 0.7},
]
evento_atual = random.choice(eventos)
st.sidebar.write(f"**Evento Atual:** {evento_atual['evento']} (Impacto: {evento_atual['impacto']})")

# Tabela para entrada de dados
st.header("Dados das Empresas")
st.write("Preencha as informações abaixo para cada empresa participante:")

# Entrada de empresas
empresas = []
empresa_count = st.number_input("Quantas empresas deseja gerenciar?", min_value=1, max_value=10, step=1)

for i in range(empresa_count):
    st.subheader(f"Empresa {i + 1}")
    empresa_nome = st.text_input(f"Nome da Empresa {i + 1}", key=f"empresa_{i}")
    preco = st.number_input(f"Preço do Produto (Empresa {i + 1})", min_value=1.0, key=f"preco_{i}")
    quantidade = st.number_input(f"Quantidade Produzida (Empresa {i + 1})", min_value=1, key=f"quantidade_{i}")
    propaganda = st.number_input(f"Gasto com Propaganda (Empresa {i + 1})", min_value=0, key=f"propaganda_{i}")
    vendedores = st.number_input(f"Número de Vendedores (Empresa {i + 1})", min_value=0, key=f"vendedores_{i}")
    
    empresas.append({
        "empresa": empresa_nome,
        "preco": preco,
        "quantidade": quantidade,
        "propaganda": propaganda,
        "vendedores": vendedores,
    })

# Convertendo para DataFrame
df = pd.DataFrame(empresas)

if not df.empty:
    # Calcular métricas
    df = calcular_custos_e_faturamento(df, sazonalidade, evento_atual["impacto"])
    
    # Exibição de resultados
    st.header("Resultados")
    st.write("Relatório de Desempenho por Empresa")
    st.dataframe(df[["empresa", "faturamento", "lucro", "classificacao"]])
    
    st.write("Detalhes de Custos e Faturamento")
    st.dataframe(df[["empresa", "total_despesas", "faturamento", "salario_vendedores", "hora_extra", "estocagem"]])
    
    # Gráficos
    st.subheader("Análise Gráfica")
    st.bar_chart(df.set_index("empresa")[["lucro", "faturamento"]])
